# DevOps 學習路線圖 & 進度追蹤

> 給 Claude Code 看的：這份文件記錄我的學習路線與進度，請根據這份文件了解我目前的程度，接續教學。

---

## 進度總覽

| 階段 | 主題 | 狀態 |
|------|------|------|
| 00 | Linux 基礎 | 🟡 進行中 |
| 01 | 網路基礎 | ⬜ 待開始 |
| 02 | Shell Scripting | ⬜ 待開始 |
| 03 | Git 進階 | ⬜ 待開始 |
| 04 | Docker 容器化 | 🟡 有實戰基礎 |
| 05 | CI/CD | 🟡 有實戰基礎 |
| 06 | IaC（Terraform/Ansible） | ⬜ 待開始 |
| 07 | Kubernetes | ⬜ 待開始 |

---

## 階段 00：Linux 基礎（建議 2-3 週）

### 1.1 檔案系統與基本操作
- [x] 目錄結構理解（`/etc`, `/var`, `/home`, `/usr`, `/tmp`）（2026-03-23）
- [x] 檔案操作：`ls`, `cd`, `mkdir`, `cp`, `mv`, `rm`（2026-03-23）
- [x] 檔案內容查看：`head`（2026-03-23）
- [x] 路徑觀念：絕對路徑、相對路徑、`~`（2026-03-23）
- [x] 隱藏檔與常用旗標 `-h`, `-s`, `-l`, `-a`, `-R`（2026-03-23）
- [ ] `cat`, `less`, `tail`, `wc`
- [ ] `find`, `locate` 搜尋檔案
- [ ] `ln` 硬連結與軟連結
- [ ] `tar`, `gzip`, `zip`, `unzip` 壓縮與解壓

### 1.2 使用者與權限管理
- [ ] 使用者管理：`useradd`, `usermod`, `userdel`, `passwd`
- [ ] 群組管理：`groupadd`, `groups`
- [ ] 檔案權限：`chmod`（數字與符號模式）, `chown`, `chgrp`
- [ ] 特殊權限：SUID, SGID, Sticky Bit
- [ ] `sudo` 與 `/etc/sudoers`

### 1.3 文字處理
- [ ] `grep`（正規表達式搜尋）
- [ ] `sed`（串流編輯器）
- [ ] `awk`（文字處理語言）
- [ ] 管線 `|` 與重導向 `>`, `>>`, `2>&1`
- [ ] `sort`, `uniq`, `cut`, `tr`, `xargs`

### 1.4 程序管理
- [ ] `ps`, `top`, `htop`
- [ ] `kill`, `killall`, `pkill`
- [ ] 前景與背景執行：`&`, `fg`, `bg`, `jobs`, `nohup`
- [ ] `systemctl`（服務管理）
- [ ] `journalctl`（日誌查看）

---

## 階段 01：網路基礎（建議 2 週）

### 2.1 網路概念
- [ ] TCP/IP 基礎、OSI 模型
- [ ] IP 位址、子網遮罩、DNS、DHCP
- [ ] 常見埠號（22, 80, 443, 3306, 5432, 6379）

### 2.2 網路工具
- [ ] `ping`, `traceroute`, `dig`, `nslookup`
- [ ] `curl`, `wget`
- [ ] `ss`, `netstat`
- [ ] `ip addr`, `ip route`

### 2.3 防火牆
- [ ] `ufw`（Ubuntu 防火牆）
- [ ] `iptables` 基礎概念

### 2.4 SSH
- [ ] `ssh` 連線遠端主機
- [ ] SSH key 產生與管理（`ssh-keygen`, `ssh-copy-id`）
- [ ] SSH config 設定（`~/.ssh/config`）
- [ ] `scp`, `rsync` 遠端傳檔

---

## 階段 02：Shell Scripting（建議 2-3 週）

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

### 3.3 實戰練習
- [ ] 自動備份腳本
- [ ] 系統監控腳本（CPU、記憶體、磁碟）
- [ ] log 分析腳本

---

## 階段 03：Git 進階（建議 1-2 週）

### 5.1 Git 核心操作
- [ ] `branch`, `merge`, `rebase`
- [ ] `stash`, `cherry-pick`
- [ ] 解決 merge conflict
- [ ] `.gitignore`

### 5.2 協作流程
- [ ] Git Flow / GitHub Flow
- [ ] Pull Request 流程
- [ ] Code Review 觀念

---

## 階段 04：Docker 容器化（建議 3-4 週）

### 4.1 Docker 基礎
- [x] 容器生命週期：`run`, `start`, `exec`（有筆記）
- [x] 容器監控：`top`, `inspect`, `stats`（有筆記）
- [x] Port mapping（`-p`）（有筆記）
- [ ] Volume mount（`-v`）深入

### 4.2 Dockerfile
- [x] `FROM`, `RUN`, `ENV`, `WORKDIR`, `CMD`（有筆記）
- [x] Layer 觀念與最佳化（有筆記）
- [x] Image tag 與版本管理（有筆記）
- [ ] 多階段建置（multi-stage build）
- [ ] `.dockerignore`

### 4.3 Docker Compose
- [ ] `docker-compose.yml` 撰寫
- [ ] 多容器應用部署
- [ ] 網路與資料卷

### 4.4 Docker 網路
- [x] Virtual Network / Bridge（有筆記）
- [x] Docker DNS 與 Service Discovery（有筆記）
- [x] `--network-alias` 與 Round-Robin（有筆記）

---

## 階段 05：CI/CD（建議 2-3 週）

### 6.1 概念
- [x] Pipeline 架構（test → build）（有筆記）

### 6.2 GitLab CI（工作實戰）
- [x] `.gitlab-ci.yml` 撰寫（有筆記）
- [x] Windows Runner + PowerShell 踩坑（有筆記）
- [x] pip cache 加速（有筆記）
- [x] ruff linting、pip-audit 安全掃描（有筆記）
- [x] Artifacts 管理（有筆記）

### 6.3 GitHub Actions
- [ ] Workflow YAML 撰寫
- [ ] Trigger 條件、Secrets 管理

### 6.4 實戰練習
- [ ] 自動建置 Docker image 並推到 Docker Hub
- [ ] 自動部署

---

## 階段 06：IaC — 雲端與基礎設施（建議 4+ 週）

### 7.1 雲端入門（擇一）
- [ ] AWS：EC2, S3, IAM, VPC, RDS
- [ ] GCP 或 Azure 基礎

### 7.2 Terraform
- [ ] 用程式碼管理基礎設施

### 7.3 Ansible
- [ ] Playbook 撰寫

---

## 階段 07：Kubernetes（建議 4+ 週）

### 8.1 K8s 核心概念
- [ ] Pod, Deployment, Service, Ingress
- [ ] Namespace, ConfigMap, Secret
- [ ] `minikube` 或 `kind` 本地練習

### 8.2 進階
- [ ] Helm Charts
- [ ] 監控：Prometheus + Grafana
- [ ] 日誌：ELK Stack 或 Loki
