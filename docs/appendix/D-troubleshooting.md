---
hermes_version: 0.10.0
title: 故障排查手册
---

# D. 故障排查手册

按**症状**索引。哪里不对,看哪节。

---

## 安装 / 启动

### `hermes: command not found`

```bash
# 1. 确认装了
ls ~/.hermes/venv/bin/hermes
# 或:
which hermes

# 2. shell PATH 里有没有 venv
echo $PATH | grep hermes

# 3. 重载 shell
source ~/.bashrc   # 或 ~/.zshrc

# 4. 不行就手动加
echo 'export PATH="$HOME/.hermes/venv/bin:$PATH"' >> ~/.zshrc
```

### 启动报 `ModuleNotFoundError`

```bash
# venv 环境坏了 / 依赖没装完整
source ~/.hermes/venv/bin/activate
pip install --force-reinstall hermes-agent
```

### 启动报 `yaml.scanner.ScannerError`

`~/.hermes/config.yaml` 格式有问题。

```bash
# 备份,恢复默认
cp ~/.hermes/config.yaml ~/.hermes/config.yaml.bak
rm ~/.hermes/config.yaml
hermes setup    # 重走向导
```

或手工修:
- 缩进全空格
- `:` 后有空格
- 含特殊字符的值加引号

### Termux 上某些依赖装不上

```bash
# 用 Termux 专用 extra
pip install 'hermes-agent[termux]'
```

---

## API / 模型

### 401 / 403

API key 错了 / 失效。

```bash
cat ~/.hermes/.env | grep API
# 确认 key 在,格式对
```

到对应 provider 面板 rotate 新 key,更新 `.env`,`hermes gateway restart`(如果在跑 gateway)。

### 429 Too Many Requests

限流。

- 降 concurrency / 降频率
- 配 fallback model(`providers.routing.fallback_model`)
- 换 provider(从 OpenRouter 临时切 Anthropic 官方)

### 500 / 503

Provider 临时挂了。

- 等几分钟重试
- 切 fallback

### 响应空 / 截断

Hermes v0.9+ 有 empty response retry(3 次 nudge)。如果还是不行:
- 换模型(某些 reasoning 模型偶发)
- 降 temperature

### 「未知 provider」

```bash
hermes model --list
```

看当前已配置的。没有你要的 → `hermes model` 交互菜单里 "Add custom provider"。

---

## Agent 行为

### Agent 只说话不调工具

- 工具调用能力弱的模型(小模型、某些 chat-only 模型)
- 切 Claude Sonnet / Kimi K2.5 / GPT-4o 级别
- 明确说「用 xxx 工具来做」

### Agent 反复调同一工具

- `max_iterations` 设太高,让它死循环
- 切更强模型
- Ctrl+C 打断,用人话明确换方向

### Agent "忘了" memory 里的东西

- `/memory` 看 memory 里**确实有吗**
- `cat ~/.hermes/memories/MEMORY.md`
- 重启会话(memory 只在 session 启动注入)
- **Profile 不对**:你可能在另一个 profile

### Agent 跑太久

看实时活动栏(`┊` 行)知道在干啥。
- 长网络请求:超时 / 设 timeout
- 大工具输出:其实在正常工作,稍等
- 死循环:`Ctrl+C`

### 压缩后对话变呆

- 压缩质量问题:升级 Hermes(v0.8+ 修了若干)
- auxiliary model 能力不够:换强一点
- 频繁压缩后考虑 `/new`

---

## Gateway / 消息平台

### bot 完全不响应

```bash
hermes gateway status
```

看:
- 网关是不是在跑
- 对应平台是不是 `connected`
- `allowed_users` 里有没有你自己

```bash
hermes gateway logs --follow
```

看实时日志,发消息时应该有 incoming event。

### Telegram bot 收不到消息

- Token 错(@BotFather 处对照)
- 国内网络:配 `TELEGRAM_PROXY`
- **Webhook 冲突**:Telegram 同时只能一个程序接收。旧进程没关干净。
  ```bash
  # 手动删 webhook
  curl "https://api.telegram.org/bot<TOKEN>/deleteWebhook"
  ```

### Discord bot @ 后没反应

- Developer Portal → **Message Content Intent** 必须开
- Event Subscriptions 加 `app_mention`
- bot 在服务器里的权限够

### Slack bot 连不上

- Socket Mode 要两个 token(`xoxb-` + `xapp-`),别弄反
- Enterprise Grid 不支持 Socket Mode,必须 HTTP Events

### 消息延迟 / 卡顿

- LLM 本身慢 —— 切 Fast Mode 或换模型
- 某工具卡 —— 看 logs 里每步耗时
- gateway 负载 —— 多平台并发,升级到 v0.9+ 的 async 优化

---

## Profile / 数据

### profile 切了没生效

```bash
env | grep HERMES
```

看 `HERMES_HOME` 是不是对的。

```bash
hermes -p work                  # 正确用法
# 或
HERMES_PROFILE=work hermes
```

### profile 数据丢了

```bash
ls ~/.hermes/profiles/
```

profile 目录在不在?没了可能:
- 误 `hermes profile remove`
- 迁移时没拷过来

**对策**:下次**有重要 profile 前先 `hermes backup`**。

### 备份恢复后 gateway 启动不了

token 锁冲突。

```bash
ls ~/.hermes/profiles/*/gateway/locks/
rm ~/.hermes/profiles/*/gateway/locks/*.lock
```

然后重启 gateway。

### sessions.db 损坏

```bash
sqlite3 ~/.hermes/sessions.db "PRAGMA integrity_check;"
```

如果损坏:
```bash
# 导出能导出的
sqlite3 ~/.hermes/sessions.db ".dump" > dump.sql
mv ~/.hermes/sessions.db ~/.hermes/sessions.db.broken
sqlite3 ~/.hermes/sessions.db < dump.sql
```

或从 `hermes backup` 恢复。

---

## 配置 / 皮肤 / 插件

### `/skin xxx` 报找不到

```bash
ls ~/.hermes/skins/
```

文件名拼对了吗?文件里 `name:` 字段跟文件名一致吗?

### config 改了没生效

**大部分 config 改动需要重启 CLI / gateway**。

```bash
# CLI:退出再开
# gateway:
hermes gateway restart
```

### 插件启用后加载失败

```bash
hermes plugins info <name>
```

看错误信息。常见:
- 依赖没装(`pip install xxx`)
- Python 版本不兼容
- 插件代码语法错

### MCP server 连不上

```bash
# 对话内
> /reload-mcp
```

仍然不行:
- 检查 `command` / `args` / `env` 配置
- 手动跑一次:`npx -y @modelcontextprotocol/server-github`,看它能启动吗
- OAuth 问题:`ls ~/.hermes/mcp_oauth/` 看 token 状态

---

## 工具细节

### terminal 工具没权限

- 默认开 approval。你批准了吗?
- messaging 平台默认关 terminal(安全)。`hermes tools` 开(慎)。

### browser 工具启动慢 / 失败

- `BROWSERBASE_API_KEY` 没配
- 或者用 Camofox 本地浏览器,但要装 extras:`pip install 'hermes-agent[browser]'`

### image_generation 报 402

FAL 额度用完了。充值 或 关掉 `use_gateway.image_generation` 用别的。

### session_search 返回空

- `sessions.db` 里没相关内容
- FTS5 tokenizer 对中文弱 —— 搜英文关键词组合
- 时间范围太窄:`days_back` 调大

---

## 升级 / 迁移

### `hermes update` 失败

```bash
# 手动强制
pip install --upgrade hermes-agent
```

或:

```bash
rm -rf ~/.hermes/venv
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

(不会丢数据,只重建 venv)

### 升级后某命令没了 / 行为变了

```bash
hermes --version
```

看 changelog:[github.com/NousResearch/hermes-agent/releases](https://github.com/NousResearch/hermes-agent/releases)

如果回退到老版:
```bash
pip install hermes-agent==0.9.0
# 可能需要 hermes import 一个老版备份
```

---

## 快速诊断命令汇总

| 症状类别 | 命令 |
|---|---|
| 整体健康 | `hermes doctor --verbose` |
| 安全配置 | `hermes doctor --security` |
| Gateway 状态 | `hermes gateway status` |
| 日志 | `hermes gateway logs --follow` |
| 生成诊断报告 | `hermes debug share` |
| 查 env vars | `env | grep HERMES` |
| 查 config | `hermes config get` |

---

## 寻求帮助

试了以上仍未解决:

1. **`hermes debug share`** —— 生成诊断报告,拿到 URL
2. 去 [Discord](https://discord.gg/NousResearch) #support
3. 或提 [GitHub Issue](https://github.com/NousResearch/hermes-agent/issues)

**提交时**:
- 附诊断 URL
- 描述你做了什么 → 预期什么 → 实际什么
- 贴相关日志(脱敏!)
