---
hermes_version: 0.10.0
---

# 第三部 · 精通

**目标**:把 Hermes 真正装进你的生活 —— 住在 Telegram / Discord / Slack,定时帮你做事,多实例隔离工作生活,MCP 接入你所有的第三方系统。

## 这部分包含什么

<div class="grid cards" markdown>

-   :material-message-processing-outline: __[12. 消息网关总览 + Telegram](12-gateway-telegram.md)__

    ---

    网关架构心智模型,从零搭一个 Telegram bot。

-   :material-discord: __[13. Discord / Slack 网关](13-gateway-discord-slack.md)__

    ---

    两大办公 / 社区平台接入详细流程。

-   :material-translate: __[14. 其他平台 + 中文生态](14-gateway-others.md)__

    ---

    WhatsApp / Signal / iMessage / Matrix / Email / Home Assistant,以及微信 / 钉钉 / 飞书 / 企业微信 / QQBot。

-   :material-clock-time-eight-outline: __[15. 定时任务 cron 深入](15-cron-deep-dive.md)__

    ---

    调度表达式、跨平台投递、失败重试、真实场景剧本。

-   :material-folder-account-outline: __[16. Profile 多实例 + 备份](16-profile-backup.md)__

    ---

    工作 / 个人 / 项目隔离,`hermes backup` / `import` 跨机迁移。

-   :material-cog-outline: __[17. 配置文件详解](17-config-yaml.md)__

    ---

    config.yaml / .env 每个关键字段,怎么用 `hermes config set`。

-   :material-palette-outline: __[18. 皮肤定制](18-skin-customize.md)__

    ---

    YAML 零代码改 CLI 外观,从 ares 到 cyberpunk。

-   :material-hub-outline: __[19. MCP 集成 + Web Dashboard](19-mcp-dashboard.md)__

    ---

    接 GitHub / Notion / Linear / Postgres,启动本地 Web Dashboard。

-   :material-rocket-launch-outline: __[20. Tool Gateway + Fast Mode](20-gateway-fast.md)__

    ---

    Nous Portal 订阅自带工具、优先队列延迟优化。

-   :material-shield-check-outline: __[21. 安全权限 + 插件系统](21-security-plugins.md)__

    ---

    命令审批机制、DM 配对、容器隔离,以及 `hermes plugins` 自定义开发。

</div>

## 读完这部分,你应该能做到

- [x] 在至少 2 个消息平台上接好 Hermes bot,自如使用
- [x] 写至少一个 cron 任务,每天/每周自动跑
- [x] 有 2+ 个 Profile 隔离不同场景
- [x] 备份并成功在另一台机器上恢复你的 Hermes
- [x] 接入至少一个 MCP 服务器(如 GitHub)
- [x] 用 Web Dashboard 在浏览器里管理 agent
- [x] 写一个自己的皮肤(个人品牌或季节主题)
- [x] 理解 Hermes 的命令审批机制,知道什么时候该启用

准备好了,从 [12. 消息网关总览 + Telegram →](12-gateway-telegram.md) 开始。
