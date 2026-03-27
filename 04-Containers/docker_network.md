# Docker 學習筆記：內外網路架構

## 1. 網路架構 (Networking)

Docker 的網路設計是為了讓服務之間解耦 (Decoupling)。

### 外部溝通：Port Mapping (`-p`)
* **語法**：`-p [Host_Port]:[Container_Port]`
* **原理**：在 Host 實體機打洞，將流量轉發進 Container。
* **限制**：實體 Port 是稀缺資源（一個 Port 只能給一個服務用，會衝突）。

### 內部溝通：Virtual Network (虛擬網路)
* **原理**：Container 彼此透過 Docker 建立的虛擬交換器 (Bridge) 溝通。
* **優勢**：
    1.  **無需真實 Port**：完全不佔用 Host 的 80, 3306 等 Port，安全又乾淨。
    2.  **隔離性**：不同網路的 Container 無法互通，增加安全性。
    3.  **多對多**：一個 Container 可以同時加入多張虛擬網卡 (一張對外 Web，一張對內 DB)。

---

### ⭐ 關鍵觀念：為何不能依賴 IP？ (DNS 機制)

這是新手最常踩的坑，請務必理解 **Docker DNS** 的重要性。

#### 1. IP 是動態的 (Ephemeral)
> **問題**：Container 每次重啟 (`stop` -> `start`) 或重建 (`rm` -> `run`)，Docker 分配的內部 IP **可能會改變**。
> **後果**：如果你在程式碼裡寫死 IP (Hardcode `172.17.0.2`)，下次重開機連線就會斷掉。

#### 2. Docker DNS 解決方案
Docker 在「自訂網路 (User-defined Network)」中內建了 DNS Server。
* **自動解析**：Docker 會自動維護一張對照表，讓你直接用 **Container Name** 當作網址連線。
* **範例**：Web Server 連資料庫時，Host 填寫 `my-db` (容器名稱)，Docker 會自動幫你轉導到當下正確的 IP。

#### 3. Hostname 設定
* **預設行為**：Docker 會將 `Container Name` 自動設定為該容器的 Hostname。
* **客製化**：若有特殊需求 (例如叢集識別)，可在啟動時使用 `--hostname` 參數修改。
    ```bash
    docker run -d --name my-redis --hostname redis-node-01 redis
    ```
---

### ⭐ 關鍵觀念 2：服務發現與負載平衡 (Service Discovery)

這是 Docker 內網的進階功能，能模擬真實世界的多伺服器架構。

#### 1. 網路別名 (`--network-alias`)
* **定義**：給容器一個「群組名稱」或「職稱」。
* **與 Container Name 的差異**：
    * `--name` (容器名稱)：**唯一** (ID)，全網只能有一個 `johnny`。
    * `--network-alias` (別名)：**可重複** (Group)，多個容器可以共用同一個 `search` 別名。

#### 2. 運作原理：DNS 輪詢 (Round-Robin)

當多個容器共用同一個 Alias 時，Docker 內建的 DNS Server 會自動進行簡易負載平衡。

* **情境**：啟動 3 個容器，全部設定 `--network-alias search`。
* **行為**：當 Client 呼叫 `search` 時，Docker DNS 會輪流回傳這 3 個容器的 IP。
    * 第 1 次 Request -> 回傳 Container A 的 IP
    * 第 2 次 Request -> 回傳 Container B 的 IP
    * ... (以此類推，流量被自動分散)

#### 3. 實戰指令範例
```bash
# 1. 啟動多個服務 (模擬叢集)
# 雖然容器名字(name)不同，但它們都有同樣的別名(alias) "web-group"
docker run -d --net my-net --name web-01 --network-alias web-group nginx
docker run -d --net my-net --name web-02 --network-alias web-group nginx

# 2. 驗證負載平衡 (使用 alpine + nslookup)
# 你會看到 "web-group" 這個名字背後對應了多個 IP
docker run --rm --net my-net alpine nslookup web-group
---

### 網路管理指令
```bash
# 列出所有網路
docker network ls 

# 查看特定網路的細節 (看誰連在上面、網段是多少)
docker network inspect [network_name]

# 創建虛擬網路
# --driver 預設是 bridge (單機)，若是叢集則用 overlay
docker network create --driver bridge [network_name]

# 連接/斷開網路 (可以在 Container 執行中動態插拔網卡)
docker network connect [network_name] [container_name]
docker network disconnect [network_name] [container_name]
```

