# 📝 Topic: Dockerfile Basic Syntax & Best Practices

## 1. 核心觀念：The Build Script
Dockerfile 是自動化建置的腳本。對於 C# 開發者來說，你可以把它想像成 **`.csproj` 檔案 + `Program.cs` 的混合體**。

### Analogy: The C# Project
* **`FROM`** = **`<TargetFramework>`** 或 **Base Class**。
    * 決定了你的基礎環境（是 .NET Core 還是 Framework？是 Ubuntu 還是 Alpine？）。這直接決定了你接下來能用哪個 Package Manager (`apt`, `yum`, `apk`)。
* **`ENV`** = **`appsettings.json`** 或 **`Global Constants`**。
    * 設定全域變數，無論是在 Build 階段還是 Runtime 階段都能讀取。
* **`WORKDIR`** = **Project Folder**。
    * 設定當前的工作目錄，之後的指令都在這個資料夾下執行。
* **`RUN`** = **Pre-build Events**。
    * 在「編譯」階段執行的指令（安裝套件、複製檔案）。只會執行一次，結果會被燒錄進 Image。
* **`CMD`** = **`static void Main()` (Constructor)**。
    * 當 `new Container()` 被建立起來時，第一個執行的啟動指令。

---

## 2. 語法詳解 (Syntax Breakdown)

### 2.1 `FROM`: 選擇基底 (The Foundation)
這是 Dockerfile 的第一行。
```dockerfile
# 選擇 Ubuntu，代表後續要用 apt-get
FROM ubuntu:22.04
```
* **DevOps Note:** 選擇 `alpine` (極簡版 Linux) 雖然體積小，但它是用 `apk` 安裝，且 C 語言函式庫 (musl) 與標準版 (glibc) 不同，對 Python/C# 專案有時會有相容性坑。

### 2.2 `ENV`: 環境變數 (Global Config)
將 Key/Value 寫入環境。
```dockerfile
# 設定 App 的模式
ENV APP_ENV=production
ENV LOG_PATH=/var/log/myapp
```
* **作用:** 這些變數會持續存在於 OS 中，你的程式碼可以用 `os.getenv('APP_ENV')` 或 `Environment.GetEnvironmentVariable("APP_ENV")` 讀取。

### 2.3 `WORKDIR`: 指定目錄 (The Context)
```dockerfile
# 之後的指令都在 /app 底下執行
WORKDIR /app
```

### 2.4 `RUN` & The `&&` Strategy: 建立圖層 (Building Layers)
這是最關鍵的部分。每一個 `RUN` 指令都會建立一個新的 **Layer**。

* **Bad Practice (多層，體積大):**
    ```dockerfile
    RUN apt-get update        # Layer 1
    RUN apt-get install curl  # Layer 2 (apt cache 殘留在 Layer 1)
    ```
* **Best Practice (單層，體積小):**
    使用 `&&` 串接指令，並在同一層清理快取。
    ```dockerfile
    RUN apt-get update && \
        apt-get install -y curl nginx && \
        rm -rf /var/lib/apt/lists/*
    # 這樣整個動作只佔用一層 Layer，且垃圾被清掉了
    ```

### 2.5 `CMD`: 啟動入口 (The Constructor)
容器啟動時執行的指令。
```dockerfile
# 語法：["執行檔", "參數1", "參數2"]
CMD ["nginx", "-g", "daemon off;"]
```
* **注意:** 每個 Dockerfile 只能有一個 `CMD`，如果寫多個，只有最後一個生效。

---

## 3. 實戰操作：Build & Log

### 3.1 指定 Dockerfile 建置
如果你的檔案不叫 `Dockerfile` (例如你有 `Dockerfile.dev`, `Dockerfile.prod`)，需要用 `-f` 指定。

```bash
# 語法：docker build -f [檔名] -t [Tag名稱] [Context路徑]
docker build -f Dockerfile.dev -t myapp:dev .
```

### 3.2 關於 Log 的處置
在 Docker 哲學中，Log 不建議寫死在容器內的檔案系統（因為容器刪除就沒了）。
* **標準作法:** 讓程式把 Log 吐到 **STDOUT/STDERR** (標準輸出)。
* **Docker 處理:** Docker Daemon 會自動攔截這些輸出，你可以用 `docker logs [container_id]` 查看。
* **如果必須存檔:** 配合 `VOLUME` 指令，將容器內的 `/var/log/myapp` 掛載到宿主機，避免資料遺失。

```dockerfile
# 宣告這個路徑是掛載點 (提示用，實際掛載還是在 docker run 指定)
VOLUME /var/log/myapp
```

## 4. 進階管理：發布與版本控制 (Publishing & Versioning)

### 4.1 Repository 命名鐵律
上傳 Docker Hub 前，Image 名稱必須嚴格遵守格式，否則會 `Access Denied`。

* **核心公式:** `[DockerHub帳號] / [專案名稱] : [版本標籤]`
* **規則:**
    1.  **擁有權宣告:** 開頭必須是你的 ID (例如 `j4503523`)。
    2.  **全小寫定律:** 名稱不能有大寫 (`JohnLo` ❌ -> `johnlo` ✅)。
    3.  **避免底線:** 建議用連字號 (`-`) 代替底線 (`_`)。

### 4.2 Tag 的本質 (The Reference)
`docker tag` **不是複製檔案**，而是 **「建立別名」**。
* **Analogy (C#):** 就像 `var alias = originalObject;`。兩個變數指向 Heap 中的同一個記憶體位置 (Image ID)。
* **硬碟消耗:** 0 MB。

### 4.3 自訂版本號 (Escaping :latest)
如果你在 Tag 時沒指定版本，Docker 會預設補上 `:latest`。

* **❌ 錯誤 (隱式):**
    ```bash
    docker image tag assignment_1 j4503523/app
    # 結果 -> j4503523/app:latest
    ```
* **✅ 正確 (顯式):**
    ```bash
    docker image tag assignment_1 j4503523/app:v1
    # 結果 -> j4503523/app:v1 (latest 不會被覆蓋)
    ```
* **DevOps Workflow:** 通常會同時 Push 兩個標籤：`:v1` (存檔用) 和 `:latest` (方便下載用)。

### 4.4 刪除邏輯 (Delete Name vs. ID)
當你想清理 Image 時，參數不同，行為也不同。

* **指名道姓 (Delete by Name/Tag):** `docker image rm j4503523/app:v1`
    * **行為:** 撕掉標籤。如果是最後一張標籤，才會刪除檔案。**(推薦作法)**
* **查號碼刪除 (Delete by ID):** `docker image rm 930acd...`
    * **行為:** 試圖銷毀檔案。
    * **衝突 (Conflict):** 如果該 ID 身上有多張標籤 (例如同時是 `v1` 和 `latest`)，Docker 會報錯阻止，怕你誤刪。

### 4.5 標準發布流程 (Workflow Summary)
1.  **Login:** `docker login`
2.  **Tag:** `docker image tag [來源] [帳號]/[Repo]:[Ver]`
3.  **Push:** `docker image push [帳號]/[Repo]:[Ver]`