# Docker 學習筆記：container lifecycle

## 1. 容器生命週期：Run vs Start

這兩個指令常常混淆，區別在於是否產生新的實體。


### `docker run` (建立並執行)
* **定義**：從無到有，開啟一個**全新的** Container。
* **底層運作流程**：
    1.  **Check**：找 Local 有沒有指定的 Image (或是指定 Tag 版本)。
    2.  **Pull**：如果沒有，從 Docker Hub (或指定 Registry) 下載。
    3.  **Create**：建立一個新的 Container 寫入層 (Writeable Layer)。
    4.  **Start**：啟動該 Container 的主程序 (Main Process)。
* **指令範例**：
    ```bash
    # -d: 背景執行, --name: 指定名字
    docker run -d --name my-web nginx
    ```

### `docker start` (喚醒)
* **定義**：開啟一個**已經存在** (狀態為 Exited/Stopped) 的 Container。
* **特性**：不會產生新資料，原本的設定、檔案與 IP 通常會保留（除非 IP 被別人搶走）。
* **指令範例**：
    ```bash
    docker start my-web
    ```

## 2. 互動模式與除錯 (Interactive Mode)

### `docker run -it` (啟動時進入)
* **參數意義**：
    * `-i` (Interactive): 保持標準輸入 (STDIN) 開啟，讓你可以輸入指令。
    * `-t` (TTY): 分配一個虛擬終端機 (Pseudo-TTY)，讓你看得到漂亮的排版。
* **權限狀態**：
    * 預設會顯示 `root@container_ID`。
    * **注意**：這代表你是 **Container 內的 Root**，雖然權限很大，但仍受限於 Docker 的隔離機制 (Namespace/Cgroups)，並非 Host 系統的真 Root。
* **Exit 的後果**：
    * 因為 Bash 是這個 Container 的「PID 1 (主程序)」。
    * 當你輸入 `exit` 結束 Bash，PID 1 結束，Container 就會判定任務完成而 **自動停止 (Stop)**。
    * *Tips: 若想離開但不關閉，可按 `Ctrl + P` 接著按 `Ctrl + Q` (Detach)。*
* **常用搭配：自動移除 (`--rm`)**：
    * **問題**：若不加此參數，每次測試完 `exit` 離開後，系統會殘留一個狀態為 `Exited` 的廢棄容器，佔用硬碟空間。
    * **解法**：加上 `--rm`，讓容器在停止 (Stop) 的瞬間 **自動刪除 (Remove)**。
    * **範例**：`docker run --rm -it ubuntu bash` (用完即丟，不留垃圾)。

### `docker exec -it` (執行中插入)
* **定義**：在一個 **「已經在執行中」** 的 Container 裡，額外開啟一個新的 Process (例如 Bash)。
* **Exit 的後果**：
    * 輸入 `exit` 只會關閉這個額外開啟的 Bash。
    * 原本的主程序 (PID 1) 仍在背景執行，所以 Container **不會停止**。
    * *這最適合用來進入伺服器修改設定檔或除錯。*
* **注意**：`docker exec` 指令 **不支援** `--rm` 參數，因為它只是插入程序，不負責管理容器的生命週期。

---

## 3. 容器監控 (Observability)

當 Container 跑起來後，我們需要指令來確認它的健康狀態。

| 指令 | 功能 | 備註 |
| :--- | :--- | :--- |
| **`docker container top`** | 查看 Container **內部** 的 Process | 類似 Linux 的 `ps` 指令，可檢查有無殭屍程序。 |
| **`docker container inspect`** | 查看 Container 的**詳細配置** (Config) | 顯示 JSON 格式，包含 IP、Env、Mounts、Ports 等細節。 |
| **`docker container stats`** | 即時監視資源使用量 | 類似工作管理員，即時顯示 CPU %、Memory、Network I/O。 |

---
