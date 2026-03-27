# 📝 Topic: Docker Core & Troubleshooting Strategies

## 1. 核心觀念：The Docker Golden Triangle
對於 C# 開發者來說，Docker 的三個階段完全對應軟體開發週期 (SDLC)。

### 1.1 Dockerfile (The Source Code)
* **類比 (C#):** 這就是你的 `.cs` 原始碼檔案。
* **定義:** 純文字檔案，描述如何構建軟體。
* **指令:** 就像你寫 Code 一樣，這裡包含 `FROM`, `RUN`, `COPY` 等指令。

### 1.2 Image (The Compiled Class/Assembly)
* **類比 (C#):** 這就是編譯後的 `.dll` 或 `.exe` (Class Definition)。
* **特性:**
    * **Immutable (不可變):** 就像你不會在 Runtime 去改寫編譯好的 `.dll` 內容一樣，Image 建好後就是唯讀的 (Read-only)。
    * **Layered (分層):** 像繼承鏈一樣堆疊。
* **作用:** 它是靜態的「模具」。

### 1.3 Container (The Runtime Instance)
* **類比 (C#):** 這就是 `var app = new App();` 產生出的 **Instance (物件實例)**。
* **特性:**
    * **Ephemeral (短暫):** 就像記憶體中的物件，程式結束 (Stop/Remove) 後，記憶體釋放，資料消失 (除非有掛載 Volume 持久化)。
    * **Writable Layer:** Docker 在唯讀的 Image 上面加了一層「可寫層」。這就像物件的 `Properties` (屬性)，每個 Instance 可以有不同的狀態，但不會影響原本的 Class 定義。

---

## 2. SRE 實戰除錯：依賴腐敗 (Dependency Rot)
**情境:** 你接手維護一個舊專案，CI/CD Pipeline 突然紅燈炸裂。錯誤顯示 `yum update` 失敗。這是典型的「上游依賴失效」。

### 案例：CentOS 7 EOL (End of Life)
CentOS 7 已停止維護，官方移除軟體源，導致 Docker build 失敗。我們需要用 `sed` (Stream Editor) 修改設定檔指向封存庫 (Vault)。

#### 🛠️ Linux Text Processing (CKA 重點)
為什麼用 `sed`？因為在 Docker build 過程中是無互動模式 (Non-interactive)，你不能打開 Notepad 或 Vim 去改檔。

```bash
# 語法 breakdown:
# sed -i 's/舊字串/新字串/g' 檔案路徑
# -i (in-place): 直接修改檔案內容，而不是只輸出到螢幕 (這是 C# string.Replace() vs StringBuilder 的差別)
# s (substitute): 替換操作
# g (global): 整行全換，不只換第一個

# 1. 註解掉失效的 mirrorlist
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Base.repo

# 2. 將 baseurl 指向 vault (注意分隔符號改用 | 以避免 url 中的 / 衝突)
sed -i 's|#baseurl=[http://mirror.centos.org](http://mirror.centos.org)|baseurl=[http://vault.centos.org](http://vault.centos.org)|g' /etc/yum.repos.d/CentOS-Base.repo

# 3. 重建快取
yum makecache
```

---

## 3. SRE 實戰除錯：身分錯亂 (Identity Crisis)
**情境 (Option 2):** 線上服務異常，你需要緊急進入容器除錯 (Debug)。你執行 `docker run -it nginx:latest` 卻發現版本跟線上的不一樣！或者指令報錯說找不到 Image。

### 問題：Tag vs Image ID
* **Tag (`latest`, `v1.0`):** 就像 C# 專案中的 **Alias** 或 **Symlink**。它只是一個「指標」，隨時可能被改掉指向別的版本。
* **Image ID (`ca871...`):** 就像 **GUID** 或記憶體位址。這是絕對唯一且真實的物理檔案。

### 解決方案：Bypass the Tag
當 Docker 前端 (CLI) 與後端 (Daemon) 的 Metadata 不同步，或者 Tag 被覆蓋時，**直接使用 ID** 是最保險的 SRE 手段。

```bash
# 1. 查詢真實 ID (查出 GUID)
docker image ls
# Output:
# REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
# nginx        latest    ca871a86d45a   2 days ago     187MB

# 2. 針對 ID 操作 (不透過 Alias)
# 用於查看這個特定版本的構建歷史，找出是哪一行指令搞壞了環境
docker history ca871a86d45a

# 3. 強制刪除頑固 Image (如果 Tag 刪不掉)
docker rmi -f ca871a86d45a
```

### 💡 DevOps Takeaway
在正式生產環境 (Production) 的 Kubernetes YAML 中，我們通常**避免使用 `latest` tag**，而是使用具體的版號 (e.g., `v1.2.3`) 甚至是 SHA256 Digest (`image@sha256:...`)，以確保 "Immutable Infrastructure" —— 昨天測過的 Code 跟今天跑的 Code 絕對是同一份 Assembly。

## 4. 進階觀念：Docker Internals - Layers & Architecture
Docker 的儲存設計非常聰明，它不儲存重複的檔案，而是像「堆積木」一樣共用組件。這也是它比虛擬機 (VM) 輕量的核心原因。

### 4.1 Official Images (The Trusted Source)
* **觀念：** 在 Docker Hub 上，只有標示 **"Official Image"** 的才是官方維護的版本 (如 `python`, `nginx`, `ubuntu`)。其他的可能是路人甲上傳的，可能有資安風險 (挖礦程式、後門)。
* **類比 (Git):** 這就像 GitHub 上專案的 **Verified Release Tag**。你不會去下載隨便一個 Fork 分支的 Code 來用，你會用官方簽署過的那一版。

### 4.2 Image Layers (Union File System)
* **觀念：** Image 不是一個單一大檔案，而是由一連串的 **Layers (層)** 組成的。
    * 每一層代表檔案系統的一次變更 (Add, Delete, Modify)。
    * 每一層都有唯一的 SHA256 ID。
    * **De-duplication (去重):** 如果你下載了 5 個不同的 App，但它們都基於 `ubuntu:20.04`，那麼底層的 Ubuntu Layer 只會下載一次，大家共用。
* **類比 (Git):** 這完全就是 **Git Commits**。
    * 當你 `git pull` 時，Git 不會把整個專案重新抓下來，它只會抓你**缺少的那些 Commits (Deltas)**。
    * Docker Pull 也是一樣，只下載本機 Cache 沒有的 Layer SHA。

### 4.3 Container as a Stack
* **觀念：** 當 Container 啟動時，Docker 只是在唯讀的 Image Layer 堆疊最上面，再蓋上一層 **Read-Write Layer**。
* **類比 (Git):**
    * **Image Layers** = `git log` 中的歷史 Commits (唯讀，不可改)。
    * **Container Layer** = 你的 **Working Directory (工作區)**。你在這裡新增/刪除檔案，還沒 Commit 之前，都不會影響到底下的歷史紀錄。

---

## 5. 深入指令：History & Missing Layers
當你查看 Image 的製作歷程時，會看到特殊的標記，這經常讓新手困惑。

### 指令
```bash
docker history [Image_Name_or_ID]
```

### 關鍵解讀：`<missing>` 是什麼？
在執行 `docker history` 時，你常會看到 Image ID 欄位顯示 `<missing>`。
* **原因：** 這不是 Error。這是因為現代 Docker 使用了 **BuildKit** 建置引擎。為了效能最佳化，它只保留最終結果與必要的 Cache，**中間過程的暫存 Image ID 不會被保留在本地資料庫中**。
* **意義：** 這些 Layer 的資料 (檔案系統變更) 實際上是存在的，只是沒有給它一個獨立的 Image ID 標籤而已。

---

## 6. SRE 實戰除錯：Configuration Audit (Inspect)
**情境:** 開發者回報：「我的 App 在 Container 裡讀不到設定檔！」或者「Port 沒開，連不進去！」。我們需要像驗屍官一樣，對比「出廠設定」與「現場狀況」。

### 工具：`docker inspect` (透視鏡)
這會回傳一大串 JSON 格式的詳細資訊 (Metadata)。在 CKA 考試中，這是檢查 ConfigMap 有沒有掛載成功的關鍵指令。

#### Step 1: 查 Image (出廠預設值 - Should be)
查看 Dockerfile 當初是怎麼寫的，確認 Base Image 的設定。
```bash
# 查看 Image 的預設設定 (Expose Port, Entrypoint, Env Vars)
docker image inspect nginx:latest
```

#### Step 2: 查 Container (實際執行值 - As is)
查看運作中的 Container 到底用了什麼設定 (可能被 `docker run` 的參數覆蓋了)。
```bash
# 查看 Container 的 Runtime 設定 (IP Address, Mounts, 實際生效的 Env)
docker container inspect my-nginx-app
```

### 實戰技巧：用 grep 過濾 JSON
JSON 輸出往往長達數百行，我們通常配合 `grep` 快速過濾重點欄位。

```bash
# 1. 檢查 Image 預設對外開放什麼 Port
docker image inspect nginx:latest | grep -i "ExposedPorts" -A 5
# (-A 5: After 5 lines, 顯示關鍵字後 5 行，讓你看清楚 JSON 結構)

# 2. 檢查 Container 實際掛載了哪個 Volume (查路徑錯誤常用)
docker container inspect my-db-container | grep -i "Mounts" -A 10
```

### 💡 DevOps Takeaway
**Image Inspect** 顯示的是靜態的 Class Definition。
**Container Inspect** 顯示的是 Runtime 的 Instance State。
當兩者不一致時（例如 Image 說 Port 80，Container 卻沒開），通常就是啟動指令 (`docker run` 或 K8s YAML) 覆蓋了設定。

## 7. Linux 文字處理實戰：Grep 的 "OR" 邏輯
在 CKA 考試或除錯時，我們常需要一次檢查多個關鍵字（例如同時看 Mounts 和 Networks）。Linux 雖然沒有 SQL 的 `WHERE ... OR ...` 語法，但我們可以透過 **Extended Grep (`grep -E`)** 達成。

### 7.1 語法觀念 (SQL Style)
* **指令：** `grep -E "Pattern1|Pattern2"`
* **參數：** `-E` 代表 Extended Regular Expression (擴充正規表示式)。
* **符號：** `|` (Pipe) 在這裡代表邏輯上的 **"OR"**。
* **類比：** 就像 SQL 的 `WHERE column LIKE '%Pattern1%' OR column LIKE '%Pattern2%'`。

### 7.2 實戰範例
**情境：** 你想一次檢視容器的「掛載點」與「網路設定」，不想打兩次指令。

```bash
# 語法：
# grep -E "關鍵字1|關鍵字2" -A [顯示行數]

# 範例：
docker container inspect my-db-container | grep -E "Mounts|Networks" -A 10
```

**解析：**
1.  `docker container inspect ...`：吐出巨量 JSON。
2.  `grep -E "Mounts|Networks"`：搜尋包含 "Mounts" **或者** "Networks" 的行。
3.  `-A 10`：印出比對到的那一行，以及**後面的 10 行** (After 10 lines)。這對於看 JSON 結構非常重要，因為詳細資訊都在標題的下方。