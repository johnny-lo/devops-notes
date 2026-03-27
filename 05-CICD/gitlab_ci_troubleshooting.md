# GitLab CI/CD Troubleshooting FAQ

> 日期：2026-03-27
> 階段：GitLab CI/CD on Windows

---

## 重點整理

- 本篇收錄在 Windows + PowerShell 環境下跑 GitLab Runner 實際踩過的坑
- 多數問題源自 **PowerShell 解析機制**與 **編碼差異**，彼此環環相扣
- 因果鏈：`<>` 解析失敗 → 環境變數為空 → pip 參數錯位（見 Q2 → Q6 → Q5）

---

## Q1. `shell cmd not found`

**錯誤訊息：**
```
ERROR: Preparation failed: shell cmd not found
```

**原因：**
GitLab Runner 預設使用 `cmd` 作為 shell executor，但在某些 Windows 環境下找不到 `cmd`。

**解法：**
修改 Runner 設定檔 `config.toml`（通常在 `gitlab-runner.exe` 同目錄下），指定使用 PowerShell：

```toml
[[runners]]
  shell = "powershell"
```

修改後需重啟 Runner。

---

## Q2. `CI_COMMIT_AUTHOR` / `CI_COMMIT_DESCRIPTION` 含 `<>` 導致 PowerShell 解析錯誤

**錯誤訊息：**
```
+ $CI_COMMIT_DESCRIPTION="`nCo-Authored-By: Claude Opus 4.6 <noreply@an ...
                                                             ~
'<' 運算子保留供未來使用
+ $CI_COMMIT_AUTHOR="Johnny <j4503523@gmail.com>"
                            ~
'<' 運算子保留供未來使用
```

**原因：**
GitLab Runner 執行 CI 腳本前，會自動生成 PowerShell 前導腳本，把所有 CI 變數注入為賦值語句。PowerShell **先解析整個腳本區塊再執行**，當變數內容含有 `<>`（如 git author email、`Co-Authored-By`），`<` 會被當成重導向運算子，導致解析失敗。

**影響範圍：**
- 解析錯誤本身是**非致命**的，CI 腳本指令仍會執行
- 但前導區塊中的環境變數賦值可能**部分失敗**，導致 `$env:` 變數為空值
- 空值可能引發連鎖問題（見 [[#Q5. `pip install` 報錯 "attempting to install a package literally named requirements.txt"|Q5]] 和 [[gitlab_ci_pipeline_guide#善用 pip cache 加速依賴安裝|pip cache 指南]]）

**解法：**
在 `.gitlab-ci.yml` 的 `variables` 區塊覆蓋可以降低發生機率，但 Runner 可能仍注入 git 原始值：

```yaml
variables:
  CI_COMMIT_AUTHOR: "CI"
  CI_COMMIT_DESCRIPTION: ""
```

更可靠的做法：**避免在 CI 腳本中用 `$env:` 引用可能受影響的變數**，改用硬編碼值。

---

## Q3. 中文字在 CI 腳本中變成亂碼導致 Job 失敗

**錯誤訊息：**
```
Write-Host 依賴安�??��?證�??? : 無法辨識...
CommandNotFoundException
```

**原因：**
`.gitlab-ci.yml` 以 UTF-8 編碼儲存，但 Windows GitLab Runner 的 PowerShell 使用系統預設編碼（Big5 / CP950）讀取腳本，中文字變亂碼導致 PowerShell 無法解析。

**解法：**
CI 腳本中的 `Write-Host`、`echo` 等輸出一律使用英文：

```yaml
# Bad
- Write-Host "依賴安裝及驗證成功"

# Good
- Write-Host "Dependencies installed and verified successfully"
```

YAML 註解使用中文不影響執行，但為了一致性也建議用英文。

---

## Q4. PyInstaller 多行指令在 YAML 中折疊出錯

**原因：**
YAML 的 `>` 折疊語法會將換行轉為空格，搭配 PowerShell 時可能產生非預期的指令拼接。

**解法：**
將 PyInstaller 指令寫成單行：

```yaml
# Bad - 多行折疊可能出問題
- >
  pyinstaller
  --name "TM_Automatic_BeadBlaster"
  --windowed
  ...

# Good - 單行指令
- pyinstaller --name "TM_Automatic_BeadBlaster" --windowed --icon "resource/leanteq_icon.ico" --add-data "resource;resource" --add-data "scripts;scripts" --noconfirm "(TM)Automatic_BeadBlaster.py"
```

---

## Q5. `pip install` 報錯 "attempting to install a package literally named requirements.txt"

**錯誤訊息：**
```
$ pip install --cache-dir "$env:PIP_CACHE_DIR" -r requirements.txt
ERROR: Could not find a version that satisfies the requirement requirements.txt
HINT: You are attempting to install a package literally named "requirements.txt"
```

**原因：**
當 `$env:PIP_CACHE_DIR` 為空（因 PowerShell 前導腳本解析失敗，見 [[#Q2. `CI_COMMIT_AUTHOR` / `CI_COMMIT_DESCRIPTION` 含 `<>` 導致 PowerShell 解析錯誤|Q2]]），pip 收到的參數變成：

```
pip install --cache-dir -r requirements.txt
            ↑ 需要一個值    ↑ 被當成 cache-dir 的值
                              ↑ 變成套件名稱 → 失敗
```

**解法：**
1. **`-r` 放在 `--cache-dir` 前面**：即使 cache-dir 值異常，`-r requirements.txt` 已被正確解析
2. **cache-dir 使用硬編碼路徑**：不依賴可能為空的環境變數

```yaml
# Bad — $env: 可能為空，--cache-dir 吃掉 -r
- pip install --cache-dir "$env:PIP_CACHE_DIR" -r requirements.txt

# Good — -r 先被解析，cache-dir 硬編碼
- pip install -r requirements.txt --cache-dir .pip_cache
```

---

## Q6. Build 產物（Artifacts）在哪裡？

Pipeline 成功後，打包好的 exe 可以在兩個地方取得：

1. **GitLab UI**：到 pipeline 的 build job 頁面，點擊右側 **Download artifacts** 下載 zip
2. **Runner 本機**：因為 Runner 跑在本機，產物直接在專案目錄下：
   ```
   dist/TM_Automatic_BeadBlaster/TM_Automatic_BeadBlaster.exe
   ```

Artifacts 預設保留 30 天（在 `.gitlab-ci.yml` 中設定 `expire_in: 30 days`）。

---

## 環境資訊

- GitLab Runner: 18.10.0
- Runner executor: shell (PowerShell)
- OS: Windows 11
- 架構: amd64

---

## 相關連結

- [[gitlab_ci_pipeline_guide]] — Pipeline 架構與實踐指南
