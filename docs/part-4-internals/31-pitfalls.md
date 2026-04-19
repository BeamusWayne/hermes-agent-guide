---
hermes_version: 0.10.0
title: 已知坑点与防御
---

# 31. 已知坑点与防御

本章是**项目维护者踩过的坑汇总**。每一条后面都有**代价** —— 有的是几个小时 bug 追,有的是 PR 合并后线上事故。

**写代码前扫一遍**,可以少走弯路。

---

## 路径 / Profile 相关

### ⚠️ 不要 hardcode `~/.hermes/`

用 `get_hermes_home()`(代码)/ `display_hermes_home()`(用户 facing)。

**代价**:PR #3575 之前有 5 个 bug,profile 切换后某些工具还是写到默认 profile。

### ⚠️ Module-level 常量要小心

```python
# agent/display.py
DISPLAY_DIR = get_hermes_home() / "display"  # 模块级求值,import 时定格
```

这在**通过 hermes 命令启动**时没问题(profile 已 apply)。但如果测试 / 脚本直接 import,可能拿到错的 HOME。

**对策**:
- 优先在函数里调用 `get_hermes_home()`
- 或者模块级 OK,但**测试务必用 fixture 重定向**

### ⚠️ `_get_profiles_root()` 必须用 `Path.home()`

不能改成 `get_hermes_home()`。否则 `hermes -p A profile list` 看不到其他 profile。

---

## Cache / 系统提示相关

### ⚠️ 不要 mid-session 改 system prompt

- 切 personality 必破缓存
- 重载 memory 必破缓存
- 改 toolset 必破缓存

**详见第 27 章**。

### ⚠️ 不要在 system prompt 里放动态内容

时间戳、运行计数器、任何每次调用都不同的东西 —— 破缓存。

### ⚠️ 工具 schema description 不要引用其他工具名

```python
# ❌ 坏
description: "... Prefer web_search for queries ..."
```

那个 `web_search` 可能当前不可用,LLM 被误导。

**对策**:在 `model_tools.py` 的 `get_tool_definitions()` 里**动态**拼接 cross-reference,只在相关工具真可用时加。

---

## UI / 显示相关

### ⚠️ 不要用 `simple_term_menu`

在 tmux / iTerm2 下**有 ghosting bug**(滚动时出现残影)。

**对策**:用 stdlib 的 `curses`。`hermes_cli/tools_config.py` 是示范。

### ⚠️ 不要用 `\033[K`(ANSI erase-to-EOL)

在 `prompt_toolkit` 的 `patch_stdout` 下会**以字面 `?[K` 泄漏**到屏幕。

**对策**:用**空格填充**:
```python
# ❌
print(f"\r{line}\033[K")

# ✅
print(f"\r{line}{' ' * pad}")
```

### ⚠️ emoji / 字体依赖终端

`kawaii_spinner` 等需要终端**字体支持**。老终端显示 `?`。

**对策**:
- 默认 skin 用可移植的字符
- 高级 skin 记录字体依赖

---

## Agent Loop 相关

### ⚠️ `_last_resolved_tool_names` 是 process-global

`model_tools.py` 有个 process-level 全局变量。`_run_single_child()` in `delegate_tool.py` 会**保存并恢复**这个全局,围绕 subagent 执行。

如果你写新代码读这个全局,**它在子 agent 运行期间可能是 stale 的**。

### ⚠️ 工具调用对不能拆散

Assistant 的 `tool_call` + 对应 `tool` 结果必须**作为一对**保留或一起压缩。

`context_compressor.py` 的 `_group_tool_pairs` 处理。自己写逻辑别破坏。

### ⚠️ 打断后的 messages 状态

用户 Ctrl+C 后,如果最后一条是 `user` 但没对应 `assistant`,下次 continue 会失败。

**对策**:CLI / gateway 补一条 `{"role": "assistant", "content": "[interrupted]"}`。

---

## 消息网关相关

### ⚠️ 所有平台操作**必须 async**

一个阻塞操作会拖死整个 gateway。

### ⚠️ Bot 别回 Bot

```python
if event.is_bot:
    return
```

否则两个 bot 在群里相互触发可能无限循环。

### ⚠️ Telegram 命令名 ≤ 32 字符

超了**被截断**,可能跟别的命令撞。

### ⚠️ Discord Message Content Intent 不开 bot 看不到消息正文

**现象**:bot 响应 `/help`,但 @提及 后不响应。

**对策**:Developer Portal **手动开** Message Content Intent。

### ⚠️ Slack Socket Mode 不支持 Enterprise Grid

企业版必须走 HTTP Events。

---

## 测试相关

### ⚠️ 测试**必须**用 `_isolate_hermes_home` fixture

autouse 已经生效,但如果你手动绕开(比如 `monkeypatch.delenv("HERMES_HOME")`),会污染用户真实 `~/.hermes/`。

### ⚠️ LLM 调用必须 mock

`no_real_llm` fixture 已经 block,但 indirect 路径可能绕过。看测试报 `RuntimeError: Real LLM call blocked`,是好事 —— mock 不全。

### ⚠️ Profile 测试额外 mock `Path.home()`

因为 `_get_profiles_root()` 用 `Path.home()`。不 mock 它就读到你真实 home。

```python
monkeypatch.setattr(Path, "home", lambda: tmp_path)
```

---

## 安全 / 审批相关

### ⚠️ allowed_users **必填**(生产环境)

`allowed_users: []` 不等于 "我自己",而是 "任何人"。

**对策**:生产部署前在 doctor / setup 时强制检查。

### ⚠️ DM Pairing 不是万能

Pairing 码**不过期时间够长**可被重放。

**对策**:`dm_pairing_ttl: 300`(5 分钟)一次性使用。

### ⚠️ YOLO 模式 = 信任爆炸

`/yolo` 自动批所有命令,包括删库跑路。

**对策**:
- 只在 sandbox 环境用
- CI 跑测试时可以 yolo
- 真机绝对不
- v0.10+ 在 `/yolo` 前加二次确认

### ⚠️ Memory 里的 prompt injection

有人在对话里诱导 agent "把 `ignore previous instructions` 写进 memory",之后每次加载就 break agent。

**对策**:
- `scan_memory` 自动检测,已开
- 定期 review memory 文件
- 不要让不信任的人直接对话

### ⚠️ Tool 参数 injection

```python
# ❌ 危险
def my_tool(cmd: str):
    os.system(cmd)   # agent 传什么你都执行

# ✅ 安全
def my_tool(cmd: str):
    # 白名单命令
    if not cmd.startswith(("ls ", "pwd", "echo ")):
        return json.dumps({"error": "not allowed"})
    subprocess.run(shlex.split(cmd), ...)
```

---

## Provider 相关

### ⚠️ OpenRouter 偶发 429

作为中间层容量敏感。配 fallback:

```yaml
providers:
  routing:
    fallback_model: anthropic/claude-sonnet-4-6  # 官方源兜底
```

### ⚠️ Gemini `Authorization: Bearer` 不兼容

Hermes v0.10 起**自动 strip** Gemini 请求的 Authorization header(HTTP 400 就是这个)。

老版本如果看到 400,先升级。

### ⚠️ DeepSeek / Kimi / GLM 的 `<think>` 泄漏

Reasoning 模型的 thinking 段偶尔以 `<think>...</think>` 文本形式泄到响应。

**对策**:v0.9+ 自动 strip。老版本升级。

### ⚠️ Custom provider 要注册进 `/model` 列表

加新自托管端点时,除了 `.env` 配 key,config 里也要 register,否则 `/model` 下拉看不到。

---

## 依赖 / 环境相关

### ⚠️ `hermes update` 不重启进程

update 只装新包,**跑着的 gateway / CLI 还是老版本**。

**对策**:update 后手动 `hermes gateway restart`。

### ⚠️ faster-whisper 在 Android 不兼容

Termux 上 `faster-whisper` 装不上(依赖 ctranslate2 非 Android wheel)。

**对策**:v0.9+ Termux 用专门的 `.[termux]` extra,不包含 voice。

### ⚠️ uv 和 pip 混用

一会儿 `pip install`,一会儿 `uv pip install`,可能产生**不一致的 lockfile**。

**对策**:一个项目里**统一用 uv**。

---

## 升级 / 迁移相关

### ⚠️ `_config_version` 不匹配不要手修

让 Hermes 自动 migration 跑。手改会破坏数据结构。

### ⚠️ 备份文件里有明文 API key

`hermes backup` 包含 `.env`。

**对策**:
- 存加密卷
- git 管理 backup 目录加密
- 或用 `--exclude-secrets`(future feature)

### ⚠️ 迁移后 cron 立即触发全部

scheduler 检测到"错过的任务",会补跑。

**对策**:
- 启动前 `hermes cron pause-all`
- 升完检查再 `resume-all`

---

## 日志 / 观测相关

### ⚠️ 日志里的敏感信息

默认 `RedactingFormatter` 脱敏常见模式(`KEY=...`、`token: ...`)。

**但不是完美的**:
- 自定义 key 名 pattern 可能漏
- Base64 过的 secret 漏

**对策**:
- 按需扩展 redaction pattern
- 日志文件权限 600
- 不 commit 日志

### ⚠️ Debug 模式不要长期开

`HERMES_DEBUG=1` 打印完整 prompt / response —— **含敏感 token**,磁盘爆炸,且有泄露风险。

**对策**:只临时 debug 时开,结束关。

---

## 给 PR 贡献者的自查清单

PR 合并前对照这个清单。每一条都是**一个踩过的坑**:

- [ ] 我没 hardcode `~/.hermes/` —— 用 `get_hermes_home()`
- [ ] 我没 mid-session 改 system prompt / toolset / memory
- [ ] 我的工具 schema description 不 cross-reference 其他工具
- [ ] 我没用 `simple_term_menu`
- [ ] 我没用 `\033[K` ANSI escape
- [ ] 我的 async 代码真的全 async(没有阻塞调用)
- [ ] 我的工具 handler 返回 JSON 字符串,不 raise
- [ ] 我的测试没调真实 LLM / 真实网络
- [ ] 我写了新测试,全 3000 个测试跑过
- [ ] 我的 PR 描述提到了破 cache 风险(如果涉及)
- [ ] 我没 hardcode provider 名(未来要加新 provider 就要改一堆)
- [ ] 我的日志 / 输出过了脱敏

---

## 历史 bug 的 PR 编号(学习用)

有兴趣读 bug 怎么被修的,按编号在 GitHub 搜:

- **PR #3575** —— 5 个 hardcode `~/.hermes/` bug
- **PR #6395 / #6453** —— 上下文压缩改进
- **PR #7893** —— Gemini Authorization header bug
- **PR #7930** —— tool interrupt 跨 session leak
- **PR #7983** —— 防 agent mid-task 停止
- **PR #8017** —— `/compress <focus>` 引入
- **PR #8107** —— 压缩防止回答陈旧问题
- **PR #8258** —— 压缩用 live session model
- **PR #8562** —— strip Gemma4 `<thought>` 标签

---

## 第四部完结检查清单

能独立完成下面所有事情,你完成了第四部:

- [ ] 能在 hermes-agent 目录里快速定位核心 20+ 文件
- [ ] 读过 `run_agent.py` 的 `run_conversation`
- [ ] 给 Hermes 加过一个自己的工具(3 文件改动)
- [ ] 给 Hermes 加过一个 slash 命令(CommandDef + 2 handlers)
- [ ] 看过 `BasePlatform` 抽象,能设计新平台适配器
- [ ] 理解 prompt caching 的 5 条规则
- [ ] 写过 profile-safe 的新代码,用 `get_hermes_home()`
- [ ] 读过 `context_compressor.py` 知道压缩算法
- [ ] 会写 pytest 测试,用 autouse fixtures
- [ ] 本章坑点扫过至少一遍

🎉 全部打勾?你已经是 Hermes 的**贡献者级别**。

---

接下来可以:

- **做研究 / RL 训练** → [第五部 · 研究](../part-5-research/index.md)
- **查参考** → [附录](../appendix/index.md)
- **真的开始贡献 PR** → [CONTRIBUTING.md](https://github.com/NousResearch/hermes-agent/blob/main/CONTRIBUTING.md)
