---
hermes_version: 0.10.0
title: 配置文件详解
---

# 17. 配置文件详解

## 心智模型:两文件,分工明确

```mermaid
graph TB
    subgraph "~/.hermes/"
        C[config.yaml<br/>行为与偏好]
        E[.env<br/>密钥与敏感信息]
    end

    C -.-> D1[default_model]
    C -.-> D2[max_iterations]
    C -.-> D3[display.skin]
    C -.-> D4[messaging.telegram.allowed_users]
    C -.-> D5[...]

    E -.-> E1[OPENROUTER_API_KEY]
    E -.-> E2[ANTHROPIC_API_KEY]
    E -.-> E3[TELEGRAM_BOT_TOKEN]
    E -.-> E4[...]

    style C fill:#FFD700,color:#000
    style E fill:#FF6B6B,color:#fff
```

**铁律**:
- `config.yaml` 可以 `git add`(分享、版本化都行)
- `.env` **绝对不能** `git add`(里面都是 token / key)

---

## 怎么改配置的三种方式

### 方式 1 · 命令式(推荐)

```bash
hermes config set display.skin slate
hermes config set max_iterations 120
hermes config set messaging.telegram.allowed_users "katya,friend"
```

**路径用点号分隔**。Hermes 会:
- 创建中间层级(如果不存在)
- 验证类型(布尔 / 数字 / 字符串 / 数组)
- 写回 YAML 保持格式

### 方式 2 · 交互式

```bash
hermes config set           # 不带参数,交互式菜单
```

树形菜单里浏览。

### 方式 3 · 直接编辑

```bash
vim ~/.hermes/config.yaml
```

优点:一次改多项,看全貌。
缺点:要熟悉 YAML 语法,手滑容易破坏格式。

---

## 全量 config.yaml(v0.10 的主要字段)

下面是一个**注释过的完整样例**,每个字段告诉你是什么、什么时候改。

```yaml
# 顶层:当前 config 版本,用于未来 migration(动不得)
_config_version: 5

# ====== 模型与 Provider ======

default_model: openrouter/anthropic/claude-sonnet-4-6
# 启动时的默认模型。对话内用 /model 改不会动这个。

providers:
  # OpenRouter 默认偏好
  openrouter:
    preferred_order:           # 候选模型顺序
      - openrouter/anthropic/claude-sonnet-4-6
      - openrouter/google/gemini-2.5-flash
  routing:
    # 主模型不可用时的 fallback
    fallback_model: openrouter/google/gemini-2.5-flash
    max_fallback_attempts: 3

# ====== Agent 行为 ======

max_iterations: 90
# 一次对话最多多少轮 LLM + 工具循环。超了报错给你。

iteration_budget: null
# 细粒度 token 预算(通常不用改)

skip_memory: false
# 启动时不注入 memory(命令行 --skip-memory 等价)

skip_context_files: false
# 启动时不注入 AGENTS.md 等项目 context

save_trajectories: false
# 保存完整轨迹(研究用,见第五部)

quiet_mode: false
# 静默模式,少打印

# ====== 辅助模型 ======

auxiliary:
  mode: auto          # auto | same-as-main | custom
  # auto (v0.10 默认):沿用主模型,减少多 provider 的配置
  # same-as-main:强制同模型
  # custom:下面配每个任务用什么
  custom:
    compression: openrouter/google/gemini-2.5-flash
    summarization: openrouter/google/gemini-2.5-flash
    session_search: openrouter/google/gemini-2.5-flash
    title_generation: openrouter/google/gemini-2.5-flash

# ====== 显示 / 界面 ======

display:
  skin: default             # default | ares | mono | slate | 自定义名
  banner: true              # 启动显示 banner
  tool_prefix: "┊"          # 工具输出前缀(默认数据)
  response_border: true     # 响应框边框
  background_process_notifications: all   # all | result | error | off
  tool_progress_command: false            # TUI 里显示工具进度滚动
  kawaii_spinner: true                    # 加载时的颜文字 spinner

# ====== 记忆 ======

memory:
  memory_max_chars: 10000   # MEMORY.md 上限字符数
  user_max_chars: 5000      # USER.md 上限字符数
  nudge_interval: 20        # 每多少轮提醒一次 agent 考虑记录

# ====== 技能 ======

skills:
  autoload: true            # 启动时索引 ~/.hermes/skills/
  hub_url: https://agentskills.io
  max_search_results: 10

# ====== 会话 ======

sessions:
  autoclose_idle: false     # 闲置会话自动关
  idle_timeout: 3600        # 秒
  max_stored: null          # 不限存储
  # 历史对话自动 title 生成
  auto_title: true

# ====== 消息平台 ======

messaging:
  telegram:
    token: ${TELEGRAM_BOT_TOKEN}   # 引用 .env 里的变量
    allowed_users: []               # 空 = 不限(危险!)
    allowed_chats: []
    home_channel: null
    transcribe_voice: false
    proxy_url: null                 # socks5://127.0.0.1:7890 等
    markdown_tables_as_code: true   # 表格自动包代码块(v0.9+)

  discord:
    token: ${DISCORD_BOT_TOKEN}
    allowed_roles: []
    home_channel_id: null
    forum_support: true             # v0.9+

  slack:
    bot_token: ${SLACK_BOT_TOKEN}
    app_token: ${SLACK_APP_TOKEN}
    allowed_users: []
    default_channel: null
    dm_pairing_required: false      # 生产推荐 true

  # 更多平台:whatsapp / signal / email / matrix / mattermost / bluebubbles
  #         weixin / wecom / dingtalk / feishu / qqbot / sms / webhook

# ====== Cron ======

cron:
  enabled: true
  timezone: Asia/Shanghai           # 你的本地时区
  max_concurrent: 3                 # 最多几个任务并行
  default_timeout: 600              # 默认超时(秒)
  default_retry: 2

# ====== 工具 ======

tools:
  # 各平台独立的工具开关(hermes tools 会改这里)
  enabled_per_platform:
    cli: []          # 空 = 默认全开
    messaging: []    # 空 = 默认只开安全子集
    cron: []

  # MCP 服务器
  mcp:
    servers:
      github:
        command: npx
        args: ["-y", "@modelcontextprotocol/server-github"]
        env:
          GITHUB_PERSONAL_ACCESS_TOKEN: ${GITHUB_TOKEN}
      postgres:
        command: uvx
        args: ["mcp-server-postgres", "--connection-string", "${DB_URL}"]

  # 工具 Gateway (Nous Portal 订阅)
  use_gateway:
    web_search: false        # v0.10+ 订阅用户可开
    image_generation: false
    tts: false
    browser_use: false

# ====== 终端后端 ======

terminal:
  default_backend: local    # local | docker | ssh | daytona | modal | singularity
  backends:
    docker:
      image: ubuntu:22.04
    ssh:
      host: my-vps.example.com
      user: hermes
    daytona:
      workspace_id: ...

# ====== 网络 ======

network:
  proxy: null                # 全局 proxy
  timeout: 60
  retries: 3

# ====== 安全 ======

security:
  approval:
    enabled: true                   # 默认开
    auto_approve_patterns: []       # 自动允许的命令模式
    block_patterns:
      - "rm -rf /"
      - "dd if="
      - "mkfs"
    require_confirmation_for:
      - "sudo"
      - "git push --force"
  path_security:
    sandbox_mode: false             # 限制在工作目录内读写
    allowed_paths: []
  secrets:
    scan_memory: true               # 扫描 memory 里的敏感模式
    scan_output: false              # 扫描输出(避免泄露)

# ====== Profile ======

profiles:
  confirm_remove: false             # 删 profile 是否二次确认

# ====== 个性 ======

personality:
  default: null                     # 默认个性名

# ====== Honcho (可选 · 用户画像增强) ======

honcho:
  enabled: false
  api_url: https://honcho.dev/api
  api_key: ${HONCHO_API_KEY}
```

---

## `.env` 文件结构

```bash
# ~/.hermes/.env

# ---- Provider keys ----
OPENROUTER_API_KEY=sk-or-...
ANTHROPIC_API_KEY=sk-ant-...
OPENAI_API_KEY=sk-...
GEMINI_API_KEY=...
XAI_API_KEY=...
NOUS_API_KEY=...

# ---- Messaging tokens ----
TELEGRAM_BOT_TOKEN=123456:ABC...
DISCORD_BOT_TOKEN=...
SLACK_BOT_TOKEN=xoxb-...
SLACK_APP_TOKEN=xapp-...

# ---- Tools ----
BROWSERBASE_API_KEY=...
FIRECRAWL_API_KEY=...
FAL_API_KEY=...
ELEVENLABS_API_KEY=...

# ---- Ancillary ----
GITHUB_TOKEN=ghp_...
DB_URL=postgresql://user:pass@host/db
HONCHO_API_KEY=...

# ---- Proxy ----
TELEGRAM_PROXY=socks5://127.0.0.1:7890
```

**Hermes 读取 `.env` 的规则**:
- 启动时自动 `load_dotenv()`
- `config.yaml` 里 `${VAR}` 引用会被替换
- 没定义的变量用**空字符串**替换(不报错)

---

## Config 版本迁移

`_config_version` 字段跟踪 schema 版本。

**当 Hermes 升级引入 breaking 的 config 字段变动**:
1. 启动时检测到 `_config_version` 低于当前
2. 自动跑 migration 脚本(在 `hermes_cli/config.py`)
3. 备份老 config 到 `~/.hermes/config.yaml.v4.bak`
4. 写回新 config,更新 version

**如果 migration 失败**:
- Hermes 报错,要求你手动 diff 和合并
- 老 config 备份仍在,数据不会丢

---

## 常用速查

### 查当前配置

```bash
hermes config get display.skin
hermes config get messaging.telegram.allowed_users
hermes config get                                    # 全 dump
```

### 重置某项

```bash
hermes config unset display.skin                    # 回默认
```

### 从备份恢复

```bash
cp ~/.hermes/config.yaml.bak ~/.hermes/config.yaml
```

### 用环境变量 override(临时)

```bash
HERMES_MAX_ITERATIONS=200 hermes
```

`HERMES_<UPPERCASE_KEY>` 格式,临时 override config 单项。

---

## 坑点

### 坑 1 · YAML 格式错误

**现象**:启动报 `yaml.scanner.ScannerError`。

**排查**:
- 缩进用空格,不要混用 tab
- `:` 后面要有空格(`key: value` 不是 `key:value`)
- 字符串含冒号要引号(`proxy: "socks5://...:7890"`)

**对策**:用 `hermes config set` 命令改,避免手写 YAML 出错。

### 坑 2 · `.env` 没被读到

**现象**:虽然 `.env` 里有 API key,但 `hermes doctor` 说没配。

**排查**:
```bash
ls -la ~/.hermes/.env              # 文件确实在?
cat ~/.hermes/.env | grep API      # 确实有值?
```

常见原因:
- `.env` 里用了 export(不需要,Hermes 自己 load)
- key 名拼错
- 值前后有空格 / 引号不对

### 坑 3 · 改了 config 但没生效

**现象**:`config set` 成功,但 Hermes 行为没变。

**原因**:
- CLI / gateway **需要重启才生效**(不热加载)
- Profile 没对上(你改了 default,但 hermes -p work 跑的是 work)

### 坑 4 · 敏感信息进了 config.yaml

**现象**:你不小心把 API key 写进 `config.yaml`,还 push 了。

**对策**:
- 立刻 revoke 泄露的 key
- 把值改为 `${XXX_KEY}` 形式,真值移到 `.env`
- git 历史也要清:`git filter-repo --path config.yaml --invert-paths` 或 BFG

### 坑 5 · `_config_version` 乱改

**现象**:你手动把 `_config_version` 改成一个不存在的值,启动报错。

**对策**:**不要手改这个字段**。Hermes 自己管理。

---

## 进阶

- 第 30 章(第四部)—— DEFAULT_CONFIG 源码位置、migration 写法
- 第 28 章(第四部)—— `hermes_cli/config.py` 的三种加载器(CLI / tools / gateway)

---

下一章:[18. 皮肤定制 →](18-skin-customize.md)
