---
hermes_version: 0.10.0
title: 必学命令清单
---

# 4. 必学命令清单

## 心智模型:两套命令

```mermaid
graph LR
    subgraph "Shell 里跑 (hermes xxx)"
        CLI1[hermes]
        CLI2[hermes setup]
        CLI3[hermes model]
        CLI4[hermes tools]
        CLI5[hermes gateway]
        CLI6[hermes cron]
        CLI7[hermes doctor]
        CLI8[hermes config]
        CLI9[hermes update]
        CLI10[hermes profile]
    end

    subgraph "对话里跑 (/xxx)"
        S1[/new]
        S2[/model]
        S3[/personality]
        S4[/memory]
        S5[/skills]
        S6[/compress]
        S7[/usage]
        S8[/undo]
        S9[/retry]
        S10[/help]
    end

    CLI1 --启动 TUI--> S1
```

**核心分工**:
- **`hermes xxx`**(shell 子命令):**配置、管理、启动**。在 shell 里跑。
- **`/xxx`**(slash 命令):**对话内操作**。进入 TUI 或消息平台后跑。

下面两张表各列 10 个,**这 20 个掌握了 = 能用**。

---

## 🐚 CLI 子命令 Top 10

| # | 命令 | 做什么 | 什么时候用 |
|---|---|---|---|
| 1 | `hermes` | 启动交互式 TUI,开始对话 | 最常用的入口 |
| 2 | `hermes setup` | 交互式向导,配置所有东西 | 首次安装后;想重新配置 |
| 3 | `hermes model` | 交互式选模型 / 直接切 | 想换模型或查可用模型 |
| 4 | `hermes tools` | 启用 / 禁用工具,按平台分 | 想关掉某类工具(比如不让它访问网络) |
| 5 | `hermes gateway` | 管理消息网关(setup/start/stop/status) | 接入 Telegram/Discord/Slack... |
| 6 | `hermes cron` | 管理定时任务(add/list/remove) | 想让 agent 自动跑定期任务 |
| 7 | `hermes doctor` | 诊断当前安装状态 | 怀疑哪里出问题了 |
| 8 | `hermes config set` | 改配置文件里的值 | 改某一项设置(不想跑 setup) |
| 9 | `hermes update` | 升到最新版 | 看到新版本想升 |
| 10 | `hermes profile` | 管理 profile(多实例隔离) | 想区分工作/个人 agent |

### 1 · `hermes`(启动)

```bash
hermes                    # 默认配置启动
hermes --model <id>       # 临时指定模型
hermes -p <profile>       # 指定 profile
hermes --skip-memory      # 本次不加载 memory
hermes --quiet            # 静默模式,少打印
```

常用组合:

```bash
# 用一个便宜模型快速聊几句
hermes --model openrouter/google/gemini-2.5-flash

# 在"工作"profile 启动
hermes -p work
```

### 2 · `hermes setup`(向导)

```bash
hermes setup              # 全功能向导
hermes setup --quick      # 只配最关键的(模型 + key)
```

**任何时候都可以重跑**,覆盖旧配置。

### 3 · `hermes model`(切模型)

```bash
hermes model                                       # 交互式选
hermes model openrouter/anthropic/claude-sonnet-4-6 # 直接设
hermes model --list                                # 列出所有已配置模型
```

### 4 · `hermes tools`(开关工具)

```bash
hermes tools              # 交互界面,按平台列出所有工具
```

工具**按平台分别开关**(CLI / messaging / cron)—— 你可能想让 CLI 能跑 shell,但 Telegram 不能。

### 5 · `hermes gateway`(消息网关)

```bash
hermes gateway setup      # 配置平台(选哪些要开)
hermes gateway start      # 启动网关进程
hermes gateway status     # 看网关状态
hermes gateway stop       # 停止
```

**启动后是长驻进程**,建议用 `tmux` / `systemd` / `supervisor` 管理。

### 6 · `hermes cron`(定时任务)

```bash
# 每天 8 点跑
hermes cron add --at "08:00" --deliver telegram \
    "总结昨天的 GitHub 活动"

# 每周一 9 点
hermes cron add --at "Mon 09:00" --deliver discord \
    "列上周合并的 PR"

hermes cron list          # 列出所有
hermes cron remove <id>   # 删除
hermes cron test <id>     # 立即测试一次
```

### 7 · `hermes doctor`(诊断)

```bash
hermes doctor             # 总览
hermes doctor --verbose   # 详细输出,含每个工具的可用性原因
```

### 8 · `hermes config set`(改配置)

```bash
hermes config set display.skin slate
hermes config set max_iterations 120
hermes config set background_process_notifications result
```

键是 **点号分隔的路径**,对应 `~/.hermes/config.yaml` 里的层级。

### 9 · `hermes update`(升级)

```bash
hermes update             # 升到 stable 最新
hermes update --pre       # 升到预发布
```

**不会覆盖你的数据**(config/memory/skills 都在 `~/.hermes/`,不在 venv 里)。

### 10 · `hermes profile`(多实例)

```bash
hermes profile list                 # 列出所有 profile
hermes profile create coder         # 新建一个
hermes -p coder                     # 用它启动
hermes profile remove coder         # 删除(会 prompt 确认)
```

**每个 profile 有完全独立的** config / key / memory / skills / sessions / gateway。常见用法:
- `default` · 个人日常
- `work` · 工作任务(不同公司账号)
- `coder` · 纯编码场景(只开 file/terminal 工具)

---

## 💬 Slash 命令 Top 10

| # | 命令 | 做什么 | 什么时候用 |
|---|---|---|---|
| 1 | `/new` 或 `/reset` | 开新对话 | 当前话题聊完了,开始新的 |
| 2 | `/model` | 换模型 | 想用更强/更便宜的模型继续 |
| 3 | `/personality` | 换个性 | 想让 agent 换种说话风格 |
| 4 | `/memory` | 管理记忆 | 查/改 agent 记住的事实 |
| 5 | `/skills` | 浏览/触发技能 | 想用某个技能模板 |
| 6 | `/compress` | 压缩上下文 | 聊太久了,上下文快满了 |
| 7 | `/usage` | 看 token 用量 | 关心花了多少钱 |
| 8 | `/undo` / `/retry` | 撤销/重试上一轮 | 最后一次回答不满意 |
| 9 | `/stop` / `Ctrl+C` | 打断当前任务 | agent 跑偏了想叫停 |
| 10 | `/help` | 查全部命令 | 不记得某个命令时 |

### 1 · `/new` vs `/reset`

```
> /new          # 存档当前对话并开新的
> /reset        # 清空当前对话(一般等价,语义细微差别按实现)
```

**不会清记忆**(memory)。只清当前会话上下文。

### 2 · `/model`

```
> /model                                          # 交互式列表
> /model openrouter/anthropic/claude-opus-4-6     # 直接切
```

**对话历史保留**。下一轮用新模型继续。

### 3 · `/personality`

```
> /personality              # 列出所有个性
> /personality code-reviewer  # 切换
> /personality none          # 清除
```

### 4 · `/memory`

```
> /memory                   # 查看当前记忆
> /memory add "我偏好 4 空格缩进"
> /memory list              # 列出所有条目
```

也可以直接编辑 `~/.hermes/memory/MEMORY.md`。

### 5 · `/skills`

```
> /skills                   # 浏览所有技能(含 Skills Hub)
> /skills search python     # 搜索
> /<skill-name>             # 直接触发某个技能
```

Skills Hub:[agentskills.io](https://agentskills.io) —— 社区技能库。

### 6 · `/compress`

```
> /compress                 # 自动压缩整段对话
> /compress 代码审查部分     # 带 focus:只压缩这个主题
```

当 `/usage` 显示上下文快满时用。**不会丢重要信息**,但会把老的冗余段合并。

### 7 · `/usage`

```
> /usage
```

输出类似:
```
Context: 45,234 / 200,000 tokens (22%)
Session cost: $0.18
Cache hit rate: 87%
```

### 8 · `/undo` / `/retry`

```
> /undo    # 撤销上一轮(user + assistant 两条都移除)
> /retry   # 用同样的 user message 再请求一次
```

**`/retry` 在想看不同回答时特别好用**(尤其配合 temperature > 0 的模型)。

### 9 · `/stop` / `Ctrl+C`

- CLI 里:**`Ctrl+C` 直接打断**
- 消息平台里:发送 **`/stop`** 或直接发新消息打断

打断后对话保留,你可以"**别做那个,先看别的**"。

### 10 · `/help`

```
> /help                    # 所有命令
> /help memory             # 某个命令的详细帮助
```

---

## 🆕 v0.9 - v0.10 新增的值得一学的命令

除了上面的 Top 10,这些是最近版本加入的,掌握了能立刻少写很多字:

| 命令 | 类型 | 做什么 |
|---|---|---|
| `/fast` | slash | 切换 Fast Mode(OpenAI / Anthropic 优先队列,延迟更低) |
| `/debug` | slash | 一键生成诊断报告(会话、环境、配置) |
| `/status` | slash | 查看当前会话 / agent / 模型状态概览 |
| `/statusbar` | slash | 开关 TUI 状态栏 |
| `/yolo` | slash | 放飞模式 —— 跳过所有命令审批(**仅在沙箱环境用**) |
| `/compact` / `/compress` | slash | 上下文压缩(`/compress <focus>` 可带主题) |
| `/snapshot` · `/rollback` | slash | 会话快照与回滚 |
| `/resume` | slash | 恢复之前的会话 |
| `/reload-mcp` | slash | 重载 MCP 服务器,不用重启 agent |
| `hermes backup` | CLI | 备份配置 / 会话 / 技能 / 记忆到单个归档 |
| `hermes import` | CLI | 从备份归档恢复(跨机迁移神器) |
| `hermes debug share` | CLI | 诊断报告上传到 pastebin,方便求助 |
| `hermes plugins` | CLI | 插件系统管理(v0.9+ 可插拔 Context Engine 等) |
| `hermes dashboard` | CLI | 启动本地 Web Dashboard(浏览器里管理 agent) |
| `hermes skills reset` | CLI | 把内置技能恢复到干净状态 |

!!! tip "特别推荐 `hermes backup`"
    换机器、升级系统、折腾前 —— **先 `hermes backup -o ~/hermes-snapshot.tar.gz`**。
    出问题时 `hermes import ~/hermes-snapshot.tar.gz` 几秒复原。

---

## 组合场景:一次典型会话

```bash
# 1. Shell 里启动(用便宜模型)
$ hermes --model openrouter/google/gemini-2.5-flash

# 2. 让它做个简单任务
> 帮我 ls 一下 ~/projects,选出看起来最近改过的

# 3. 发现需要更强模型做代码审查
> /model openrouter/anthropic/claude-sonnet-4-6

# 4. 深入审查
> 审查一下 myproject/auth.py 有没有安全问题

# 5. 用太久上下文变大,压缩一下
> /compress

# 6. 记住新事实
> 记住一下:我的主项目是 ~/projects/myproject

# 7. 查用量
> /usage

# 8. 结束
> /exit
```

---

## 坑点

### 坑 1 · 分不清 `hermes xxx` 和 `/xxx`

!!! warning "最常见的新手困惑"
    `hermes model` 在 **shell 里**跑;`/model` 在 **对话里**跑。

    它们功能类似但**不是一回事** —— shell 版改的是默认配置(写入 config.yaml),slash 版只改当前会话(对话结束就回默认)。

### 坑 2 · `/new` 不清记忆

`/new` 只是开新会话,memory 跨会话持久。想清记忆要:
```bash
rm ~/.hermes/memory/MEMORY.md       # 暴力
# 或
> /memory clear                      # 温和(agent 侧)
```

### 坑 3 · 切模型后行为突变

不同模型工具调用能力差很大:
- Claude 系列擅长调工具
- 有些 chat-only 模型几乎不主动调工具

**切完模型后发现 agent 变"呆"**,通常是模型不对路。回切或换更大的。

### 坑 4 · `/compress` 破坏了我关心的内容

`/compress` 偶尔会把某个细节压掉。预防:
- 压缩前用 `/compress <focus>` 告诉它保留哪部分
- 或提前用 `/memory add` 把关键事实记下,这样不靠对话历史也能找回

---

## 第一部完结检查清单

能独立完成下面所有事情,你完成了第一部:

- [ ] 用 `hermes setup` 配置好第一个模型
- [ ] 跑 `hermes` 进入 TUI,发出第一条消息
- [ ] 看到 agent 调用至少一个工具
- [ ] 用 `/model` 在会话中切换模型
- [ ] 用 `/memory` 添加一条事实,重启后 agent 仍能引用
- [ ] 用 `/skills` 浏览技能库,触发一个
- [ ] 用 `/compress` 压缩一次上下文
- [ ] 用 `hermes doctor` 能看懂输出
- [ ] 知道 Profile 是什么,用 `hermes -p xxx` 启动过另一个实例
- [ ] 能说出**五大支柱**每一根是什么

🎉 全部打勾?你已经是合格的 Hermes **基础用户**。

---

接下来可以:

- **继续往深走** → [第二部 · 日常](../part-2-daily/index.md)(深入技能、记忆、会话搜索、模型切换策略)
- **开始接 Telegram / Discord** → [第三部 · 精通](../part-3-mastery/index.md)(消息网关、Profile 进阶、定时任务)
- **想写代码扩展** → [第四部 · 内核](../part-4-internals/index.md)(架构、加工具、加命令)
- **要做研究** → [第五部 · 研究](../part-5-research/index.md)(批处理、轨迹、RL)
