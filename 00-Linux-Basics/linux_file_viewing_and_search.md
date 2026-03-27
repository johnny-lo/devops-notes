# 2026-03-27 Linux 學習總結：檔案檢視與搜尋

> 日期：2026-03-27
> 階段：00 Linux 基礎

---

## 今日學到的指令

### 檔案內容檢視

| 指令                | 用途                           |
| ----------------- | ---------------------------- |
| `cat <檔案>`        | 一次印出整個檔案內容（適合短檔案）            |
| `cat -n <檔案>`     | 顯示行號                         |
| `cat file1 file2` | 串接多個檔案輸出（cat = concatenate）  |
| `tail <檔案>`       | 看檔案最後 10 行                   |
| `tail -n 20 <檔案>` | 看最後 20 行                     |
| `tail -f <檔案>`    | 即時追蹤檔案變化（看 log 必備，Ctrl+C 結束） |
| `less <檔案>`       | 互動式翻頁瀏覽（適合長檔案）               |
| `wc <檔案>`         | 印出行數、單字數、位元組數                |
| `wc -l <檔案>`      | 只看行數（最常用）                    |

### `less` 內的操作

| 按鍵 | 動作 |
|------|------|
| `Space` / `f` | 下一頁 |
| `b` | 上一頁 |
| `g` | 跳到開頭 |
| `G` | 跳到結尾 |
| `/keyword` | 搜尋（`n` 找下一個） |
| `q` | 離開 |

### 檔案搜尋

| 指令 | 用途 |
|------|------|
| `find <路徑> -name "pattern"` | 依名稱找（大小寫敏感） |
| `find <路徑> -iname "pattern"` | 依名稱找（忽略大小寫） |
| `find <路徑> -type f` | 只找檔案 |
| `find <路徑> -type d` | 只找目錄 |
| `find <路徑> -mtime -1` | 最近 1 天內修改過 |
| `find <路徑> -mtime +7` | 超過 7 天沒修改 |
| `find <路徑> -size +1M` | 找超過 1MB 的檔案 |
| `find ... -exec <指令> {} \;` | 對找到的檔案執行指令 |
| `locate <關鍵字>` | 查資料庫快速搜尋（需先 `sudo updatedb`） |

### `find` vs `locate`

| | `find` | `locate` |
|---|---|---|
| 速度 | 慢（即時掃描） | 快（查資料庫） |
| 精確度 | 即時結果 | 可能過時 |
| 條件篩選 | 強（名稱、類型、大小、時間） | 弱（只能查名稱） |
| DevOps 常用度 | ★★★★★ | ★★ |

---

## 重要觀念

### Flag 的格式規則

flag 前面一定要有 `-`，且中間不能有空格：

```bash
tail -n 5 file    # ✓ 正確
tail 5 file       # ✗ "5" 被當成檔名
head - n 10       # ✗ "-" "n" "10" 被當成三個獨立參數
wc l file         # ✗ "l" 被當成檔名
```

### `find` 的萬用字元

`-name` 是精確比對，需要萬用字元 `*` 來做模糊搜尋：

```bash
find /etc -name "host"       # 只找剛好叫 host 的
find /etc -name "*host*"     # 找名稱含有 host 的（前後都可能有字）
find /etc -name "*.conf"     # 找 .conf 結尾的
```

### `-mtime` 的正負號

```bash
-mtime -1     # 「少於 1 天前」= 最近 1 天內（新的）
-mtime +7     # 「多於 7 天前」= 超過 7 天沒動（舊的）
```

### stdout vs stderr

`find` 的 `Permission denied` 是輸出到 **stderr**，不影響管線中的 **stdout** 資料流。之後學重導向（`2>/dev/null`）可以消掉這些雜訊。

### `/etc/passwd` 的格式

```
username:x:UID:GID:comment:home:shell
```

- UID 1000 起是一般使用者
- `x` 表示密碼存在 `/etc/shadow`
- shell 為 `/usr/sbin/nologin` 代表系統帳號，不能登入

---

## 管線組合技

```bash
ls /etc | wc -l                                    # 算 /etc 底下有幾個項目
find /etc -type f -name "*.conf" | wc -l           # 算 .conf 檔有幾個
find /etc -type f -name "*.conf" -exec wc -l {} \; | sort -nr | head -5
# 找出行數最多的前 5 個 .conf 檔
```

---

## 今日踩過的坑

1. `tail 5 file` → 忘了加 `-n`，`5` 被當成檔名
2. `wc l file` → 忘了加 `-`，`l` 被當成檔名
3. `head - n 10` → `-` 和 `n` 中間多了空格，被拆成三個參數
4. `find -name "host"` → 沒用萬用字元，找不到 hostname、hosts 等檔案
5. `find -mtime -7` → 正負號搞反，`-7` 是 7 天內，`+7` 才是超過 7 天
6. `sort head -5` → sort 和 head 之間忘了加 `|` 管線符號

---

## 相關連結

- [[linux_commands_basics]] — 前一次學習的基礎指令
