# pytest — Python 测试框架

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Holger Krekel 及社区 |
| 首次发布 | 2004年 |
| 用途 | Python 最流行的测试框架，自动发现测试、丰富的断言、插件生态 |

## 🎯 用来做什么？

- **自动化测试** — 运行单元测试、集成测试、功能测试
- **代码质量保障** — 确保修改代码不会破坏已有功能
- **测试驱动开发 (TDD)** — 先写测试再写实现，让代码更可靠
- **持续集成** — 与 CI/CD 配合，每次提交自动运行测试

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `pytest` | 自动发现并运行当前目录下所有 test_*.py 中的测试 |
| `pytest -v` | 详细模式，显示每个测试的名称和结果 |
| `pytest -k "keyword"` | 只运行名称中包含 keyword 的测试 |
| `pytest FILE.py` | 只运行指定文件中的测试 |
| `pytest -x` | 遇到第一个失败就停止（快速调试） |
| `pytest --lf` | 只运行上次失败的测试（重跑） |
| `pytest -s` | 显示测试中的 print 输出 |
| `pytest --co -q` | 安静模式下只列出测试名称（不运行） |

## 💡 日常使用示例

### 示例 1：写一组简单测试

创建测试文件 `test_calc.py`：

```python
# test_calc.py
def add(a, b):
    return a + b

def test_add():
    assert add(1, 2) == 3
    assert add(-1, 1) == 0

def test_add_strings():
    assert add("a", "b") == "ab"
```

运行：

```
$ pytest test_calc.py -v
============================= test session starts ==============================
platform darwin -- Python 3.11.15, pytest-9.0.2, pluggy-1.6.0
collected 2 items

test_calc.py::test_add PASSED                                            [ 50%]
test_calc.py::test_add_strings PASSED                                    [100%]

============================== 2 passed in 0.01s ===============================
```

### 示例 2：按关键字筛选测试

有多个测试文件，只想跑跟"水果"相关的测试：

```
$ pytest -k "apple or banana" -v
============================= test session starts ==============================
collected 4 items / 3 deselected / 1 selected

test_fruit.py::test_apple PASSED                                         [ 50%]
test_fruit.py::test_banana PASSED                                        [100%]

======================= 2 passed, 2 deselected in 0.00s ========================
```

`-k` 支持布尔表达式：`"not string"` 排除、`"string and not add"` 组合筛选。

### 示例 3：日常开发循环（先失败再修复）

```
$ pytest -x --tb=short
============================= test session starts ==============================
collected 4 items

test_calc.py ..                                                          [ 50%]
test_fruit.py ..                                                         [100%]

============================== 4 passed in 0.00s ===============================
```

- `-x`：一遇到失败就停，不浪费时间
- `--tb=short`：简短的错误回溯（默认是详细模式）
- 修改代码后重新运行，看到全绿就放心了

如果只想看测试名（不实际运行），方便规划：

```
$ pytest --co -q
test_calc.py::test_add
test_calc.py::test_add_strings
test_fruit.py::test_apple
test_fruit.py::test_banana
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| 提示 `pytest: command not found` | pytest 未安装或不在当前环境 | 运行 `pip install pytest` |
| 运行后显示 `no tests ran` | 文件名或函数名不符合命名规则 | 文件名必须以 `test_` 开头，函数名也以 `test_` 开头 |
| `assert` 报错信息看不懂 | 默认断言太简略 | 在 `conftest.py` 中配置，或加 `-v` 看详细输出 |
| 测试之间有依赖（A 先跑 B 才能跑） | 测试应该独立 | pytest 不保证执行顺序，用 fixture 管理依赖 |
| 测试很慢 | 可能连了数据库或网络 | 用 `@pytest.mark.slow` 标记，配合 `-m "not slow"` 排除 |

## 🔗 参考链接

- [官方文档](https://docs.pytest.org/)
- [GitHub 仓库](https://github.com/pytest-dev/pytest)
- 安装：`pip install pytest`
