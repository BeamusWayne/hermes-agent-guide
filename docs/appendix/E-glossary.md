---
hermes_version: 0.10.0
title: 术语表
---

# E. 术语表

按字母顺序。**简短定义 + 跳转**。

---

## ACP(Agent Client Protocol)

支持 IDE(VS Code / Zed / JetBrains)作为 Hermes 客户端的协议。详见 `acp_adapter/` 目录。

---

## Activity Bar(活动栏)

CLI 中用 `┊` 前缀显示工具输出的实时区域。

---

## Agent Loop

`AIAgent.run_conversation` 里的核心循环:调 LLM → 有工具调用就执行 → 循环,直到 LLM 返回最终文本。见 [第 23 章](../part-4-internals/23-aiagent-class.md)。

---

## AGENTS.md

项目级 context 文件,Hermes 启动会话时自动读取并注入。是项目给 AI 的「使用说明」。

---

## Allowed Users

消息平台的 allow list,限制谁能跟 bot 对话。**生产环境必填**。

---

## Atropos

Nous Research 的 RL 框架。见 [第 34 章](../part-5-research/34-atropos-rl.md)。

---

## Auxiliary Model

辅助模型。用于上下文压缩、会话搜索总结等**辅助任务**,通常选便宜快的。v0.10 默认沿用主模型(`auto` 模式)。

---

## BasePlatform

消息网关平台适配器的基类。新平台都继承它。见 `gateway/platforms/base.py`。

---

## Batch Runner

`batch_runner.py` —— 并行跑多个 agent 任务的工具。研究 / SFT 数据生成用。

---

## BlueBubbles

iMessage 桥接方案。Hermes 通过它支持 iMessage。

---

## Callback

AIAgent 接受的回调函数集,由外层(CLI / gateway)注入,用于处理 approval / clarify / 进度通知等。

---

## Canary(对话里的)

对比测试手段 —— 给两个模型同一任务,看哪个表现好。

---

## Cron(Hermes 的)

内置定时任务调度器,跑在 gateway 里。自然语言任务 + 跨平台投递。见 [第 15 章](../part-3-mastery/15-cron-deep-dive.md)。

---

## Context Engine

v0.9+ 的可插拔上下文组装接口。允许用插件完全自定义 context 行为。

---

## Context File

项目根目录的 AGENTS.md / CLAUDE.md 等,会被 Hermes 注入到对话初始上下文。

---

## COMMAND_REGISTRY

`hermes_cli/commands.py` 里的 slash 命令单源头。所有下游(CLI / gateway / Telegram 菜单等)都从这里派生。

---

## Daytona

terminal 后端之一,serverless 持久化环境 —— 闲时休眠,用时唤醒。

---

## Delegate

通过 `delegate_task` 工具 spawn 子 agent。子 agent 独立上下文,并行跑。

---

## DM Pairing

公共频道里 bot 通过**私聊发配对码**来验证用户身份的安全机制。Slack / Discord 支持。

---

## Frozen Snapshot(冻结快照)

Hermes 设计模式:memory / context files / personality / subdirectory hints 在 session 启动时**一次性读取并冻结**,整个会话里不变。**为了保 prompt cache**。

---

## FTS5

SQLite 的全文搜索扩展。Hermes 用它索引所有 session,供 `session_search` 工具回溯。

---

## Gateway

`hermes gateway start` 的消息网关进程。跑在一个进程里,同时连所有启用的消息平台。

---

## HERMES_HOME

环境变量。决定数据目录位置。profile 切换就是改这个变量。**所有代码通过 `get_hermes_home()` 读取它**。

---

## Honcho

可选的用户建模服务。给 agent 增加"用户画像"维度。

---

## Intent(Discord)

Discord Bot API 的权限声明。**Message Content Intent** 是让 bot 能读消息正文的关键 intent。

---

## Iteration Budget

细粒度的 token 预算,比 `max_iterations` 更细。限制单次对话总消耗。

---

## MCP(Model Context Protocol)

Anthropic 提出的开放协议,标准化 agent 与外部工具 / 数据的接口。Hermes 是 MCP 客户端。见 [第 19 章](../part-3-mastery/19-mcp-dashboard.md)。

---

## Memory

Hermes 的持久化事实存储。两文件:**MEMORY.md**(agent 的笔记)+ **USER.md**(关于用户)。见 [第 7 章](../part-2-daily/07-memory-deep-dive.md)。

---

## max_iterations

单次对话的工具循环上限。防死循环。默认 90。

---

## Nudge(Memory Nudge)

Hermes 周期性提示 agent 考虑写 memory 的机制。

---

## OAuth Credential Pool

多 OAuth token 轮流用的机制,分散限流压力。

---

## OpenClaw

Hermes 的前身项目。`hermes claw migrate` 用于从 OpenClaw 迁移。

---

## Personality

调整 agent 说话风格的文本层。见 [第 11 章](../part-2-daily/11-personality.md)。

---

## Plugin

v0.9+ 的可插拔 Python 代码,能改 Hermes 本体行为。见 [第 21 章](../part-3-mastery/21-security-plugins.md)。

---

## Platform

消息适配的具体类型(Telegram / Discord / Slack ...)。每种在 `gateway/platforms/` 下有一个文件。

---

## Profile

完全隔离的 Hermes 实例配置 + 数据。多用户 / 多用途场景用。见 [第 16 章](../part-3-mastery/16-profile-backup.md)。

---

## Prompt Cache

Anthropic 的前缀缓存机制。对 Hermes 影响巨大,破坏它成本飙升。见 [第 27 章](../part-4-internals/27-prompt-caching.md)。

---

## Reasoning Model

支持内部 thinking 段的模型(DeepSeek-R、Claude Opus thinking、Kimi K2 等)。输出前有额外推理。

---

## Registry(Tool Registry)

`tools/registry.py`。所有工具在此注册 schema + handler。

---

## RL(Reinforcement Learning)

见 [第 34 章](../part-5-research/34-atropos-rl.md)。

---

## Session

一次连续的对话。存在 `sessions.db` 里。跨平台共享同一 session(只要是同一用户)。

---

## SFT(Supervised Fine-Tuning)

用 (prompt, answer) 对直接训练模型。agent 轨迹是常见 SFT 数据。

---

## Skill

可复用的流程模板。目录形式:`~/.hermes/skills/<name>/SKILL.md`。见 [第 8 章](../part-2-daily/08-skills-deep-dive.md)。

---

## Skills Hub

[agentskills.io](https://agentskills.io) —— 社区技能市场,Hermes 原生集成。

---

## Slash Command

斜杠开头的对话内命令。与工具不同 —— LLM 看不到,是用户用的。

---

## Subagent(子 agent)

通过 `delegate_task` 工具 spawn 的独立 agent 实例。用于并行工作、隔离上下文。

---

## Token Lock

防止多 profile 用同一 bot token 冲突的文件锁。v0.9+。

---

## Tool

LLM 可调用的函数。JSON schema + Python handler。在 `tools/registry.py` 注册。

---

## Toolset

一组 tools 的集合。按平台区分哪些开哪些关。见 `toolsets.py`。

---

## Tool Gateway(Nous)

v0.10 新功能。付费 Nous Portal 订阅用户可以**通过订阅使用 web_search / image_gen / TTS / 浏览器**,免自配第三方 key。

---

## Trajectory

一次 agent 对话的完整记录(messages + usage + metadata)。研究用。

---

## TUI

Terminal UI。`hermes` 命令启动的交互终端界面。

---

## User Modeling

对用户的长期画像建模。Hermes 默认用 USER.md,可选用 Honcho 增强。

---

## watch_patterns

`terminal(background=true, watch_patterns=[...])` 的参数。模式命中时主动通知。v0.9+。

---

## YOLO Mode

`/yolo` —— 自动批所有命令审批。**仅 sandbox 用**。

---

## § (Section Sign)

memory 文件里的条目分隔符。
