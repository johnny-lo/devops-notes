# DevOps 學習路線圖 & 進度追蹤

> 給 Claude Code 看的：這份文件記錄我的學習路線與進度，請根據這份文件了解我目前的程度，接續教學。

---

## 教學風格

1. **先教概念，再給練習**：每個主題先用簡短說明 + 範例講解核心觀念，然後出練習題
2. **練習只給目標，不給答案**：描述要達成的結果和可能用到的指令/flag，讓學生自己組合。卡住時給提示，不直接給完整指令
3. **錯誤是教材**：學生犯錯時，指出錯誤背後的通用規則（例如「flag 前一定要有 `-`」），幫助建立模式辨識而非只修正單一錯誤
4. **串連舊知識**：練習題盡量結合已學過的指令（如 `find` + `wc` + `head`），強化組合應用能力
5. **連結實務場景**：說明每個指令在 DevOps 工作中的典型用途（例如 `tail -f` 看 log、`find -mtime` 清舊檔），讓學習有動機
6. **不重複已會的東西**：標記 `[w]` 的項目代表有實戰經驗，教學時跳過基礎直接進階或補理論缺口

---

## 標記說明

- `[x]` 系統學習完成
- `[w]` 工作中學到，尚未系統整理
- `[ ]` 待學習

## 建議學習順序

> 00 Linux → 02 Shell → 01 網路 → 03 Git → 04 Docker 補完 → 05 CI/CD 補完 → 06 IaC → 07 K8s
>
> 理由：Linux 基礎 + Shell 是所有後續階段的底層能力，優先打底效益最大。

---

## 進度總覽

| 階段  | 主題                     | 狀態       |
| --- | ---------------------- | -------- |
| 00  | Linux 基礎               | 🟡 進行中   |
| 01  | 網路基礎                   | ⬜ 待開始    |
| 02  | Shell Scripting        | ⬜ 待開始    |
| 03  | Git 進階                 | ⬜ 待開始    |
| 04  | Docker 容器化             | 🟡 有實戰基礎 |
| 05  | CI/CD                  | 🟡 有實戰基礎 |
| 06  | IaC（Terraform/Ansible） | ⬜ 待開始    |
| 07  | Kubernetes             | ⬜ 待開始    |

---

## 階段 00：Linux 基礎（建議 2-3 週）

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

---

## 階段 01：網路基礎（建議 2 週）

### 1.1 網路概念
- [ ] TCP/IP 基礎、OSI 模型
- [ ] IP 位址、子網遮罩、DNS、DHCP
- [ ] 常見埠號（22, 80, 443, 3306, 5432, 6379）

### 1.2 網路工具
- [ ] `ping`, `traceroute`, `dig`, `nslookup`
- [ ] `curl`, `wget`
- [ ] `ss`, `netstat`
- [ ] `ip addr`, `ip route`

### 1.3 防火牆
- [ ] `ufw`（Ubuntu 防火牆）
- [ ] `iptables` 基礎概念

### 1.4 SSH
- [ ] `ssh` 連線遠端主機
- [ ] SSH key 產生與管理（`ssh-keygen`, `ssh-copy-id`）
- [ ] SSH config 設定（`~/.ssh/config`）
- [ ] `scp`, `rsync` 遠端傳檔

---

## 階段 02：Shell Scripting（建議 2-3 週）

### 2.1 Bash 基礎
- [ ] 變數、環境變數、`export`
- [ ] 條件判斷：`if`, `case`
- [ ] 迴圈：`for`, `while`, `until`
- [ ] 函數定義與呼叫

### 2.2 實用技巧
- [ ] Exit code 與 `$?`
- [ ] 命令替換 `$()`
- [ ] 字串操作與參數展開
- [ ] `trap` 訊號處理
- [ ] `cron` 排程任務（`crontab -e`）

### 2.3 實戰練習
- [ ] 自動備份腳本
- [ ] 系統監控腳本（CPU、記憶體、磁碟）
- [ ] log 分析腳本

---

## 階段 03：Git 進階（建議 1-2 週）

### 3.1 Git 核心操作
- [ ] `branch`, `merge`, `rebase`
- [ ] `stash`, `cherry-pick`
- [ ] 解決 merge conflict
- [ ] `.gitignore`

### 3.2 協作流程
- [ ] Git Flow / GitHub Flow
- [ ] Pull Request 流程
- [ ] Code Review 觀念

---

## 階段 04：Docker 容器化（建議 3-4 週）

### 4.1 Docker 基礎
- [w] 容器生命週期：`run`, `start`, `exec`（有筆記）
- [w] 容器監控：`top`, `inspect`, `stats`（有筆記）
- [w] Port mapping（`-p`）（有筆記）
- [ ] Volume mount（`-v`）深入

### 4.2 Dockerfile
- [w] `FROM`, `RUN`, `ENV`, `WORKDIR`, `CMD`（有筆記）
- [w] Layer 觀念與最佳化（有筆記）
- [w] Image tag 與版本管理（有筆記）
- [ ] 多階段建置（multi-stage build）
- [ ] `.dockerignore`

### 4.3 Docker Compose
- [ ] `docker-compose.yml` 撰寫
- [ ] 多容器應用部署
- [ ] 網路與資料卷

### 4.4 Docker 網路
- [w] Virtual Network / Bridge（有筆記）
- [w] Docker DNS 與 Service Discovery（有筆記）
- [w] `--network-alias` 與 Round-Robin（有筆記）

### 4.5 Container Registry
- [ ] Docker Hub push/pull
- [ ] Private registry（GitLab Container Registry）
- [ ] Image tagging 策略（`latest` vs semver vs commit SHA）

---

## 階段 05：CI/CD（建議 2-3 週）

### 5.1 概念
- [w] Pipeline 架構（test → build）（有筆記）
- [ ] CI/CD 設計原則（fail fast、parallel stages、cache 策略）

### 5.2 GitLab CI（工作實戰）
- [w] `.gitlab-ci.yml` 撰寫（有筆記）
- [w] Windows Runner + PowerShell 踩坑（有筆記）
- [w] pip cache 加速（有筆記）
- [w] ruff linting、pip-audit 安全掃描（有筆記）
- [w] Artifacts 管理（有筆記）

### 5.3 GitHub Actions
- [ ] Workflow YAML 撰寫
- [ ] Trigger 條件、Secrets 管理

### 5.4 實戰練習
- [ ] 自動建置 Docker image 並推到 Docker Hub
- [ ] 自動部署

---

## 階段 06：IaC — 雲端與基礎設施（建議 4+ 週）

### 6.0 雲端 CLI 入門
- [ ] 雲端帳號設定與 CLI 安裝（AWS CLI / gcloud）
- [ ] 基本操作：建立 VM、上傳檔案、查看資源

### 6.1 雲端服務（擇一）
- [ ] AWS：EC2, S3, IAM, VPC, RDS
- [ ] GCP 或 Azure 基礎

### 6.2 Terraform
- [ ] 用程式碼管理基礎設施

### 6.3 Ansible
- [ ] Playbook 撰寫

---

## 階段 07：Kubernetes（建議 4+ 週）

### 7.1 K8s 核心概念
- [ ] Pod, Deployment, Service, Ingress
- [ ] Namespace, ConfigMap, Secret
- [ ] `minikube` 或 `kind` 本地練習

### 7.2 進階
- [ ] Helm Charts
- [ ] 監控：Prometheus + Grafana
- [ ] 日誌：ELK Stack 或 Loki
