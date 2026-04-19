---
hermes_version: 0.10.0
title: 与 OpenClaw 迁移对照
---

# F. 与 OpenClaw 迁移对照

给从 [OpenClaw](https://github.com/openclaw/openclaw) 过来的老用户 —— 你原来的**习惯、配置、数据**到 Hermes 里怎么映射。

---

## 一键迁移

Hermes 自带迁移命令。**首次设置时**会自动检测 `~/.openclaw/` 并提议迁移;也可以随时手动:

```bash
hermes claw migrate              # 交互式
hermes claw migrate --dry-run    # 预览
hermes claw migrate --preset user-data   # 不含 secrets
hermes claw migrate --overwrite  # 覆盖冲突
```

### 默认会迁移

- **SOUL.md** → Hermes 的 personality
- **MEMORY.md** / **USER.md** → 同名,Hermes 原生支持
- **用户技能** → `~/.hermes/skills/openclaw-imports/`
- **Approval 允许列表** → Hermes 的 `security.approval.auto_approve_patterns`
- **消息平台配置** → `messaging.*`
- **已注册的 API key**(Telegram、OpenRouter、OpenAI、Anthropic、ElevenLabs)→ `~/.hermes/.env`
- **TTS workspace 文件** → `~/.hermes/assets/`
- **AGENTS.md**(带 `--workspace-target` 时)→ 项目目录

---

## 概念映射

| OpenClaw 叫 | Hermes 叫 | 备注 |
|---|---|---|
| Soul | Personality | 一个概念,文件格式略有调整 |
| Memory (MEMORY.md) | Memory (MEMORY.md) | 完全兼容 |
| User file (USER.md) | USER.md | 完全兼容 |
| Skill | Skill | 完全兼容 agentskills.io 标准 |
| Agent | Agent / Subagent | 术语统一 |
| Chat | Session / Conversation | session 是 Hermes 核心概念 |
| Approval list | Approval patterns | 配置位置在 config.yaml |
| Platform adapter | Platform | 完全一致 |
| Toolbox | Toolset | 用 `hermes tools` 管 |
| Agent runner | AIAgent class | 代码层面重命名 |
| Claw CLI | `hermes` CLI | 命令结构类似 |
| Worker | Subagent / delegate_task | 并行任务的概念 |
| Flow | Skill | 复用的流程,Hermes 叫 skill |

---

## CLI 命令对照

| OpenClaw | Hermes |
|---|---|
| `claw` | `hermes` |
| `claw setup` | `hermes setup` |
| `claw run` | `hermes chat` / `hermes`(TUI) |
| `claw gateway` | `hermes gateway` |
| `claw cron` | `hermes cron` |
| `claw skill` | `hermes skills` |
| `claw config` | `hermes config` |
| `claw upgrade` | `hermes update` |
| `claw doctor` | `hermes doctor` |

---

## 配置文件位置

| OpenClaw | Hermes |
|---|---|
| `~/.openclaw/config.yaml` | `~/.hermes/config.yaml` |
| `~/.openclaw/.env` | `~/.hermes/.env` |
| `~/.openclaw/memory/` | `~/.hermes/memories/` |
| `~/.openclaw/skills/` | `~/.hermes/skills/` |
| `~/.openclaw/personalities/` | `~/.hermes/personalities/` |

---

## 常见功能映射

### 1. 切换模型

```bash
# OpenClaw
claw model set <id>

# Hermes
hermes model <id>
# 对话内: /model <id>
```

### 2. 启用 gateway

```bash
# OpenClaw
claw gateway run

# Hermes
hermes gateway start
```

### 3. 记忆管理

```bash
# 查看
cat ~/.hermes/memories/MEMORY.md    # 直接 cat
hermes memory show                   # 命令

# 添加
echo "新事实" >> ~/.hermes/memories/MEMORY.md   # 不推荐(没 § 分隔)
# 或在对话里:
> /memory add "新事实"
> 请把这条记到 memory 里:...
```

### 4. 写一个技能

目录结构**完全一致** —— OpenClaw 的 skill 文件直接拷过来就能用。

### 5. cron 任务

```bash
# OpenClaw
claw cron add --at "08:00" --target telegram "..."

# Hermes
hermes cron add --at "08:00" --deliver telegram "..."
# 注意: --target → --deliver
```

### 6. Profile

**OpenClaw 没有原生 Profile**,很多用户通过多 `~/.openclaw-<name>/` 目录模拟。

Hermes 有原生 `hermes profile` 系统(见 [第 16 章](../part-3-mastery/16-profile-backup.md))。迁移时:

- 默认 profile → `~/.hermes/`
- 想要多 profile → `hermes profile create <name>` 然后重跑迁移 `hermes -p <name> claw migrate`

---

## 新特性(OpenClaw 没有或弱)

Hermes 比 OpenClaw 多了什么:

- **FTS5 会话搜索**(`session_search`)—— 跨所有历史对话的全文检索
- **多模型厂牌**:200+ 模型,xAI、Xiaomi MiMo、NVIDIA NIM、Ollama Cloud 等
- **Fast Mode**(`/fast`)—— OpenAI / Anthropic 优先队列
- **Nous Tool Gateway**(v0.10)—— 订阅即用的工具集
- **Local Web Dashboard**(v0.9+)
- **`hermes backup` / `import`** —— 跨机迁移一键搞定
- **插件系统** —— 改 Hermes 本体行为
- **Atropos / RL 环境**
- **Prompt caching** 精细化管理(OpenClaw 有,但 Hermes 更严格)
- **16 平台** 支持(OpenClaw 多数也有,但稳定性提升)
- **BlueBubbles iMessage / 钉钉 AI Cards / 飞书文档评论** 等新平台

---

## 破坏性差异(迁移要注意)

### 1. Memory 文件存储路径

```
OpenClaw: ~/.openclaw/memory/MEMORY.md
Hermes:   ~/.hermes/memories/MEMORY.md     ← "memories" 复数
```

### 2. 几个环境变量重命名

| 老 | 新 |
|---|---|
| `OPENCLAW_HOME` | `HERMES_HOME` |
| `OPENCLAW_PROFILE` | `HERMES_PROFILE` |
| `OPENCLAW_DEBUG` | `HERMES_DEBUG` |

### 3. 配置 YAML 字段重组

多数字段一致,少数重组(如 `messaging.*` 树更深)。**使用 `hermes claw migrate` 会自动映射**,不要手动搬。

### 4. Skill 元数据字段

更严格遵循 agentskills.io 标准:

- `name` 必须 ≤64 字符
- `description` 必须 ≤1024 字符

超过的会被**截断或拒绝**。迁移时 agent 会提示你哪些 skill 需要修。

### 5. OAuth token 不迁移

API key 会迁移,但各平台的**OAuth token**(GitHub / Google / Notion MCP)需要**重新授权**。原因:token 存储格式可能有变,安全起见重建。

---

## 迁移步骤(详细)

```bash
# 1. 先备份 OpenClaw(以防万一)
cp -r ~/.openclaw ~/.openclaw.bak

# 2. 装 Hermes
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash

# 3. 预览迁移
hermes claw migrate --dry-run

# 4. 真的迁移
hermes claw migrate

# 5. 验证
hermes doctor
hermes
> 你好,我是 Katya,你记得之前跟我聊过的什么吗?
[ 应该回忆起 memory 里的事实 ]

# 6. 启动 gateway(如果用 bot)
hermes gateway start

# 7. 没问题 → 几天后删 backup
rm -rf ~/.openclaw.bak
```

---

## 并存运行

想 OpenClaw 和 Hermes **同时跑** 做对比?

- **不同 bot token**(不然占用冲突)
- **不同 gateway 端口**(如果用 webhook 模式)
- **OpenClaw 的配置独立**,Hermes 读 `~/.hermes/`,互不干扰

---

## 故障排查

### 迁移后 agent 不认识我

- 确认 `~/.hermes/memories/` 下有文件:`ls -la ~/.hermes/memories/`
- `hermes memory show` 看内容
- 如果为空,重新跑迁移:`hermes claw migrate --overwrite`

### Gateway 连不上 Telegram

- bot token 迁移了但 webhook 没清
- 手动删:`curl "https://api.telegram.org/bot<TOKEN>/deleteWebhook"`
- 重启:`hermes gateway restart`

### 迁移的技能报错

- 多半是元数据超限(name / description 太长)
- 编辑 `~/.hermes/skills/openclaw-imports/<name>/SKILL.md`,裁短

---

## 获取帮助

- OpenClaw → Hermes 专用迁移指南:[hermes-agent.nousresearch.com/docs/getting-started/openclaw-migration](https://hermes-agent.nousresearch.com/docs/getting-started/openclaw-migration)
- [Discord](https://discord.gg/NousResearch) #migration 频道
- `hermes debug share`,把 URL 贴出来让别人帮你看

---

> 欢迎来到 Hermes ☤
