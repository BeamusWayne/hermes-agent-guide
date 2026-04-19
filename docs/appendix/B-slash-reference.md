---
hermes_version: 0.10.0
title: Slash 命令完整参考
---

# B. Slash 命令完整参考

所有 `/xxx` 命令。**在 CLI TUI 或消息平台对话框里用**。

!!! info "字段说明"
    - **别名**:任意写法都行
    - **场景**:CLI / Gateway / 两者
    - **参数**:`<x>` 必填,`[x]` 可选

---

## Session(会话)

| 命令 | 别名 | 参数 | 场景 | 做什么 |
|---|---|---|---|---|
| `/new` | `/reset` | - | 两 | 开新对话,保留 memory |
| `/compress` | `/compact` | `[focus]` | 两 | 压缩上下文,可带主题 |
| `/snapshot` | - | `[name]` | 两 | 会话存档点 |
| `/rollback` | - | `[snapshot]` | 两 | 回到某存档 |
| `/resume` | - | `<session-id>` | 两 | 恢复之前的会话 |
| `/history` | - | - | 两 | 看当前会话消息列表 |
| `/undo` | - | - | 两 | 撤销最后一轮 |
| `/retry` | - | - | 两 | 重试最后一个 user 消息 |
| `/stop` | `/interrupt` | - | 两 | 打断当前任务 |
| `/save` | - | `[name]` | 两 | 保存会话 |
| `/title` | - | `<new-title>` | 两 | 改会话标题 |
| `/branch` | - | `[name]` | 两 | 从当前点分支出新会话 |
| `/clear` | - | - | 两 | 清屏(UI 操作,不清对话) |
| `/exit` | `/quit` | - | CLI | 退出 |

---

## Configuration(配置)

| 命令 | 别名 | 参数 | 场景 | 做什么 |
|---|---|---|---|---|
| `/model` | - | `[provider/model]` | 两 | 切主模型 |
| `/provider` | - | `[name]` | 两 | 切 provider |
| `/personality` | - | `[name]` | 两 | 切个性 |
| `/skin` | - | `[name]` | CLI | 切皮肤 |
| `/config` | - | `[key] [value]` | 两 | 读写配置 |
| `/reasoning` | - | `[none/low/med/high]` | 两 | reasoning 层级 |
| `/fast` | - | `[on/off]` | 两 | Fast Mode 开关 |
| `/verbose` | - | `[on/off]` | 两 | 输出详细度 |
| `/sethome` | - | `<path>` | GW | 设置 gateway 工作目录 |
| `/setup` | - | - | CLI | 对话内切到 `hermes setup` |

---

## Memory / Context

| 命令 | 别名 | 参数 | 场景 | 做什么 |
|---|---|---|---|---|
| `/memory` | - | `[add/show/clear] [content]` | 两 | 管理 memory |
| `/insights` | - | `[--days N]` | 两 | 看对话洞察 / 统计 |
| `/usage` | - | - | 两 | token 用量 + 成本 |
| `/gquota` | - | - | 两 | (Gateway)配额 |

---

## Tools & Skills

| 命令 | 别名 | 参数 | 场景 | 做什么 |
|---|---|---|---|---|
| `/tools` | - | - | 两 | 列出可用工具 |
| `/toolsets` | - | - | 两 | 列出 toolsets + 启用状态 |
| `/skills` | - | `[search/install/触发]` | 两 | 技能菜单 / 触发 |
| `/<skill-name>` | - | `[args]` | 两 | 触发某技能(e.g. `/my-pr-review`) |
| `/image` | - | `<prompt>` | 两 | 直接生成图 |
| `/browser` | - | `<url>` | 两 | 直接打开浏览器任务 |
| `/voice` | - | `[on/off]` | CLI | 语音模式 |
| `/reload-mcp` | - | - | 两 | 重载 MCP 服务器 |
| `/background` | `/bg` | - | CLI / GW* | 后台进程管理 |
| `/queue` | - | - | 两 | 看消息队列 |

`GW*` = config 开关打开时 gateway 里才可用。

---

## Info(信息)

| 命令 | 别名 | 参数 | 场景 | 做什么 |
|---|---|---|---|---|
| `/help` | - | `[cmd]` | 两 | 命令帮助 |
| `/commands` | - | - | 两 | 列出所有命令 |
| `/platforms` | - | - | CLI | 看各平台状态 |
| `/status` | - | - | 两 | 会话 / agent 状态 |
| `/statusbar` | - | `[on/off]` | CLI | TUI 状态栏 |
| `/debug` | - | - | 两 | 诊断报告 |
| `/agents` | - | - | 两 | 当前活跃子 agent |
| `/paste` | - | - | CLI | 多行粘贴模式 |
| `/copy` | - | `[n]` | CLI | 复制第 n 条响应 |

---

## Dangerous(谨慎用)

| 命令 | 别名 | 参数 | 场景 | 做什么 |
|---|---|---|---|---|
| `/yolo` | - | `[on/off]` | 两 | 自动批所有 approval(仅 sandbox!) |
| `/approve` | - | - | 两 | 批准待批准的命令 |
| `/deny` | - | - | 两 | 拒绝 |
| `/btw` | - | `<note>` | 两 | 临时提示 agent,不存入 memory |
| `/restart` | - | - | 两 | 重启 agent(保留 session)|

---

## 技巧 / 特殊用法

### 空命令补全

TUI 里打 `/` 然后 `Tab`,列出所有可用命令。

### 按类别 help

```text
> /help
[ 显示所有 ]

> /help memory
[ memory 命令的详细帮助 ]
```

### 别名用法

```text
> /reset       # 等价 /new
> /bg          # 等价 /background
> /compact     # 等价 /compress
```

### 连续命令

```text
> /new
> /personality code-reviewer
> /model claude-opus-4-7
```

三条独立命令,**不是一条**(不用逗号分号)。

### Slash 命令**不是工具**

LLM 看不到 slash 命令,调不了它们。要让 agent 能"按某流程走",做成 **skill**(第 8 章)。

---

## 平台差异

某些命令平台独有:

- **CLI only**:`/platforms`、`/paste`、`/copy`、`/skin`、`/voice`、`/statusbar`、`/setup`、`/exit`
- **Gateway only**:`/sethome`、`/gquota`
- **两者**:其余

---

## 新版本可能加的命令

这份清单基于 v0.10.0。`/help` 总是真相来源 —— 查看你当前实际可用。
