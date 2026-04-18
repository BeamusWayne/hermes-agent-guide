---
hide:
  - navigation
  - toc
---

# Hermes Agent 中文指南 ☤

<div style="text-align: center; margin: 2rem 0; font-size: 1.2rem;">
<em>从零基础到完全掌握 Nous Research 的 Hermes Agent —— 一本由浅入深的中文说明书。</em>
</div>

---

## 用一句话告诉我 Hermes 是什么

> **Hermes 是唯一自带学习闭环的 AI agent —— 它从经验里造技能、在使用中改技能、搜索自己的历史对话、跨会话建模「你是谁」,并且可以跑在 $5 的 VPS、GPU 集群、或闲时近乎零成本的 serverless 上。**

它不绑定你的笔记本 —— 你可以在 Telegram 里指挥它,让它在云端 VM 上干活;你可以把它放进 Discord 服务器、Slack 工作区、Signal 对话、甚至 Home Assistant。

它也不绑定你的模型 —— Nous Portal、OpenRouter(200+ 模型)、z.ai/GLM、Kimi、MiniMax、OpenAI、或你自己的端点,一行 `hermes model` 就能切换。

## 这本书能带你到哪

=== "第 0 层 · 认识"
    看懂 Hermes 到底独特在哪,建立「五大支柱」心智模型。**30 分钟。**

=== "第 1 层 · 上手"
    装好、跑通第一次对话、掌握 10 个必学命令。**1 小时。**

=== "第 2-3 层 · 日常使用"
    切模型、玩技能、配置记忆、接入 Telegram、配置定时任务、跨设备聊天。**一个周末。**

=== "第 4 层 · 精通"
    Profile 多实例、MCP 集成、皮肤定制、所有配置项。**一周业余时间。**

=== "第 5 层 · 内核"
    读懂 agent loop、工具注册机制、消息网关架构,能给 Hermes 加工具、加命令、加平台。**深度阅读。**

=== "第 6 层 · 研究"
    批量轨迹生成、Atropos RL 环境、轨迹压缩 —— 训练下一代工具调用模型。**科研向。**

## 推荐的阅读路径

!!! tip "先选一个身份"
    不同身份用不同顺序读,效率最高:

    - **只想体验一下**:[前言](preface/how-to-read.md) → [第 0 层](part-1-entry/00-what-is-hermes.md) → [第 1 层](part-1-entry/02-install.md) → 停。够用了。
    - **日常重度用户**:第一部 → 第二部 → 第三部。然后按需翻第四、第五部。
    - **开发者 / 贡献者**:第 0 层认识一下 → 直接跳第四部 · 内核 → 回头补前几部里用得上的场景。
    - **科研同学**:第 0 层 → 第五部 · 研究 → 按需回查。

## 这本书的承诺

- **每章都有心智模型图** —— 不只告诉你怎么做,先告诉你为什么这样做
- **最小可跑通实践** —— 复制粘贴就能见效,不搞「理论上可以」
- **坑点独立成段** —— 把作者踩过的坑摆在显眼位置,帮你绕路
- **精确版本锚定** —— 每篇开头标注基于的 Hermes 版本,升级有迹可循

## 开始阅读

[前言 · 为什么读这本书 →](preface/why-this-book.md){ .md-button .md-button--primary }
[直接入门 · Hermes 是什么 →](part-1-entry/00-what-is-hermes.md){ .md-button }

---

<div style="text-align: center; opacity: 0.7; font-size: 0.9rem; margin-top: 3rem;">
☤ <em>Hermes 的蛇杖 —— 引路,送达,连接。</em>
</div>
