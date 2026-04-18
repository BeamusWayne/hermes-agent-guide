---
hermes_version: 0.10.0
---

# 怎么读

## 四种身份的推荐路径

=== "🧑‍💻 只想体验一下"

    目标:**1 小时内装好、聊一次、判断它是不是我想要的**。

    路径:
    1. [第 0 层 · Hermes 是什么](../part-1-entry/00-what-is-hermes.md) —— 10 分钟
    2. [第 1 层 · 五大支柱心智模型](../part-1-entry/01-the-five-pillars.md) —— 15 分钟
    3. [第 2 层 · 安装](../part-1-entry/02-install.md) —— 15 分钟
    4. [第 3 层 · 第一次对话](../part-1-entry/03-first-conversation.md) —— 10 分钟

    停在这里。觉得喜欢再继续往下。不喜欢也没损失 —— 你已经拥有了判断它的完整信息。

=== "🎯 日常重度用户"

    目标:**把 Hermes 装进自己生活**,在 Telegram / Discord / Slack 里指挥它,有定时任务,有技能,有记忆。

    路径:
    1. 完整读完 **第一部 · 入门**
    2. 完整读完 **第二部 · 日常** —— 切模型、技能、记忆、会话搜索
    3. 完整读完 **第三部 · 精通** 的前三章 —— 消息网关、定时任务、Profile

    剩下的按需翻。遇到具体问题再去查附录里的 [CLI 参考](../appendix/index.md) 和 [故障排查](../appendix/index.md)。

=== "🛠️ 开发者 / 贡献者"

    目标:**给 Hermes 加工具、加命令、加平台适配器,或者 fork 一份改造**。

    路径:
    1. [第 0 层 · Hermes 是什么](../part-1-entry/00-what-is-hermes.md) —— 只需要大局观
    2. [第 1 层 · 五大支柱](../part-1-entry/01-the-five-pillars.md) —— 建立心智模型
    3. 直接跳 **第四部 · 内核** —— 架构、agent loop、工具注册机制、Profile 工作原理
    4. 按需回头看前几部里用得上的场景

    !!! tip "重要"
        写代码前先通读 **第四部** 开头的「重要策略」—— prompt caching 的边界、profile-safe 代码规则、已知坑点。这些是**项目维护者已经踩过的坑**,别再踩一次。

=== "🔬 科研同学"

    目标:**用 Hermes 做 agent 轨迹生成、RL 训练、工具调用模型评估**。

    路径:
    1. [第 0 层 · Hermes 是什么](../part-1-entry/00-what-is-hermes.md) —— 建立大局观
    2. [第 1 层 · 五大支柱](../part-1-entry/01-the-five-pillars.md) —— 知道数据从哪流出
    3. 直接跳 **第五部 · 研究** —— 批处理、Atropos 环境、轨迹压缩
    4. 附录里的 [术语表](../appendix/index.md) 随手查

---

## 三种阅读节奏

!!! note "**冲刺式**(适合决策场景)"
    只读**第 0-1 层**(Hermes 是什么 + 五大支柱),大约 30 分钟。用来决定「要不要在团队里推这个东西」或者「这个项目跟我想做的事有没有关系」。

!!! note "**周末集中式**(适合建立系统认知)"
    一个完整周末读完**第一、二部**,动手把第一部的所有命令跑一遍,第二部挑 2-3 个场景亲自走通。周末结束你就是合格的重度用户。

!!! note "**按需查阅式**(适合已经在用了)"
    不用按顺序读。遇到具体问题,用站内搜索(右上角 🔍)找关键词。这本书的每一节都能独立读,不靠前文背景。

---

## 约定俗成

这本书用了一些固定的视觉约定:

!!! success "绿色提示 —— 最佳实践"
    值得照做的做法。

!!! warning "黄色警告 —— 坑点"
    作者或项目维护者踩过的坑,可以绕开。

!!! danger "红色危险 —— 会出事"
    这样做会损坏数据/配置/缓存/账号,不要试。

!!! info "蓝色信息 —— 背景知识"
    不影响读下一段,但知道了更有趣。

!!! question "紫色问题 —— 思考题"
    请先自己想一下,再往下看答案。读得更深。

```bash
# 代码块里带 $ 或 > 前缀的是 shell 命令
$ hermes --help
> hermes model
```

```python
# 不带前缀的是代码
def example():
    return "Python / Hermes 源码"
```

## 贡献、反馈、勘误

发现错误、有补充建议、想加一章、想署名翻译 —— 都欢迎:

- 小勘误 → 提 [Issue](https://github.com/BeamusWayne/hermes-agent-guide/issues)
- 有补充 → 提 PR
- 大改动 → 先开 [Discussion](https://github.com/BeamusWayne/hermes-agent-guide/discussions)

---

准备好了?[开始第一部 · 入门 →](../part-1-entry/index.md)
