---
hermes_version: 0.10.0
title: 测试策略
---

# 30. 测试策略

## 现状:3000+ 测试,分组测,~3 分钟跑完

```bash
source venv/bin/activate
python -m pytest tests/ -q
```

典型输出:

```
............................  [  3%]
...........................   [  7%]
...
============= 3042 passed, 12 skipped in 187.45s ===============
```

---

## 组织结构

```
tests/
├── conftest.py                        # ⭐ 全局 fixtures
├── test_run_agent.py                   # AIAgent 核心
├── test_model_tools.py                 # 工具分发
├── test_cli_init.py                    # CLI 启动
├── test_*.py                           # 顶层测试(按模块)
├── agent/                              # agent/ 模块测试
│   ├── test_context_compressor.py
│   ├── test_prompt_caching.py
│   ├── test_memory_manager.py
│   └── ...
├── tools/                              # tools/ 模块测试
│   ├── test_registry.py
│   ├── test_terminal_tool.py
│   ├── test_file_tools.py
│   └── ...
├── hermes_cli/                         # hermes_cli/ 模块测试
│   ├── test_profiles.py
│   ├── test_config.py
│   ├── test_commands.py
│   └── ...
└── gateway/                            # gateway/ 测试
    ├── test_run.py
    ├── platforms/
    │   ├── test_telegram.py
    │   ├── test_discord.py
    │   └── ...
    └── ...
```

---

## 全局 Fixtures(conftest.py)

### `_isolate_hermes_home`(autouse)

```python
@pytest.fixture(autouse=True)
def _isolate_hermes_home(tmp_path, monkeypatch):
    """
    每个测试独立 HERMES_HOME tmp 目录。
    autouse=True 意味着所有测试自动应用,无需显式注入。
    """
    fake_home = tmp_path / ".hermes"
    fake_home.mkdir()
    monkeypatch.setenv("HERMES_HOME", str(fake_home))
    (fake_home / "memories").mkdir()
    (fake_home / "skills").mkdir()
    yield fake_home
```

**意味着**:测试**不会**读写真实 `~/.hermes/`。如果你测 memory、skill、config,写到 tmp 目录。

### `no_real_llm`(autouse)

```python
@pytest.fixture(autouse=True)
def no_real_llm(monkeypatch):
    """
    Block real LLM calls in tests.
    测试里的 LLM 必须 mock,否则报错。
    """
    def fake_completions_create(*args, **kwargs):
        raise RuntimeError("Real LLM call blocked in tests. Use mock.")
    
    monkeypatch.setattr("openai.OpenAI.chat.completions.create",
                        fake_completions_create)
```

**确保**:测试必须 mock LLM,不会意外调用 API 花钱 / 依赖网络。

### `mock_agent`(显式注入)

```python
@pytest.fixture
def mock_agent():
    """创建一个 mocked AIAgent 实例,所有 LLM 调用 / 工具 mocked。"""
    agent = MagicMock(spec=AIAgent)
    agent.chat.return_value = "mocked response"
    return agent
```

---

## 典型测试模式

### 模式 1 · 测纯函数

```python
# tests/agent/test_context_compressor.py
from agent.context_compressor import compute_summary_budget

def test_summary_budget_small():
    assert compute_summary_budget(5_000) == 1_000  # 20%

def test_summary_budget_large():
    assert compute_summary_budget(200_000) == 10_000  # 5%
```

### 模式 2 · 测工具(mock 外部)

```python
# tests/tools/test_web_tools.py
from unittest.mock import patch, MagicMock
from tools.web_tools import web_search

def test_web_search_success():
    with patch("tools.web_tools.httpx.get") as mock_get:
        mock_get.return_value = MagicMock(
            json=lambda: {"results": [{"url": "...", "title": "..."}]}
        )
        result = web_search(query="test")
        data = json.loads(result)
        assert data["success"] is True
        assert len(data["results"]) == 1
```

### 模式 3 · 测 CLI 命令

```python
# tests/hermes_cli/test_commands.py
from hermes_cli.commands import resolve_command, COMMAND_REGISTRY

def test_resolve_alias():
    cmd = resolve_command("/reset")
    assert cmd.name == "new"  # reset 是 new 的别名

def test_registry_no_duplicates():
    names = [c.name for c in COMMAND_REGISTRY]
    aliases = [a for c in COMMAND_REGISTRY for a in c.aliases]
    all_names = names + aliases
    assert len(all_names) == len(set(all_names)), "Duplicate command names"
```

### 模式 4 · 测 agent loop

```python
# tests/test_run_agent.py
from unittest.mock import patch
from run_agent import AIAgent

def test_simple_chat(mock_agent):
    """Agent 单轮对话,无工具调用。"""
    with patch("openai.OpenAI") as mock_openai:
        mock_openai.return_value.chat.completions.create.return_value = MagicMock(
            choices=[MagicMock(message=MagicMock(
                content="Hello!",
                tool_calls=None,
            ))]
        )
        agent = AIAgent(model="test/model")
        result = agent.chat("Hi")
        assert result == "Hello!"
```

### 模式 5 · 测 Profile

```python
# tests/hermes_cli/test_profiles.py
from pathlib import Path

@pytest.fixture
def profile_env(tmp_path, monkeypatch):
    """注意:mock Path.home 改到 tmp。"""
    home = tmp_path / ".hermes"
    home.mkdir()
    monkeypatch.setattr(Path, "home", lambda: tmp_path)
    monkeypatch.setenv("HERMES_HOME", str(home))
    return home

def test_profile_create(profile_env):
    from hermes_cli.profiles import create_profile
    create_profile("work")
    assert (profile_env / "profiles" / "work").is_dir()
```

### 模式 6 · 测 Gateway 平台

```python
# tests/gateway/platforms/test_telegram.py
import pytest
from unittest.mock import MagicMock, patch, AsyncMock

@pytest.mark.asyncio
async def test_telegram_connect():
    with patch("gateway.platforms.telegram.Application") as mock_app:
        mock_bot = AsyncMock()
        mock_app.builder.return_value.token.return_value.build.return_value = mock_bot
        
        from gateway.platforms.telegram import TelegramPlatform
        platform = TelegramPlatform({"token": "test-token"})
        await platform.connect()
        
        mock_bot.initialize.assert_called_once()
```

---

## 运行子集

```bash
# 单文件
python -m pytest tests/test_run_agent.py -q

# 单个测试
python -m pytest tests/test_run_agent.py::test_simple_chat -q

# 按模块
python -m pytest tests/agent/ -q
python -m pytest tests/tools/ -q
python -m pytest tests/gateway/ -q
python -m pytest tests/hermes_cli/ -q

# 按关键词
python -m pytest -k "compression" -q

# 并行(快 2-3 倍)
python -m pytest tests/ -n auto -q
```

---

## CI 配置

Hermes 在 GitHub Actions 跑 3 个 matrix:

```yaml
strategy:
  matrix:
    python: ["3.11", "3.12", "3.13"]
    os: [ubuntu-latest, macos-latest]
```

每个组合跑完整 3000+ 测试。**PR 合并前必须全绿**。

---

## 写测试的规矩

### ✅ 必须

- 使用 `_isolate_hermes_home` fixture(autouse 已保证)
- LLM 调用 mock(autouse 已保证)
- 测试**确定性**(不依赖时间、网络、随机)
- 覆盖 happy path + 至少一个 edge case
- 名字表达意图:`test_compress_preserves_tail` > `test_compress_1`

### ❌ 不要

- 写真实网络 / API key 调用
- 依赖具体时间(用 `freezegun` 或 mock datetime)
- 测试之间共享状态(每个测试独立)
- 在测试里创建 `~/.hermes/`(会被 fixture 拦截,但也别尝试)
- 忽视 warning(`pytest -W error` 让它变红)

### 命名规范

```python
# tests/tools/test_weather_tool.py

def test_weather_now_success():
    """天气查询成功"""
    ...

def test_weather_now_missing_api_key():
    """没有 API key 时返回错误 JSON"""
    ...

def test_weather_now_api_timeout():
    """API 超时时优雅失败"""
    ...

def test_weather_now_invalid_city():
    """无效城市名"""
    ...
```

---

## 测试覆盖率

Hermes 用 `pytest-cov`:

```bash
python -m pytest tests/ --cov=. --cov-report=html
```

看 `htmlcov/index.html`。

**目标:≥ 80%**。关键模块(agent / tools / hermes_cli)个别要求 90%+。

---

## 性能测试

大型测试放在 `@pytest.mark.slow`,默认不跑:

```python
@pytest.mark.slow
def test_large_compression():
    """压缩 200k tokens 的对话"""
    ...

# 只跑 slow 测试
python -m pytest -m slow
# 跳过 slow
python -m pytest -m "not slow"
```

---

## 调试失败测试

```bash
# 详细输出
python -m pytest tests/test_foo.py -vv

# 失败时停下进 pdb
python -m pytest tests/test_foo.py --pdb

# 只跑上次失败的
python -m pytest --lf

# 打印所有 print
python -m pytest -s
```

---

## 常见坑

### 坑 1 · 在 conftest.py 里改全局

**现象**:某测试改了 `_HERMES_CORE_TOOLS`,污染其他测试。

**对策**:用 `monkeypatch` 而非直接赋值。

### 坑 2 · 异步测试忘了 mark

**现象**:async 测试不跑。

**对策**:加 `@pytest.mark.asyncio`,并装 `pytest-asyncio`。

### 坑 3 · mock 的范围不对

```python
# ❌ 不生效
with patch("tools.web_tools.httpx.get"):
    from tools.some_other import foo
    foo()   # 因为 some_other 内部用的是 `import httpx`,不是我 patch 的路径

# ✅ 按真实 import 路径 mock
with patch("tools.some_other.httpx.get"):
    foo()
```

### 坑 4 · fixture 间依赖

```python
# ❌ 互相依赖难以维护
@pytest.fixture
def fixture_a(fixture_b, fixture_c, fixture_d, fixture_e):
    ...
```

**对策**:扁平化。每个 fixture 做一件事。

### 坑 5 · 测试太脆

**现象**:随便改点代码,测试大片 fail。

**原因**:测试测的是**实现细节**,不是**外部行为**。

**对策**:测**接口层面的契约**。内部重构不该破坏测试。

---

## 贡献 PR 的测试要求

任何 PR 必须:

- [ ] 新增 / 修改的代码有对应测试
- [ ] **整个测试套件** 跑过(`python -m pytest tests/ -q`)
- [ ] 没引入新的 warning
- [ ] CI 3 个 matrix 全绿

---

## 下一章

[31. 已知坑点与防御 →](31-pitfalls.md)
