# GitLab CI/CD Pipeline 實踐指南

> 日期：2026-03-27
> 階段：GitLab CI/CD on Windows

---

## 重點整理

- 本篇記錄如何在 Windows GitLab Runner 上建置完整的 CI/CD pipeline
- 涵蓋 test、lint、security audit、build 四個面向
- 所有踩坑紀錄另見 [[gitlab_ci_troubleshooting]]

---

## Pipeline 架構總覽

```
test stage（三個 job 並行）        build stage
┌──────────────────┐
│  test            │  ← import 檢查 + 112 個 unit test
│  lint            │  ← ruff 程式碼品質檢查
│  security_audit  │  ← pip-audit 依賴漏洞掃描
└──────────────────┘
         ↓ 全部通過後
┌──────────────────┐
│ build_windows_exe│  ← PyInstaller 打包 exe
└──────────────────┘
```

- test stage 的三個 job 都在 `stage: test`，GitLab 會**自動並行**執行
- build stage 要等 test stage 全部通過才開始
- `lint` 和 `security_audit` 不需要建 venv，只裝單一工具，跑很快

---

## `only/except` vs `rules` 語法

GitLab 推薦使用 `rules` 取代已過時的 `only/except`，更靈活且語義更清楚：

```yaml
# Old - only/except
only:
  - main
  - merge_requests

# New - rules
rules:
  - if: $CI_PIPELINE_SOURCE == "merge_request_event"
  - if: $CI_COMMIT_BRANCH == "main"
```

---

## 善用 pip cache 加速依賴安裝

每次 CI job 都重新下載所有 pip 套件很慢。設定全域快取可以大幅加速：

```yaml
variables:
  PIP_CACHE_DIR: ".pip_cache"

cache:
  key: pip-${CI_COMMIT_REF_SLUG}
  paths:
    - .pip_cache/
```

安裝時使用 `--cache-dir`，**`-r` 要放在 `--cache-dir` 前面**：

```yaml
- pip install -r requirements.txt --cache-dir .pip_cache
```

> **注意：** 避免使用 `$env:PIP_CACHE_DIR` 引用環境變數。當 PowerShell 前導腳本解析失敗時（見 [[gitlab_ci_troubleshooting#Q2. `CI_COMMIT_AUTHOR` / `CI_COMMIT_DESCRIPTION` 含 `<>` 導致 PowerShell 解析錯誤|Troubleshooting Q2]]），環境變數可能為空值，導致 `--cache-dir` 吃掉後面的 `-r` flag。使用硬編碼相對路徑最可靠。

---

## 在 CI 中執行 Unit Test

**背景：**
原本 test stage 只用 `python -c "import ..."` 檢查套件能否 import，沒有真正跑測試。

**做法：**

```yaml
script:
  - python -c "import PyQt5; import numpy; import paho.mqtt; print('All imports OK')"
  - python -m unittest discover -s unit_test -v
  - Write-Host "All tests passed"
```

**注意事項：**
- `unittest discover -s unit_test` 會自動掃描 `unit_test/` 目錄下所有 `test_*.py`
- 測試檔案 import 的是 `main/` 下的模組，所以 `before_script` 要先裝好 `requirements.txt`
- 任何一個 test fail 都會讓整個 job 失敗，MR 無法 merge

---

## 使用 ruff 做程式碼品質檢查（Linting）

**背景：**
手動 review 很難抓到未使用的 import、無意義的 f-string 等問題。ruff 是用 Rust 寫的 Python linter，速度極快。

**CI 設定：**

```yaml
lint:
  stage: test
  tags:
    - windows
    - shell
  before_script:
    - pip install ruff --cache-dir .pip_cache
  script:
    - ruff check main/ unit_test/
```

**專案設定檔 `ruff.toml`：**

```toml
[lint]
ignore = ["F841"]  # 未使用的變數 — 部分是解構賦值刻意忽略

[lint.per-file-ignores]
"main/utils/error_handling_examples.py" = ["F821"]  # 範例檔有刻意未定義的名稱
```

**首次導入經驗：**
- 第一次跑出 53 個問題，其中 44 個可用 `ruff check --fix` 自動修復（主要是刪未使用的 import）
- 自動修復前先用 `ruff check --fix --diff` 預覽改動，確認安全後才執行
- 修完後跑一次 unit test 確認沒壞東西
- 剩餘無法自動修的用 `ruff.toml` 設定忽略

**安全性：**
- `ruff check`（只檢查）完全安全，不改任何檔案
- `ruff check --fix` 的自動修復限於刪除未使用的 import、移除多餘的 `f` prefix 等，不改邏輯
- 唯一風險：如果 import 是為了 side effect（觸發模組初始化），刪掉會壞

---

## 使用 pip-audit 掃描依賴漏洞

**背景：**
`requirements.txt` 中鎖定的套件版本可能存在已知的 CVE 漏洞。`pip-audit` 會查詢 PyPI 的漏洞資料庫進行比對。

**CI 設定：**

```yaml
security_audit:
  stage: test
  tags:
    - windows
    - shell
  before_script:
    - pip install pip-audit --cache-dir .pip_cache
  script:
    - pip-audit -r requirements.txt
```

**首次導入經驗：**
- 掃出 `pillow==12.0.0` 有 CVE-2026-25990，修復版本為 12.1.1
- 直接在 `requirements.txt` 升級版本，重跑 `pip-audit` 確認清除

**注意事項：**
- `pip-audit` 不需要安裝專案依賴，只需讀取 `requirements.txt`，所以 `before_script` 不用建 venv
- 如果掃出漏洞但暫時無法升級，可以用 `--ignore-vuln VULN_ID` 暫時跳過

---

## 相關連結

- [[gitlab_ci_troubleshooting]] — 實際踩坑 FAQ 與錯誤排除
