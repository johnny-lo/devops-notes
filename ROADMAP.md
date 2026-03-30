# DevOps 學習路線圖 & 進度追蹤

> 給 Claude Code 看的：這份文件記錄我的學習路線與進度，請根據這份文件了解我目前的程度，接續教學。
>
> 資料來源：roadmap.sh、CNCF、Coursera、GitHub milanm/DevOps-Roadmap、DEV Community、Spacelift、Grafana Labs 等（2025-2026）
>
> 最後更新：2026-03-28

---

## 進度總覽

| 階段  | 主題                       | 建議時間   | 狀態                |
| --- | ------------------------ | ------ | ----------------- |
| 00  | Linux 系統管理               | 3-4 週  | 🟡 進行中            |
| 01  | 網路與安全基礎                  | 2-3 週  | ⬜ 待開始             |
| 02  | 程式語言（Python 為主）          | 3-4 週  | ⬜ 待開始             |
| 03  | Shell Scripting          | 2-3 週  | ⬜ 待開始             |
| 04  | Git 進階                   | 1-2 週  | ⬜ 待開始             |
| 05  | Docker 容器化               | 3-4 週  | 🟡 有實戰基礎          |
| 06  | CI/CD                    | 2-3 週  | 🟡 有 GitLab CI 經驗 |
| 07  | 雲端平台（AWS）                | 4-6 週  | ⬜ 待開始             |
| 08  | IaC（Terraform + Ansible） | 3-4 週  | ⬜ 待開始             |
| 09  | Kubernetes               | 4-6 週  | ⬜ 待開始             |
| 10  | 監控與可觀測性                  | 3-4 週  | ⬜ 待開始             |
| 11  | GitOps                   | 2-3 週  | ⬜ 待開始             |
| 12  | DevSecOps / 安全           | 2-3 週  | ⬜ 待開始             |
| 13  | 服務網格（進階）                 | 2-3 週  | ⬜ 進階選修            |
| 14  | 平台工程與新興趨勢（進階）            | 持續     | ⬜ 進階選修            |
| --  | Soft Skills              | 貫穿所有階段 | 🔄 持續培養           |

**預估總時間：約 12-18 個月**

---

## 階段 00：Linux 系統管理（建議 3-4 週）

> Linux 是幾乎所有 DevOps 工具和伺服器的底層基礎，不懂 Linux 等於蓋房子沒打地基。

### 0.1 檔案系統與基本操作
- [x] 目錄結構理解（`/etc`, `/var`, `/home`, `/usr`, `/tmp`）（2026-03-23）
- [x] 檔案操作：`ls`, `cd`, `mkdir`, `cp`, `mv`, `rm`（2026-03-23）
- [x] 檔案內容查看：`head`（2026-03-23）
- [x] 路徑觀念：絕對路徑、相對路徑、`~`（2026-03-23）
- [x] 隱藏檔與常用旗標 `-h`, `-s`, `-l`, `-a`, `-R`（2026-03-23）
- [ ] `cat`, `less`, `tail`, `wc`
- [ ] `find`, `locate` 搜尋檔案
- [ ] `ln` 硬連結與軟連結
- [ ] `tar`, `gzip`, `zip`, `unzip` 壓縮與解壓

### 0.2 使用者與權限管理
- [ ] 使用者管理：`useradd`, `usermod`, `userdel`, `passwd`
- [ ] 群組管理：`groupadd`, `groups`
- [ ] 檔案權限：`chmod`（數字與符號模式）, `chown`, `chgrp`
- [ ] 特殊權限：SUID, SGID, Sticky Bit
- [ ] `sudo` 與 `/etc/sudoers`

### 0.3 文字處理
- [ ] `grep`（正規表達式搜尋）
- [ ] `sed`（串流編輯器）
- [ ] `awk`（文字處理語言）
- [ ] 管線 `|` 與重導向 `>`, `>>`, `2>&1`
- [ ] `sort`, `uniq`, `cut`, `tr`, `xargs`

### 0.4 程序管理
- [ ] `ps`, `top`, `htop`
- [ ] `kill`, `killall`, `pkill`
- [ ] 前景與背景執行：`&`, `fg`, `bg`, `jobs`, `nohup`
- [ ] `systemctl`（服務管理）
- [ ] `journalctl`（日誌查看）

### 0.5 系統管理進階（★ 常被忽略但重要）
- [ ] systemd 深入：撰寫自定義 `.service` 檔、timer units（取代 cron）
- [ ] 日誌管理：`logrotate` 配置、`rsyslog`
- [ ] 效能調校：`vmstat`, `iostat`, `sar`, `strace`；理解 load average、swap、OOM killer
- [ ] 儲存管理：LVM、磁碟分割、`lsblk`, `mount`

### 📚 學習資源
- [Linux Commands Handbook](https://www.freecodecamp.org/news/the-linux-commands-handbook/)（freeCodeCamp）
- [Ubuntu 官方教程](https://ubuntu.com/tutorials)
- GNU Bash Reference Manual

### 💪 實戰練習
- [ ] 寫一個系統健康檢查腳本（CPU、記憶體、磁碟、程序數）
- [ ] 建立自定義 systemd service 管理一個小程式
- [ ] 設定 logrotate 管理應用程式日誌
- [ ] 使用 `strace` 追蹤一個慢速指令，找出瓶頸

---

## 階段 01：網路與安全基礎（建議 2-3 週）

> 容器、雲端、CI/CD 全部跑在網路上，網路知識是排錯（troubleshooting）的關鍵能力。

### 1.1 網路概念
- [ ] TCP/IP 基礎、OSI 模型
- [ ] IP 位址、子網遮罩（CIDR 表示法）、DNS、DHCP
- [ ] 常見埠號（22, 80, 443, 3306, 5432, 6379）
- [ ] HTTP/HTTPS 協定、TLS/SSL 基礎

### 1.2 網路工具
- [ ] `ping`, `traceroute`, `mtr`
- [ ] `dig`, `nslookup` — DNS 查詢
- [ ] `curl`, `wget` — HTTP 請求
- [ ] `ss`, `netstat` — 連線狀態
- [ ] `ip addr`, `ip route` — 網路介面與路由
- [ ] `tcpdump` — 封包擷取（進階）

### 1.3 防火牆
- [ ] `ufw`（Ubuntu 防火牆，入門首選）
- [ ] `iptables` / `nftables` 基礎概念
- [ ] 開放/關閉 port、白名單設定

### 1.4 SSH
- [ ] `ssh` 連線遠端主機
- [ ] SSH key 產生與管理（`ssh-keygen`, `ssh-copy-id`）
- [ ] SSH config 設定（`~/.ssh/config`）
- [ ] `scp`, `rsync` 遠端傳檔
- [ ] SSH tunneling（port forwarding）

### 📚 學習資源
- [How DNS Works](https://howdns.works/)（互動式）
- [How HTTPS Works](https://howhttps.works/)（互動式）
- Cloudflare Learning Center — OSI Model Explained

### 💪 實戰練習
- [ ] 用 `ufw` 設定只允許 SSH(22) 和 HTTP(80/443) 流量
- [ ] 用 SSH key 設定免密碼登入 + 配置 `~/.ssh/config`
- [ ] 用 `dig` 追蹤一個域名的 DNS 解析過程
- [ ] 用 `tcpdump` 抓封包分析 HTTP 請求

---

## 階段 02：程式語言（建議 3-4 週）

> DevOps 工程師需要能寫程式解決自動化問題、開發內部工具、和開發團隊有效溝通。

### 2.1 Python（首選，自動化/API 互動/Ansible 插件）
- [ ] 基本語法：變數、資料型別、控制流程
- [ ] 函數、模組、套件管理（pip, venv）
- [ ] 檔案操作、JSON/YAML 讀寫
- [ ] 使用 `requests` 與 REST API 互動
- [ ] 錯誤處理（try/except）
- [ ] 基礎 OOP 概念

### 2.2 Go（選修，基礎設施工具語言）
- [ ] Go 基礎語法
- [ ] 寫簡單的 CLI 工具

> 💡 2026 建議：先精通 Python，Go 等有餘力再學。Docker、K8s、Terraform 都用 Go 寫的，懂 Go 有助於理解和貢獻這些工具。

### 📚 學習資源
- [Automate the Boring Stuff with Python](https://automatetheboringstuff.com/)（免費線上書）
- [Go by Example](https://gobyexample.com/)
- [Learn Go with Tests](https://quii.gitbook.io/learn-go-with-tests/)

### 💪 實戰練習
- [ ] 用 Python 寫一個自動化腳本查詢 GitHub API
- [ ] 用 Python 解析 JSON/YAML 設定檔
- [ ] 用 Python 寫一個 AWS/GCP 資源清理腳本

---

## 階段 03：Shell Scripting（建議 2-3 週）

> Shell script 是日常系統管理自動化的最快途徑。

### 3.1 Bash 基礎
- [ ] 變數、環境變數、`export`
- [ ] 條件判斷：`if`, `case`
- [ ] 迴圈：`for`, `while`, `until`
- [ ] 函數定義與呼叫

### 3.2 實用技巧
- [ ] Exit code 與 `$?`
- [ ] 命令替換 `$()`
- [ ] 字串操作與參數展開
- [ ] `trap` 訊號處理
- [ ] `cron` 排程任務（`crontab -e`）
- [ ] `set -euo pipefail`（安全模式）

### 3.3 實戰練習
- [ ] 自動備份腳本（含 rotation）
- [ ] 系統監控腳本（CPU、記憶體、磁碟）
- [ ] Log 分析腳本
- [ ] 自動化部署腳本

---

## 階段 04：Git 進階（建議 1-2 週）

> Git 是所有 DevOps 工作流的基石 — IaC、Pipeline as Code、GitOps 全部以 Git 為核心。

### 4.1 Git 核心操作
- [ ] `branch`, `merge`, `rebase`（理解差異）
- [ ] `stash`, `cherry-pick`
- [ ] 解決 merge conflict
- [ ] `.gitignore`
- [ ] `git bisect`（二分法找 bug）

### 4.2 協作流程
- [ ] Git Flow / GitHub Flow / Trunk-Based Development
- [ ] Pull Request 流程 + Code Review
- [ ] 設定 pre-commit hook（自動檢查程式碼品質）
- [ ] Conventional Commits 規範

### 📚 學習資源
- [Pro Git Book](https://git-scm.com/book/zh-tw/v2)（有繁體中文版！）
- [Learn Git Branching](https://learngitbranching.js.org/)（互動式，強烈推薦）

---

## 階段 05：Docker 容器化（建議 3-4 週）

> 容器是現代應用部署的基礎單位，Docker 出現在 42.77% 的 DevOps 職缺中。

### 5.1 Docker 基礎
- [x] 容器生命週期：`run`, `start`, `exec`（有筆記）
- [x] 容器監控：`top`, `inspect`, `stats`（有筆記）
- [x] Port mapping（`-p`）（有筆記）
- [ ] Volume mount（`-v`）深入
- [ ] 容器資源限制（`--memory`, `--cpus`）

### 5.2 Dockerfile
- [x] `FROM`, `RUN`, `ENV`, `WORKDIR`, `CMD`（有筆記）
- [x] Layer 觀念與最佳化（有筆記）
- [x] Image tag 與版本管理（有筆記）
- [ ] 多階段建置（multi-stage build）
- [ ] `.dockerignore`
- [ ] `ENTRYPOINT` vs `CMD` 的差別
- [ ] Health check 設定

### 5.3 Docker Compose
- [ ] `docker-compose.yml` 撰寫
- [ ] 多容器應用部署
- [ ] 網路（networks）與資料卷（volumes）
- [ ] 環境變數管理與 `.env` 檔

### 5.4 Docker 網路
- [x] Virtual Network / Bridge（有筆記）
- [x] Docker DNS 與 Service Discovery（有筆記）
- [x] `--network-alias` 與 Round-Robin（有筆記）

### 5.5 Docker 安全與進階
- [ ] 用 Trivy 掃描映像漏洞
- [ ] 非 root 使用者運行容器
- [ ] 了解替代方案：Podman（無 daemon）、containerd

### 📚 學習資源
- [Docker 官方 Getting Started](https://docs.docker.com/get-started/)
- Docker Crash Course — TechWorld with Nana（YouTube）

### 💪 實戰練習
- [ ] 將一個 web 應用容器化（Flask/Node.js）
- [ ] 寫 multi-stage Dockerfile 最佳化映像大小
- [ ] 用 Docker Compose 建立 web + PostgreSQL + Redis 環境
- [ ] 設定 health check 和 restart policy

---

## 階段 06：CI/CD 持續整合/持續部署（建議 2-3 週）

> CI/CD 是 DevOps 的心臟，讓程式碼從提交到部署全自動化。

### 6.1 概念
- [x] Pipeline 架構（test → build）（有筆記）

### 6.2 GitLab CI（已有實戰經驗 ✅）
- [x] `.gitlab-ci.yml` 撰寫（有筆記）
- [x] Windows Runner + PowerShell 踩坑（有筆記）
- [x] pip cache 加速（有筆記）
- [x] ruff linting、pip-audit 安全掃描（有筆記）
- [x] Artifacts 管理（有筆記）

### 6.3 GitHub Actions（2026 業界主流，建議補強）
- [ ] Workflow YAML 撰寫
- [ ] Trigger 條件（push, PR, schedule, workflow_dispatch）
- [ ] Job、Step、Action
- [ ] Secrets 管理
- [ ] Matrix builds（多版本測試）
- [ ] Reusable workflows

### 6.4 進階 CI/CD 實踐
- [ ] 多環境部署策略（dev / staging / production）
- [ ] 自動建置 Docker image 並推到 Registry
- [ ] 自動部署 + 手動核准機制
- [ ] Pipeline 效能最佳化（快取、並行）

---

## 階段 07：雲端平台（建議 4-6 週）

> 幾乎所有企業都在用或遷移到雲端，雲端是現代基礎設施的標準。

### 7.1 AWS 核心服務（市佔最高、職缺最多）
- [ ] IAM — 身份與存取管理
- [ ] EC2 — 虛擬機
- [ ] S3 — 物件儲存
- [ ] VPC — 虛擬私有網路（子網、安全群組、NAT）
- [ ] RDS — 關聯式資料庫
- [ ] EKS — 託管 Kubernetes
- [ ] CloudWatch — 監控與日誌
- [ ] Route 53 — DNS
- [ ] ELB — 負載平衡器
- [ ] AWS CLI 操作

### 7.2 雲端認證路線（建議按順序）
| 認證 | 準備時間 | 說明 |
|------|---------|------|
| AWS Cloud Practitioner (CLF-C02) | 2-4 週 | 基礎入門，建立雲端語言 |
| AWS Solutions Architect Associate (SAA-C03) | 8-12 週 | 最有價值的 Associate 認證 |
| AWS DevOps Engineer Professional | 3-6 個月 | 進階，需實務經驗後再考 |

> 💡 替代路線：Azure（AZ-900 → AZ-104 → AZ-400）或 GCP

### 📚 學習資源
- [AWS Free Tier](https://aws.amazon.com/free/)（12 個月免費額度）
- AWS Well-Architected Framework

### 💪 實戰練習
- [ ] 用 AWS Free Tier 部署 web 應用（EC2 + RDS + S3）
- [ ] 設定 VPC、安全群組、IAM 角色與策略
- [ ] 設定 CloudWatch 監控與告警
- [ ] 實作 S3 靜態網站 + CloudFront CDN

---

## 階段 08：IaC — Infrastructure as Code（建議 3-4 週）

> 「If it isn't in Git, it doesn't exist.」— IaC 讓基礎設施可版本控制、可重現、可自動化。

### 8.1 Terraform（業界標準）
- [ ] HCL 語法基礎
- [ ] Provider、Resource、Data Source
- [ ] 變數、輸出、模組（Modules）
- [ ] State 管理（遠端 backend：S3 + DynamoDB lock）
- [ ] `terraform plan`, `apply`, `destroy`
- [ ] Terraform Workspaces

> 💡 替代：OpenTofu（開源分支）、Pulumi（用程式語言寫 IaC）

### 8.2 Ansible（組態管理）
- [ ] Inventory、Playbook、Roles
- [ ] 模組使用（apt, service, copy, template）
- [ ] 變數與 Jinja2 模板
- [ ] Ansible Vault（加密敏感資料）

### 📚 學習資源
- [Terraform 官方教程](https://developer.hashicorp.com/terraform/tutorials)
- [Ansible 官方入門](https://docs.ansible.com/ansible/latest/getting_started/)

### 💪 實戰練習
- [ ] 用 Terraform 建立 AWS VPC + EC2 + Security Group
- [ ] 用 Ansible Playbook 自動化設定多台伺服器
- [ ] 實作 Terraform state 遠端儲存
- [ ] 用 Terraform modules 組織可重用的基礎設施程式碼

---

## 階段 09：Kubernetes 容器編排（建議 4-6 週）

> Kubernetes 是容器編排的業界標準，也是通往 GitOps、服務網格、平台工程的基礎。

### 9.1 K8s 核心概念
- [ ] Pod — 最小部署單位
- [ ] Deployment — 管理 Pod 副本與滾動更新
- [ ] Service — 服務發現與負載平衡（ClusterIP, NodePort, LoadBalancer）
- [ ] Ingress — HTTP 路由（Nginx Ingress Controller）
- [ ] Namespace — 資源隔離
- [ ] ConfigMap & Secret — 設定管理
- [ ] PersistentVolume & PersistentVolumeClaim — 儲存

### 9.2 工具
- [ ] `kubectl` — 核心 CLI 工具
- [ ] `k9s` — 互動式 TUI 管理工具
- [ ] `minikube` 或 `kind` — 本地練習環境
- [ ] `Lens` — 圖形化管理工具

### 9.3 進階
- [ ] Helm Charts — 套件管理
- [ ] Kustomize — 設定客製化
- [ ] HPA（Horizontal Pod Autoscaler）— 自動擴展
- [ ] Karpenter — 節點自動擴展（2026 趨勢）
- [ ] RBAC — 角色型存取控制
- [ ] NetworkPolicy — 網路策略

### 📚 學習資源
- [Kubernetes 官方教程](https://kubernetes.io/docs/tutorials/)
- Kubernetes Crash Course — TechWorld with Nana（YouTube）
- [Microsoft 50 Days K8s Learning Path](https://azure.microsoft.com/en-us/resources/kubernetes-learning-path/)

### 💪 實戰練習
- [ ] 用 minikube 部署多容器應用（前端 + 後端 + DB）
- [ ] 設定 Ingress Controller
- [ ] 用 ConfigMap 和 Secret 管理應用配置
- [ ] 用 Helm Chart 打包並部署應用
- [ ] 實作 HPA 自動擴展

---

## 階段 10：監控與可觀測性（建議 3-4 週）

> 可觀測性是區分初級和高級 DevOps 工程師的關鍵技能。

### 10.1 三大支柱

| 支柱 | 主流工具 | 替代 |
|------|---------|------|
| 指標（Metrics） | **Prometheus**（67% 生產環境使用） | Datadog, Zabbix |
| 日誌（Logs） | **Loki** / ELK Stack | Splunk, Graylog |
| 追蹤（Traces） | **Tempo** / Jaeger | Zipkin |

### 10.2 視覺化與告警
- [ ] Grafana — 儀表板視覺化
- [ ] Alertmanager — 告警管理
- [ ] PagerDuty / OpsGenie — 事件管理（了解即可）

### 10.3 統一遙測
- [ ] **OpenTelemetry**（CNCF 標準，41% 組織已在生產環境使用）
- [ ] Grafana Alloy（2026 新功能：統一遙測管道）

> 💡 推薦的現代可觀測性堆疊：**Prometheus + Loki + Tempo + Grafana + OpenTelemetry**

### 📚 學習資源
- [Prometheus 官方入門](https://prometheus.io/docs/prometheus/latest/getting_started/)
- [Grafana Tutorials](https://grafana.com/tutorials/)
- [OpenTelemetry 官方文件](https://opentelemetry.io/docs/)

### 💪 實戰練習
- [ ] 建立 Prometheus + Grafana 監控 Linux 主機（node_exporter）
- [ ] 建立 Grafana 儀表板（CPU、記憶體、網路、磁碟）
- [ ] 部署 Loki + Promtail 收集容器日誌
- [ ] 設定告警規則（如：CPU > 80% 時通知 Slack）
- [ ] 用 OpenTelemetry SDK 為 Python 應用加入追蹤

---

## 階段 11：GitOps（建議 2-3 週）

> GitOps 是 2026 年雲原生部署的標準做法，讓 Git 成為唯一的真相來源。

### 11.1 核心概念
- [ ] GitOps 原則：宣告式、版本控制、自動同步、自動修復
- [ ] Pull-based vs Push-based 部署

### 11.2 工具
- [ ] **ArgoCD**（有 UI，適合入門）
- [ ] Flux（更模組化、適合進階）

### 11.3 部署策略
- [ ] Canary Deployment
- [ ] Blue-Green Deployment
- [ ] Feature Flags

### 📚 學習資源
- [ArgoCD 官方文件](https://argo-cd.readthedocs.io/)

### 💪 實戰練習
- [ ] 安裝 ArgoCD 到 K8s 叢集
- [ ] 設定 Git repo 自動同步到 K8s
- [ ] 實作一次 canary deployment
- [ ] 模擬 drift 偵測：手動改 K8s 資源，觀察 ArgoCD 自動修復

---

## 階段 12：DevSecOps / 安全（建議 2-3 週）

> 安全已從「shift-left」進化到「shift-smart」— 持續、情境感知的安全貫穿整個生命週期。

### 12.1 容器安全
- [ ] **Trivy** — 映像漏洞掃描
- [ ] 非 root 容器、映像簽章（Sigstore/Cosign）
- [ ] SBOM（軟體物料清單，Syft）

### 12.2 密鑰管理
- [ ] **HashiCorp Vault** — 集中式密鑰管理
- [ ] Sealed Secrets（K8s 用）

### 12.3 程式碼安全
- [ ] SAST：SonarQube, Semgrep
- [ ] SCA：Snyk, Dependabot（自動更新依賴）
- [ ] DAST：OWASP ZAP

### 12.4 Policy as Code
- [ ] Open Policy Agent (OPA)
- [ ] K8s Admission Controller

### 📚 學習資源
- [OWASP DevSecOps Guideline](https://owasp.org/www-project-devsecops-guideline/)
- [Trivy 官方文件](https://trivy.dev/)
- [roadmap.sh/devsecops](https://roadmap.sh/devsecops)

### 💪 實戰練習
- [ ] 在 CI pipeline 中整合 Trivy 掃描
- [ ] 設定 Dependabot/Renovate 自動更新依賴
- [ ] 用 Vault 管理應用密鑰
- [ ] 用 OPA 寫 K8s policy（如：禁止以 root 運行容器）

---

## 階段 13：服務網格（進階選修，建議 2-3 週）

> 當微服務數量增加，服務間的流量管理、安全、可觀測性需要系統化解決方案。

### 13.1 工具選擇
| 場景 | 推薦 |
|------|------|
| K8s-only，追求簡單 | Linkerd |
| 混合環境（VM + K8s） | Istio |
| 追求效能 | Cilium（基於 eBPF，2026 趨勢） |

### 13.2 核心功能
- [ ] mTLS 自動加密
- [ ] 流量分割（Traffic Splitting）
- [ ] 熔斷器（Circuit Breaking）
- [ ] 可觀測性整合

---

## 階段 14：平台工程與新興趨勢（進階選修，持續學習）

> 這些是 2026 年區分初級和高級工程師的能力。

| 類別 | 工具/概念 | 說明 |
|------|----------|------|
| 平台工程 | **Backstage** | 內部開發者平台（IDP） |
| FinOps | Infracost | 基礎設施成本管理 |
| AIOps | LLM + 運維 | AI 輔助運維自動化 |
| Serverless | AWS Lambda | 無伺服器運算 |
| Crossplane | — | 透過 K8s CRD 管理雲端資源 |

---

## Soft Skills（貫穿所有階段）

> 常被忽略但極為重要的非技術能力。

| 技能 | 如何練習 |
|------|---------|
| **文件撰寫** | 每做一個專案就寫 README、runbook |
| **Incident Response** | 學習 SRE blameless postmortem 文化 |
| **溝通能力** | 練習用白話文解釋技術概念 |
| **Agile/Scrum** | 理解 sprint、standup、retrospective |

### 📚 推薦書籍
- *The DevOps Handbook*（Gene Kim 等著）— DevOps 聖經
- *The Phoenix Project* — 用小說形式理解 DevOps
- *Securing DevOps: Security in the Cloud*

---

## 2026 可以跳過的技術

- ~~Chef / Puppet~~（已被 Ansible/Terraform 取代）
- ~~自建 CI/CD 系統~~（用 GitHub Actions / GitLab CI）
- ~~手動伺服器 patching~~（用 IaC 管理）
- ~~Jenkins 深入學習~~（仍有用但偏舊，新專案不建議）
