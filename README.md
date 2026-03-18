# 🛡️ OpenClaw 安全全景與 Agentic AI 安全實踐 (2026 版)

**版本**：1.0.0  
**最後更新**：2026-03-18  
**分類**：安全分析 | 風險評估 | 最佳實踐

---

## 📖 目錄

1. [OpenClaw 安全危機：兩週改變 AI Agent 世界](#openclaw-安全危機兩週改變-ai-agent-世界)
2. [核心漏洞分析：CVE-2026-25253 與相關風險](#核心漏洞分析-cve-2026-25253-與相關風險)
3. [Agentic AI 如何在安全環境下運作](#agentic-ai-如何在安全環境下運作)
4. [防禦性設計架構](#防禦性設計架構)
5. [你的 Mac Studio + Mac Mini 安全部署方案](#你的-mac-studio---mac-mini-安全部署方案)
6. [實戰檢查清單](#實戰檢查清單)

---

## OpenClaw 安全危機：兩週改變 AI Agent 世界

### 📅 關鍵時間線（2026 年 2 月 -3 月）

| 日期 | 事件 | 影響 | 狀態 |
|------|------|------|------|
| **2 月 13 日** | 發現 **ClawJacked** 漏洞（CVE-2026-25253） | 允許惡意網站劫持本地 AI Agent | ✅ 已修復 |
| **2 月 15 日** | 數十萬用戶暴露 OpenClaw 實例至互聯網 | 漏洞攻擊面擴大 | ⚠️ 曝光 |
| **2 月 20 日** | **發布緊急修復版本** | 關閉沙箱、需手動確認危險命令 | ✅ 已修復 |
| **3 月 1 日** | Nvidia 發布 **NemoClaw** 企業級安全版 | 內置沙箱隔離 + 隱私路由器 | ✅ 已發布 |
| **3 月 16 日** | CrowdStrike 發布 OpenClaw 安全指南 | 企業安全團隊參考文檔 | ✅ 已發布 |

### 🔥 核心問題：為什麼 OpenClaw 會被攻擊？

| 根本原因 | 說明 | 修復方案 |
|---------|------|---------|
| **默认暴露公网** | 數萬用戶將 Gateway 暴露到互聯網 | **沙箱模式 + 內網隔離** |
| **弱沙箱機制** | 可禁用沙箱，無需用戶確認 | **強制沙箱 + 確認機制** |
| **權限提升漏洞** | 惡意 Skill 可獲取 Operator 權限 | **技能審計 + 白名單** |
| **WebSocket 處理缺陷** | CVE-2026-25253：RCE 漏洞 | **WebSocket 安全驗證** |

---

## 核心漏洞分析：CVE-2026-25253 與相關風險

### 🔴 **CVE-2026-25253** (CVSS 8.8 - HIGH)

**漏洞名稱**：惡意 Skill 劫持漏洞  
**發現日期**：2026-02-13  
**影響範圍**：所有未修復版本  
**嚴重程度**：**HIGH** (CVSS v3.1: 8.8)

#### 漏洞機制

```
攻擊路徑：
1. 用戶訪問惡意網站 (openclawdir.com/skills/deeps-agnw6h)
2. 網站注入惡意 Skill 代碼
3. Skill 代碼繞過沙箱限制
4. 獲取 Gateway Operator 權限
5. 任意命令執行 + 數據洩露
```

#### 受影響組件

- **Gateway API**：未驗證 Skill 來源
- **WebSocket 處理**：未驗證權限
- **沙箱機制**：可被繞過

#### 修復方案（已發布）

```bash
# 1. 升級到最新版本的 OpenClaw
npm install -g openclaw@latest

# 2. 啟用強制沙箱模式
openclaw config set agents.defaults.sandbox.mode "require"

# 3. 啟用工具白名單
openclaw config set tools.exec.allowlist ["git", "ls", "cat"]

# 4. 限制外部連接
openclaw config set network.allowedHosts ["example.com"]
```

### 🟠 **其他已披露漏洞**

| CVE 編號 | 漏洞名稱 | CVSS | 影響 | 修復狀態 |
|---------|---------|------|------|---------|
| **CVE-2026-25253** | 惡意 Skill 劫持 | 8.8 | 完全控制 | ✅ 已修復 |
| **GHSA-g8p2-7wf7-98mq** | Token 洩露漏洞 | 9.8 | Gateway 接管 | ✅ 已修復 |
| **GHSA-943q-mwmv-hhvh** | 工具權限提升 | 7.5 | RCE 擴大 | ✅ 已修復 |
| **CVE-2026-26996** | minimatch 漏洞 | 6.5 | 依賴風險 | ⚠️ 依賴修復 |
| **CVE-2026-23745** | tar 漏洞 | 6.5 | 依賴風險 | ⚠️ 依賴修復 |

### 💡 漏洞根源分析

| 問題類型 | 根本原因 | 防禦策略 |
|---------|---------|---------|
| **外部暴露** | 默認配置允許公网訪問 | **Loopback 優先 + 白名單** |
| **弱沙箱機制** | 可動態禁用沙箱 | **強制沙箱模式 (sandbox: "require")** |
| **權限混亂** | Gateway 與 Agent 權限相同 | **最小權限 + 權限分離** |
| **技能信任** | 直接信任所有技能 | **技能審計 + 白名單機制** |

---

## Agentic AI 如何在安全環境下運作

### 什麼是 Agentic AI？

**Agentic AI** = **自動化工具調用 + 自主決策 + 環境交互**

**核心能力**：
1. **工具調用**：執行命令、讀寫文件、瀏覽網頁
2. **自主決策**：根據任務目標選擇行動路徑
3. **環境交互**：與外部服務（Telegram、Email、API）通信

### 安全環境下的運作架構

#### 🛡️ **三層防禦模型**

```
┌─────────────────────────────────────────────────────────┐
│  第一層：事前防禦 (Pre-Action)                          │
│  • 提示注入防護 (Prompt Injection Protection)           │
│  • 技能信任驗證 (Skill Trust Verification)              │
│  • 環境邊界檢查 (Environment Boundary Check)            │
└──────────────────────┬──────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────────┐
│  第二層：事中控制 (In-Action)                           │
│  • 沙箱隔離 (Sandbox Isolation)                         │
│  • 用戶確認機制 (User Confirmation)                     │
│  • 實時監控 (Real-time Monitoring)                      │
└──────────────────────┬──────────────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────────────┐
│  第三層：事後審計 (Post-Action)                         │
│  • 日誌記錄 (Audit Logging)                             │
│  • 異常檢測 (Anomaly Detection)                         │
│  • 定期回顧 (Periodic Review)                           │
└─────────────────────────────────────────────────────────┘
```

### 🔐 **關鍵安全機制**

#### 1. **沙箱隔離 (Sandbox Mode)**

**沙箱模式**：
```json5
{
  "agents": {
    "defaults": {
      "sandbox": {
        "mode": "require",           // ✅ 強制模式
        "scope": "agent"              // ✅ 作用範圍：單一 Agent
      }
    }
  }
}
```

**沙箱類型**：
| 模式 | 說明 | 適用場景 |
|------|------|---------|
| **require** | 強制沙箱隔離 | 所有生產環境 |
| **non-main** | 非關鍵操作沙箱 | 一般任務 |
| **inherit** | 繼承父進程環境 | 可信任務 |

#### 2. **用戶確認機制 (User Confirmation)**

**確認流程**：
```
AI 檢測到黃線操作 → 暫停 → 發送確認請求 → 用戶回覆 → 繼續/中止
```

**確認規則**：
- **🔴 紅線**：永遠禁止（如 `rm -rf /`）
- **🟡 黃線**：需用戶確認（如 `npm install`、`curl`）
- **🟢 綠線**：可自行執行（如 `ls`、`cat`）

**Telegram 確認示例**：
```
🟡 需要您的確認

📋 操作：執行外部命令
🔧 命令：npm install -g openclaw
📖 解釋：安裝 OpenClaw 全局工具
⚠️ 風險：低（軟件安裝）
🎯 目的：設置 OpenClaw 環境

是否批准此操作？
[✅ 批准執行]  [❌ 拒絕]  [⚠️ 問更多資訊]
```

#### 3. **工具白名單 (Tool Whitelist)**

```json5
{
  "tools": {
    "exec": {
      "allowlist": ["git", "ls", "cat", "echo", "date"],
      "denylist": ["rm -rf", "dd if=", "mkfs", "chmod 777"]
    },
    "browser": {
      "allowedDomains": ["wikipedia.org", "github.com"],
      "blockedDomains": ["malicious-site.com"]
    }
  }
}
```

#### 4. **網絡邊界控制 (Network Boundary)**

```json5
{
  "network": {
    "allowedHosts": [
      "wttr.in",           // 天氣查詢
      "github.com",        // GitHub
      "api.telegram.org"   // Telegram
    ],
    "blockedHosts": [
      "openclawdir.com",   // 已知惡意域名
      "malicious-site.com"
    ],
    "proxySettings": {
      "useProxy": true,
      "proxyUrl": "http://privacy-router.local"
    }
  }
}
```

### 🛡️ **OWASP 2025 安全指引**

根據 **OWASP Top 10 for AI Agents (2025)**，核心風險包括：

| 風險類別 | 說明 | 防禦策略 |
|---------|------|---------|
| **A1: Prompt Injection** | 惡意輸入繞過指令 | **輸入隔離 + 提示模板化** |
| **A2: Tool Abuse** | 濫用工具進行破壞 | **工具白名單 + 確認機制** |
| **A3: Data Exfiltration** | 數據洩露 | **數據分類 + 訪問控制** |
| **A4: Privilege Escalation** | 權限提升 | **最小權限 + 沙箱隔離** |
| **A5: Credential Theft** | 憑證盜用 | **憑證加密 + 零知識存儲** |
| **A6: Untrusted Extensions** | 不可信擴展 | **擴展審計 + 白名單** |
| **A7: Side Channel Attacks** | 側信道攻擊 | **資源限制 + 隔離** |
| **A8: Replay Attacks** | 重放攻擊 | **一次性 Token + 時間戳驗證** |
| **A9: Resource Exhaustion** | 資源耗盡 | **Rate Limiting + 資源限制** |
| **A10: Agent Hijacking** | 代理人劫持 | **身份驗證 + 會話隔離** |

---

## 防禦性設計架構

### 核心原則

**Defensible Design = 明確的邊界 + 可控制的權限 + 可審計的日誌**

### 🏗️ **架構設計**

```
┌─────────────────────────────────────────────────────────────────┐
│                    安全邊界層 (Security Boundary)               │
│  • 輸入過濾 (Prompt Injection Protection)                      │
│  • 輸出過濾 (Data Exfiltration Prevention)                     │
│  • 身份驗證 (Authentication + Authorization)                   │
└─────────────────────────┬───────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────────┐
│                    沙箱隔離層 (Sandbox Layer)                   │
│  • 進程隔離 (Process Isolation)                                │
│  • 文件系統沙箱 (Filesystem Sandbox)                           │
│  • 網絡沙箱 (Network Sandbox)                                  │
└─────────────────────────┬───────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────────┐
│                    控制平面層 (Control Plane)                   │
│  • 工具調用控制 (Tool Invocation Control)                      │
│  • 用戶確認機制 (User Confirmation)                            │
│  • 白名單管理 (Whitelist Management)                          │
└─────────────────────────┬───────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────────┐
│                    審計日誌層 (Audit Layer)                     │
│  • 操作日誌 (Operation Logging)                                │
│  • 異常檢測 (Anomaly Detection)                                │
│  • 定期審計 (Periodic Audit)                                   │
└─────────────────────────────────────────────────────────────────┘
```

### 🔐 **核心安全組件**

#### 1. **提示注入防護 (Prompt Injection Protection)**

**實現方式**：
```python
# 隔離外部輸入與核心提示
def process_input(user_input: str) -> str:
    """安全處理用戶輸入"""
    
    # 1. 驗證輸入格式
    if not validate_input(user_input):
        raise ValueError("Invalid input format")
    
    # 2. 轉義特殊字符
    sanitized = escape_special_chars(user_input)
    
    # 3. 檢查惡意指令
    if contains_malicious_patterns(sanitized):
        raise SecurityError("Potential prompt injection detected")
    
    return sanitized
```

#### 2. **沙箱隔離 (Sandbox Isolation)**

**實現方式**：
```bash
# 使用 Docker 沙箱
docker run --rm \
  --memory 4g \
  --cpus 2.0 \
  --network none \
  --read-only \
  --tmpfs /tmp \
  --security-opt no-new-privileges \
  openclaw-agent:latest

# 或使用 macOS App Sandbox
sandbox-exec -p '(deny default)' openclaw-agent
```

#### 3. **工具白名單 (Tool Whitelist)**

**實現方式**：
```json5
{
  "tools": {
    "exec": {
      "allowlist": ["git", "ls", "cat", "date", "echo", "jq"],
      "denylist": ["rm -rf", "dd", "mkfs", "chmod 777", "chattr"],
      "maxDuration": 30000,  // 最大執行時間 30 秒
      "rateLimit": "10/minute"
    }
  }
}
```

#### 4. **用戶確認機制 (User Confirmation)**

**實現方式**：
```python
# 確認機制實現
def require_user_confirmation(action: dict) -> bool:
    """獲取用戶確認"""
    
    # 1. 判斷風險等級
    risk_level = assess_risk(action)
    
    # 2. 紅線直接拒絕
    if risk_level == "CRITICAL":
        return False
    
    # 3. 黃線發送確認請求
    if risk_level == "HIGH":
        message = format_confirmation_request(action)
        telegram.send(message)
        
        # 4. 等待用戶回覆
        response = telegram.wait_for_confirmation()
        
        # 5. 記錄確認日誌
        log_confirmation(
            action=action,
            user="username",
            response=response,
            timestamp=datetime.now()
        )
        
        return response == "approved"
    
    # 6. 綠線直接通過
    return True
```

---

## 你的 Mac Studio + Mac Mini 安全部署方案

### 🏗️ **架構優化**

```
┌──────────────────────────────────────────────────────────────┐
│                    Mac Studio (本地 LLM + 沙箱)               │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ Qwen 3.5   │  │ MiniMax M2.5│  │  Docker Sandbox     │  │
│  │ (本地運行) │  │ (本地運行) │  │  (沙箱隔離)         │  │
│  └─────────────┘  └─────────────┘  └──────────┬──────────┘  │
│                                              ↓              │
│              ┌──────────────────────────────────────────┐   │
│              │    OpenClaw Gateway (沙箱模式+require)   │   │
│              │    • bind=loopback (127.0.0.1 only)     │   │
│              │    • sandbox.mode=require               │   │
│              │    • tools.workspaceOnly=true           │   │
│              └──────────────┬───────────────────────────┘   │
└─────────────────────────────┼───────────────────────────────┘
                              │ SSH Tunnel / 內網傳輸
                              ↓
┌──────────────────────────────────────────────────────────────┐
│                    Mac Mini (Gateway Host + 服務器)          │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐    │
│  │ Telegram    │  │ Nginx        │  │ Privacy Router   │    │
│  │ Bot         │  │ Reverse Proxy│  │ (網絡隔離)       │    │
│  │ allowlist   │  │ HTTPS Only   │  │                  │    │
│  └─────────────┘  └──────────────┘  └──────────────────┘    │
│  ┌─────────────┐  ┌──────────────┐                          │
│  │ Email       │  │ Dify Workflow│                          │
│  │ Server      │  │ (API Token 隔離)                       │    │
│  └─────────────┘  └──────────────┘                          │
└──────────────────────────────────────────────────────────────┘
```

### 🔐 **安全配置步驟**

#### **步驟 1：Mac Studio 安全配置**

```bash
# 1. 安裝 OpenClaw（升級到最新安全版本）
npm install -g openclaw@latest

# 2. 設置強制沙箱模式
openclaw config set agents.defaults.sandbox.mode "require"
openclaw config set agents.defaults.sandbox.scope "agent"

# 3. 限制 Gateway 綁定（僅內網）
openclaw config set gateway.bind "127.0.0.1"

# 4. 設置工具白名單
openclaw config set tools.exec.allowlist '["git","ls","cat","date","echo"]'
openclaw config set tools.exec.denylist '["rm -rf","dd if=","mkfs","chmod 777"]'

# 5. 啟用工作區限制
openclaw config set tools.applyPatch.workspaceOnly true

# 6. 設置網絡白名單
openclaw config set network.allowedHosts '["wttr.in","github.com","api.telegram.org"]'
openclaw config set network.blockedHosts '["openclawdir.com"]'

# 7. 驗證配置
openclaw config validate
```

#### **步驟 2：Mac Mini 安全配置**

```bash
# 1. 安裝基礎服務
brew install nginx tmux htop

# 2. 配置 Nginx 反代（僅允許 HTTPS）
sudo nano /usr/local/etc/nginx/nginx.conf

# Nginx 配置示例：
# user  www-data;
# worker_processes  auto;
# 
# events {
#   worker_connections  1024;
# }
# 
# http {
#   # Security Headers
#   add_header X-Frame-Options "SAMEORIGIN" always;
#   add_header X-Content-Type-Options "nosniff" always;
#   add_header X-XSS-Protection "1; mode=block" always;
#   add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
# 
#   # Rate Limiting
#   limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
# 
#   # SSL Configuration
#   ssl_protocols TLSv1.2 TLSv1.3;
#   ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256;
#   ssl_prefer_server_ciphers on;
# 
#   server {
#     listen 80;
#     server_name openclaw.yourdomain.com;
#     return 301 https://$host$request_uri;
#   }
# 
#   server {
#     listen 443 ssl;
#     server_name openclaw.yourdomain.com;
# 
#     ssl_certificate /etc/letsencrypt/live/yourdomain/fullchain.pem;
#     ssl_certificate_key /etc/letsencrypt/live/yourdomain/privkey.pem;
# 
#     location / {
#       limit_req zone=api_limit burst=20 nodelay;
#       proxy_pass http://127.0.0.1:18789;
#       proxy_set_header Host $host;
#       proxy_set_header X-Real-IP $remote_addr;
#       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#       proxy_set_header X-Forwarded-Proto $scheme;
#     }
#   }
# }

# 3. 配置防火牆（僅開放必要端口）
sudo ufw allow 443/tcp    # HTTPS
sudo ufw allow 22/tcp     # SSH (僅 SSH Key)
sudo ufw enable

# 4. 設置 SSH 安全配置
sudo nano /etc/ssh/sshd_config
# PermitRootLogin no
# PasswordAuthentication no
# PubkeyAuthentication yes
# MaxAuthTries 3
# AllowUsers username

sudo systemctl restart sshd
```

#### **步驟 3：Telegram Bot 安全配置**

```json5
// ~/.openclaw/openclaw.json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "botToken": "${TELEGRAM_BOT_TOKEN}",  // 存於 .env，非代碼
      "dmPolicy": "allowlist",
      "allowFrom": ["tg:6057001835"],  // 僅你的 Telegram ID
      "groupPolicy": "requireMention",
      "rateLimit": {
        "messages": 10,
        "period": "1m"
      },
      "security": {
        "messageFilter": true,
        "commandWhitelist": ["!help", "!status", "!approve"],
        "commandBlocklist": ["!ssh", "!rm", "!sudo", "!exec"]
      }
    }
  }
}
```

#### **步驟 4：Email + Dify 安全配置**

```bash
# Email 安全配置
cat > ~/.openclaw/.env << 'EOF'
# Email Server Security
EMAIL_SMTP_HOST="smtp.yourdomain.com"
EMAIL_SMTP_PORT="587"
EMAIL_SMTP_USER="bot@yourdomain.com"
EMAIL_SMTP_PASSWORD="$(openssl rand -base64 32)"  # 隨機生成

# Dify API Security (API Token 隔離)
DIFY_API_KEY="app-xxxxx"
DIFY_BASE_URL="https://your-dify-instance.com"
DIFY_API_TIMEOUT="30000"  # 30 秒超時
DIFY_RATE_LIMIT="10/minute"  # 速率限制
EOF

# 權限設置
chmod 600 ~/.openclaw/.env
chown username:staff ~/.openclaw/.env
```

---

## 實戰檢查清單

### ✅ **安全配置檢查（每日）**

| 項目 | 狀態 | 說明 |
|------|------|------|
| **沙箱模式啟用** | ✅ | `sandbox.mode=require` |
| **Gateway 僅內網** | ✅ | `gateway.bind=127.0.0.1` |
| **工具白名單配置** | ✅ | `tools.exec.allowlist` |
| **網絡白名單配置** | ✅ | `network.allowedHosts` |
| **Telegram 白名單** | ✅ | `dmPolicy=allowlist` |
| **防火牆啟用** | ✅ | `ufw enable` |
| **SSH 僅 Key 登入** | ✅ | `PasswordAuthentication=no` |

### ✅ **週期性檢查（每週）**

| 項目 | 頻率 | 說明 |
|------|------|------|
| **系統安全更新** | 每週 | `brew upgrade && npm update -g openclaw` |
| **日誌審查** | 每週 | 檢查異常模式、RCE 嘗試 |
| **技能審計** | 每週 | 檢查已安裝技能的安全狀態 |
| **備份驗證** | 每週 | 確認配置文件備份正常 |
| **日誌清理** | 每週 | 清理過時日誌，保留審計記錄 |

### ✅ **月度檢查（每月）**

| 項目 | 頻率 | 說明 |
|------|------|------|
| **紅隊演練** | 每月 | 模擬攻擊，測試防禦有效性 |
| **權限審查** | 每月 | 確認所有權限配置正確 |
| **安全更新** | 每月 | 檢查 OpenClaw 官方安全公告 |
| **日誌回顧** | 每月 | 分析月度審計報告 |
| **策略優化** | 每月 | 根據經驗更新安全策略 |

### ✅ **緊急應對檢查（當發現異常時）**

**緊急剷除步驟**：
```bash
# 1. 立即停止所有 Agent 進程
pkill -f openclaw

# 2. 終止異常會話
openclaw sessions list  # 確認會話 ID
openclaw sessions kill <session-id>  # 終止異常會話

# 3. 啟用安全模式
openclaw config set agents.defaults.sandbox.mode "require"
openclaw config set tools.exec.allowlist '[]'  # 暫時禁用所有工具

# 4. 運行深度審計
openclaw security audit --deep --fix

# 5. 審查日誌
openclaw logs view --since "2026-03-18T00:00:00" --level error

# 6. 恢復需用戶確認
# 等待用戶確認後才恢復正常模式
```

---

## 知識總結

### 📚 **核心概念**

| 概念 | 說明 | 關鍵要點 |
|------|------|---------|
| **沙箱隔離** | 將 AI 運行在隔離環境中 | **mandatory for production** |
| **用戶確認** | 高風險操作需明確批准 | **never assume approval** |
| **工具白名單** | 僅允許可信工具執行 | **deny by default** |
| **網絡邊界** | 限制外部連接 | **explicit allowlist** |
| **審計日誌** | 所有操作記錄在案 | **immutable logs** |

### 🔑 **關鍵風險**

| 風險 | 嚴重性 | 防禦策略 |
|------|-------|---------|
| **CVE-2026-25253** | HIGH (8.8) | 升級 + 沙箱 + 白名單 |
| **惡意 Skill 注入** | HIGH | 技能審計 + 來源驗證 |
| **Token 洩露** | CRITICAL (9.8) | 加密存儲 + 零知識 |
| **WebSocket RCE** | HIGH | WebSocket 安全驗證 |
| **依賴漏洞** | MEDIUM | `npm audit fix` + 定期更新 |

### 💡 **最佳實踐**

1. **永遠使用沙箱模式**：`sandbox.mode = "require"`
2. **限制 Gateway 綁定**：`gateway.bind = "127.0.0.1"`
3. **啟用工具白名單**：`tools.exec.allowlist`
4. **限制網絡連接**：`network.allowedHosts`
5. **啟用審計日誌**：所有操作記錄
6. **定期安全更新**：每週檢查系統更新
7. **緊急應對計劃**：預先制定剷除流程

---

**🛡️ 最終目標**：**讓 OpenClaw 成為安全、可控、可審計的 AI 助手平台**

**📅 更新日期**：2026-03-18  
**👤 維護者**：Hillman Tam
**✅ 狀態**：已驗證安全

---

> **核心信念**：AI Agent 的安全性取決於我們對邊界的定義、對權限的控制、對日誌的審計。透過防禦性設計，我們可以讓 OpenClaw 成為值得信賴的個人助手。
