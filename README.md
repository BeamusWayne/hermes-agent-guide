# Hermes Agent 中文指南

> 一本由浅入深、从零基础到完全掌握 [Hermes Agent](https://github.com/NousResearch/hermes-agent) 的中文说明书。

[![Deploy](https://github.com/BeamusWayne/hermes-agent-guide/actions/workflows/deploy.yml/badge.svg)](https://github.com/BeamusWayne/hermes-agent-guide/actions/workflows/deploy.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Based on](https://img.shields.io/badge/Hermes%20Agent-v0.10.0-FFD700.svg)](https://github.com/NousResearch/hermes-agent)

## 📖 在线阅读

**👉 [beamuswayne.github.io/hermes-agent-guide](https://beamuswayne.github.io/hermes-agent-guide/)**

## 🎯 这本书是写给谁的

- **听说过 Hermes Agent 但不知从哪入手**的新人
- **从 OpenClaw / Claude Code / 其他 AI agent 迁移过来**的老用户
- **想把 Hermes 装进自己生活**(Telegram、定时任务、云端 VM)的重度用户
- **想为 Hermes 添加工具、皮肤、平台**的开发者
- **研究方向涉及 agent 训练、轨迹生成**的科研同学

## 🗂️ 目录结构

```
docs/
├── preface/          前言:为什么读、怎么读
├── part-1-entry/     第一部 · 入门(第 0-1 层:认识 + 上手)
├── part-2-daily/     第二部 · 日常(第 2-3 层:日常使用 + 让它住进生活)
├── part-3-mastery/   第三部 · 精通(第 4 层:配置/皮肤/MCP/Profile)
├── part-4-internals/ 第四部 · 内核(第 5 层:架构/扩展)
├── part-5-research/  第五部 · 研究(第 6 层:批处理/RL)
└── appendix/         附录:CLI 参考、环境变量、故障排查、术语表
```

每章节都遵循三段式模板:

1. **心智模型** —— 一张图 + 一段白话,先建立模型
2. **最小实践** —— 可复制粘贴跑通的最短路径
3. **坑点 & 进阶链接** —— 踩过的坑、相关阅读

## 🛠️ 本地开发

```bash
# 1. 克隆
git clone https://github.com/BeamusWayne/hermes-agent-guide.git
cd hermes-agent-guide

# 2. 安装依赖(建议用虚拟环境)
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 3. 本地预览(默认 http://127.0.0.1:8000)
mkdocs serve

# 4. 构建静态站点
mkdocs build --strict
```

## 🤝 贡献

这本指南欢迎任何形式的贡献 —— 改错、补充、译者署名、加章节都行。

- 发现错误?提 [Issue](https://github.com/BeamusWayne/hermes-agent-guide/issues)
- 有补充?提 PR
- 想加一章?先开 Discussion 聊想法

## 📜 版权

- **指南内容**:MIT License(见 `LICENSE`)
- **Hermes Agent 本体**:MIT License,版权归 [Nous Research](https://nousresearch.com)
- **基于版本**:Hermes Agent v0.10.0(每篇文档会在 frontmatter 标注精确锚点版本)

---

> ☤ *Hermes 的蛇杖 —— 引路,送达,连接。*
