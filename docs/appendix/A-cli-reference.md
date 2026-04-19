---
hermes_version: 0.10.0
title: CLI 命令完整参考
---

# A. CLI 命令完整参考

所有 `hermes xxx` 子命令的速查。字段按常用程度排序。

!!! tip "`hermes <cmd> --help` 总是最全"
    这里列主要参数。完整看 `hermes <cmd> --help`。

---

## 启动与会话

### `hermes`(启动 TUI)

```bash
hermes [options]
```

| 参数 | 作用 |
|---|---|
| `-p, --profile <name>` | 使用指定 profile |
| `--model <id>` | 临时切主模型 |
| `--resume <session-id>` | 续接某会话 |
| `--skip-memory` | 不加载 memory |
| `--skip-context-files` | 不加载 AGENTS.md |
| `--quiet` | 静默模式 |
| `--fast` | 启动即 Fast Mode |
| `--save-trajectories` | 保存完整轨迹到 `~/.hermes/trajectories/` |
| `--dev` | TUI 开发模式(ink 前端热重载) |
| `--version` | 显示版本 |

### `hermes chat "<message>"`(一次性)

非交互模式,跑一条消息后退出。

```bash
hermes chat --model gpt-4o "帮我 ls 一下"
```

---

## 设置向导

### `hermes setup`

全功能交互向导。**首次安装后跑**。

```bash
hermes setup [--quick]
```

`--quick` 只配最关键(模型 + API key)。

### `hermes doctor`

诊断当前安装状态。

```bash
hermes doctor [--verbose] [--security]
```

| 选项 | 作用 |
|---|---|
| `--verbose` | 每一项详细原因 |
| `--security` | 安全专项检查 |

---

## 模型管理

### `hermes model`

```bash
hermes model                        # 交互式
hermes model <provider/model-id>    # 直接设默认
hermes model --list                 # 列出已配置的
```

在交互菜单里还可以:
- Configure auxiliary models
- 添加 custom provider(自托管端点)
- Login OAuth 提供商(Gemini CLI、Nous Portal)

### `hermes auth`

OAuth 流程管理。

```bash
hermes auth add nous --type oauth   # 登录 Nous Portal
hermes auth add gemini-cli          # 登录 Gemini CLI
hermes auth list                    # 列出已登录的
hermes auth remove <name>
```

---

## 工具管理

### `hermes tools`

交互式开关工具,按平台分。

### `hermes skills`

```bash
hermes skills list              # 本地已安装
hermes skills search <query>    # 搜 Skills Hub
hermes skills install <name>    # 从 Hub 装
hermes skills reset             # 内置技能恢复出厂
hermes skills remove <name>
```

### `hermes plugins`

```bash
hermes plugins list
hermes plugins enable <name>
hermes plugins disable <name>
hermes plugins install <git-url>
hermes plugins uninstall <name>
hermes plugins info <name>
```

---

## 配置

### `hermes config`

```bash
hermes config get [path]            # 点号路径,如 display.skin
hermes config set <path> <value>
hermes config unset <path>
hermes config edit                  # 直接编辑 YAML
```

---

## Profile 多实例

### `hermes profile`

```bash
hermes profile list
hermes profile create <name>
hermes profile remove <name>
hermes profile show <name>
hermes profile rename <old> <new>
hermes profile set-default <name>
```

---

## 消息网关

### `hermes gateway`

```bash
hermes gateway setup                 # 交互式配平台
hermes gateway start [-d]            # -d 为 daemon
hermes gateway stop
hermes gateway restart
hermes gateway status
hermes gateway logs [--follow]
hermes gateway list                  # 已配置的平台
```

---

## 定时任务

### `hermes cron`

```bash
# 创建
hermes cron add [options] "<prompt>"

# 管理
hermes cron list [--active]
hermes cron show <id>
hermes cron pause <id>    / pause-all
hermes cron resume <id>   / resume-all
hermes cron remove <id>
hermes cron edit <id>

# 执行
hermes cron test <id>     # 立刻跑一次
hermes cron next <id>     # 下次触发时间
hermes cron history <id>  # 历史
hermes cron logs <id>
hermes cron start         # 只启 scheduler(不启 gateway)
```

`add` 的主要 options:

| 参数 | 作用 |
|---|---|
| `--at "<time>"` | "08:00" / "Mon 09:00" / "1st 10:00" |
| `--every <interval>` | "15m" / "2h" / "1d" |
| `--cron "<expr>"` | 标准 cron 表达式 |
| `--once "<ISO>"` | 一次性 |
| `--deliver <target>` | telegram / discord / slack / email / log |
| `--deliver-to <list>` | 多目的地 |
| `--retry N` | 失败重试次数 |
| `--retry-delay <dur>` | 重试间隔 |
| `--timeout <dur>` | 超时 |
| `--only-on-change` | 只在结果变化时通知 |
| `--model <id>` | 指定模型 |

---

## 备份 / 迁移

### `hermes backup`

```bash
hermes backup -o <file.tar.gz>
    [--profile <name>]
    [--exclude sessions,memory,...]
    [--include-only memory,skills,...]
```

### `hermes import`

```bash
hermes import <file.tar.gz>
    [--profile <name>]            # 恢复到指定 profile
    [--overwrite]                  # 覆盖冲突(不交互)
    [--dry-run]                    # 只预览
```

### `hermes claw migrate`(从 OpenClaw 迁移)

```bash
hermes claw migrate              # 交互式
hermes claw migrate --dry-run
hermes claw migrate --preset user-data   # 不含 secrets
hermes claw migrate --overwrite
```

---

## Web Dashboard(v0.9+)

### `hermes dashboard`

```bash
hermes dashboard                 # 启动,默认 localhost:8765
hermes dashboard --port 9090
hermes dashboard --host 0.0.0.0  # ⚠ 公网暴露要小心
hermes dashboard --no-browser
hermes dashboard stop            # 从另一个 shell
hermes dashboard reset-token
```

---

## 升级 / 调试

### `hermes update`

```bash
hermes update            # 升 stable
hermes update --pre      # 升 pre-release
```

### `hermes debug`

```bash
hermes debug show        # 打印诊断信息
hermes debug share       # 上传到 pastebin,给个 URL
```

---

## 数据操作

### `hermes sessions`

```bash
hermes sessions list                # 所有会话
hermes sessions show <id>           # 看完整对话
hermes sessions search <query>      # FTS5 搜
hermes sessions delete <id>
hermes sessions export <id> -o file.md
```

### `hermes memory`

```bash
hermes memory show                  # 看 MEMORY + USER
hermes memory edit                  # 打开 $EDITOR
hermes memory clear --confirm
```

---

## 研究用

### `batch_runner.py`

```bash
python batch_runner.py --input tasks.jsonl --output out/ --model ...
```

### `trajectory_compressor.py`

```bash
python trajectory_compressor.py --input in/ --output sft.jsonl
```

### `mini_swe_runner.py`

```bash
python mini_swe_runner.py --dataset mini-swe-bench --model ...
```

### `rl_cli.py`

```bash
python rl_cli.py --env tool_use_chain --policy Llama-3-8B ...
```

---

## 全局选项

很多子命令都支持:

| 选项 | 作用 |
|---|---|
| `-p, --profile` | 指定 profile |
| `--help` | 显示帮助 |
| `--version` | 显示版本 |
| `--verbose` | 更详细输出 |
| `--quiet` | 更安静 |
| `--no-color` | 禁用颜色(适合管道 / CI) |

---

## 环境变量 override

许多 config 项可以用环境变量临时 override:

```bash
HERMES_MAX_ITERATIONS=200 hermes       # 临时改
HERMES_DEBUG=1 hermes                  # 开 debug
HERMES_PROFILE=work hermes             # 等价于 -p work
```

完整清单见 [附录 C](C-env-vars.md)。
