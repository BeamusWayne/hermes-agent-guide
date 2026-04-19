---
hermes_version: 0.10.0
title: 环境变量完整参考
---

# C. 环境变量完整参考

按**用途**分类。

---

## 核心 · Hermes 自身

| 变量 | 作用 | 示例 |
|---|---|---|
| `HERMES_HOME` | 数据目录 | `~/.hermes` 或 `~/.hermes/profiles/work` |
| `HERMES_PROFILE` | 激活 profile(等价 -p) | `work` |
| `HERMES_DEBUG` | 开调试日志 | `1` |
| `HERMES_DEBUG_TOOLS` | 只打印工具调用细节 | `1` |
| `HERMES_LOG_USAGE` | 打印每次 token 用量 | `1` |
| `HERMES_MAX_ITERATIONS` | 覆盖 max_iterations | `200` |
| `HERMES_QUIET` | 静默模式 | `1` |
| `HERMES_NO_COLOR` | 关闭终端颜色 | `1` |
| `HERMES_NO_BANNER` | 不显示启动 banner | `1` |
| `HERMES_SKIP_MEMORY` | 不加载 memory | `1` |
| `HERMES_SKIP_CONTEXT` | 不加载 context files | `1` |
| `HERMES_BACKGROUND_NOTIFICATIONS` | 后台进程通知 `all/result/error/off` | `result` |
| `MESSAGING_CWD` | 消息平台的工作目录 | `/home/bot` |

---

## Provider · 模型 API Key

| 变量 | Provider |
|---|---|
| `OPENROUTER_API_KEY` | OpenRouter |
| `ANTHROPIC_API_KEY` | Anthropic 官方 |
| `OPENAI_API_KEY` | OpenAI 官方 |
| `NOUS_API_KEY` / `NOUS_PORTAL_API_KEY` | Nous Portal |
| `GEMINI_API_KEY` / `GOOGLE_API_KEY` | Google Gemini |
| `GEMINI_CLI_OAUTH_*` | Gemini CLI OAuth |
| `XAI_API_KEY` | xAI (Grok) |
| `MIMO_API_KEY` | Xiaomi MiMo |
| `NVIDIA_NIM_API_KEY` | NVIDIA NIM |
| `OLLAMA_CLOUD_API_KEY` | Ollama Cloud |
| `OLLAMA_HOST` | 本地 Ollama endpoint | 
| `KIMI_API_KEY` / `MOONSHOT_API_KEY` | Kimi / Moonshot |
| `MINIMAX_API_KEY` | MiniMax |
| `ZAI_API_KEY` / `GLM_API_KEY` | z.ai / GLM |
| `DEEPSEEK_API_KEY` | DeepSeek |
| `MISTRAL_API_KEY` | Mistral |
| `QWEN_OAUTH_*` | Qwen OAuth |
| `CODEX_API_KEY` | OpenAI Codex |

每个 provider 还可能需要:
- `<PROVIDER>_BASE_URL` 自定义端点
- `<PROVIDER>_ORG` 组织 ID
- 等等

---

## 消息平台 Token

| 变量 | 平台 |
|---|---|
| `TELEGRAM_BOT_TOKEN` | Telegram |
| `TELEGRAM_PROXY` | Telegram 专用代理 |
| `DISCORD_BOT_TOKEN` | Discord |
| `DISCORD_ALLOWED_ROLES` | 角色白名单(逗号分隔) |
| `DISCORD_PROXY` | Discord 专用代理 |
| `SLACK_BOT_TOKEN` | Slack `xoxb-` |
| `SLACK_APP_TOKEN` | Slack `xapp-`(Socket Mode) |
| `WHATSAPP_*` | WhatsApp Business |
| `SIGNAL_*` | Signal (signal-cli) |
| `EMAIL_IMAP_*` / `EMAIL_SMTP_*` | Email |
| `MATRIX_ACCESS_TOKEN` | Matrix |
| `MATTERMOST_*` | Mattermost |
| `BLUEBUBBLES_*` | iMessage(BlueBubbles) |
| `TWILIO_*` | SMS |
| `WEIXIN_*` | 微信(iLink) |
| `WECOM_*` | 企业微信 |
| `DINGTALK_*` | 钉钉 |
| `FEISHU_APP_ID` / `FEISHU_APP_SECRET` | 飞书 |
| `QQBOT_*` | QQBot |
| `HA_URL` / `HA_LONG_LIVED_TOKEN` | Home Assistant |

---

## 工具 API Key

| 变量 | 工具 |
|---|---|
| `FIRECRAWL_API_KEY` | web_extract / web_search |
| `EXA_API_KEY` | web_search(Exa provider) |
| `PARALLEL_API_KEY` | Parallel Web |
| `FAL_API_KEY` | image_generation |
| `BROWSERBASE_API_KEY` / `BROWSERBASE_PROJECT_ID` | 浏览器自动化 |
| `ELEVENLABS_API_KEY` | TTS premium |
| `OPENWEATHER_API_KEY` | 自定义天气工具(如果你跟着示例加了) |
| `GITHUB_TOKEN` | GitHub MCP |
| `HONCHO_API_KEY` | 用户建模 |

---

## 终端后端

| 变量 | 后端 |
|---|---|
| `DOCKER_HOST` | docker backend |
| `SSH_*` | SSH backend 连接 |
| `MODAL_TOKEN_ID` / `MODAL_TOKEN_SECRET` | Modal serverless |
| `DAYTONA_API_KEY` / `DAYTONA_WORKSPACE_URL` | Daytona |
| `SINGULARITY_*` | Singularity HPC |

---

## 网络

| 变量 | 作用 |
|---|---|
| `HTTP_PROXY` / `HTTPS_PROXY` | 通用 HTTP/HTTPS 代理 |
| `NO_PROXY` | 绕过代理的域名 |
| `TELEGRAM_PROXY` | Telegram 专用(v0.9+) |
| `DISCORD_PROXY` | Discord 专用 |
| `ALL_PROXY` | 通用(含 SOCKS5) |

---

## Python / 环境

| 变量 | 作用 |
|---|---|
| `PYTHONPATH` | Python 模块路径 |
| `UV_INDEX_URL` | pip / uv 镜像(镜像) |
| `PIP_INDEX_URL` | pip 镜像 |

---

## 调试相关

| 变量 | 作用 |
|---|---|
| `HERMES_DEBUG` | 主开关 |
| `HERMES_DEBUG_TOOLS` | 工具调用细节 |
| `HERMES_LOG_USAGE` | usage 日志 |
| `HERMES_TRACE_REQUESTS` | LLM 请求原始 dump(敏感!) |
| `PYTHONDEVMODE` | Python dev mode |

!!! danger "不要在生产环境开 `HERMES_TRACE_REQUESTS`"
    会把完整 prompt + response dump 到日志,**含 API key、敏感数据**。仅本地 debug 用。

---

## 怎么设

### 临时(一次性)

```bash
HERMES_DEBUG=1 hermes
```

### 当前 shell

```bash
export HERMES_DEBUG=1
hermes
```

### 持久(~/.bashrc / ~/.zshrc)

```bash
export HERMES_DEBUG=1
```

### Hermes 自己的 `.env`

```bash
# ~/.hermes/.env
HERMES_DEBUG=1
```

**推荐**:Hermes 用到的变量(特别是 API key)放 `~/.hermes/.env`,**Hermes 启动时自动 `load_dotenv()`**。

---

## 优先级

冲突时:

```
shell 环境变量 > ~/.hermes/.env > config.yaml 里 ${VAR} 引用 > 空字符串(默认)
```
