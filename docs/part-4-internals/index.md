---
hermes_version: 0.10.0
---

# 第四部 · 内核

**目标**:读懂 Hermes 的源码结构、核心循环、扩展机制,能给项目加工具、加命令、加平台,或 fork 一份深度改造。

!!! danger "写代码前必读"
    **在你动任何代码之前**,先通读这两章:

    - [27. Prompt Caching 的边界](27-prompt-caching.md) —— 破坏缓存 = 成本暴涨
    - [31. 已知坑点与防御](31-pitfalls.md) —— 项目维护者已经踩过的坑,别再踩

    这两章是**用代价换来的经验**,能让你少几个月弯路。

## 这部分包含什么

<div class="grid cards" markdown>

-   :material-file-tree-outline: __[22. 项目结构总览](22-project-structure.md)__

    ---

    目录树 + 文件依赖链 + 核心入口点,搭建源码地图。

-   :material-cpu-64-bit: __[23. AIAgent 类详解](23-aiagent-class.md)__

    ---

    `run_agent.py` 源码走读,agent loop 完整生命周期。

-   :material-toy-brick-plus-outline: __[24. 工具注册机制](24-tool-registry.md)__

    ---

    加一个自己的工具的 3 文件改动模式。

-   :material-slash-forward: __[25. Slash 命令系统](25-slash-commands.md)__

    ---

    `COMMAND_REGISTRY` 单源头设计,怎么加一条跨 CLI + gateway 的命令。

-   :material-server-network: __[26. 消息网关架构](26-gateway-arch.md)__

    ---

    `BasePlatform` 抽象,加一个新平台适配器。

-   :material-cached: __[27. Prompt Caching 的边界](27-prompt-caching.md)__

    ---

    什么能动、什么不能动 —— 写代码前必读。

-   :material-account-switch-outline: __[28. Profile 工作原理](28-profile-internals.md)__

    ---

    `_apply_profile_override` 源码,HERMES_HOME 如何传染一切。

-   :material-compress: __[29. Context Compression 算法](29-compression-algo.md)__

    ---

    `context_compressor.py` 详解,摘要模板设计、tail 保护、递归压缩。

-   :material-test-tube: __[30. 测试策略](30-testing.md)__

    ---

    3000+ 测试怎么组织,`_isolate_hermes_home` fixture,怎么写新测试。

-   :material-alert-circle-outline: __[31. 已知坑点与防御](31-pitfalls.md)__

    ---

    项目维护者踩过的坑全集,写代码前必扫。

</div>

## 读完这部分,你应该能做到

- [x] 打开 `hermes-agent/` 仓库能认识 20+ 关键文件各自做什么
- [x] 追踪一次 `hermes` 启动 → 对话 → 工具调用 的完整代码路径
- [x] 给 Hermes 加一个自己的工具(3 文件改动)
- [x] 给 Hermes 加一个 slash 命令(CLI + gateway 都工作)
- [x] 给 Hermes 加一个新的消息平台适配器
- [x] 理解 prompt caching 的边界,不写破坏性代码
- [x] 写 profile-safe 的新代码(正确用 `get_hermes_home()`)
- [x] 为你的改动写 pytest 测试,跑过 3000 测试套件不 fail

准备好了,从 [22. 项目结构总览 →](22-project-structure.md) 开始。
