# yes — 重复输出字符串（自动化确认工具）

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs |
| 首次发布 | 1979 (V7 Unix) |
| 用途 | 不断重复输出指定字符串，通常用于自动应答交互式命令的确认提示 |

## 🎯 用来做什么？

- **自动回答"y"** — 管道给需要手动确认的命令，自动输入 yes
- **批量确认操作** — 配合 `rm -i`、`cp -i`、`apt-get` 等使用
- **生成重复文本** — 快速生成大量重复内容的测试数据
- **压力测试** — 配合管道填满缓冲区测试程序行为
- **最简洁的无限循环** — 比 `while true; do echo y; done` 短得多

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `yes` | 无限输出 "y"（Ctrl+C 终止） |
| `yes \| command` | 自动回答所有 yes/no 提示 |
| `yes "some text"` | 无限输出指定文本 |
| `yes \| head -n 10` | 输出 10 行后停止 |
| `yes "" \| head -3` | 输出 3 个空行 |
| `yes n \| command` | 自动回答 no（测试拒绝路径） |

## 💡 日常使用示例

### 1. 自动确认删除文件

最经典用法。配合 `rm -i`（交互式删除）自动确认：

```bash
# 先创建几个测试文件
touch a.txt b.txt c.txt

# 不用 yes：需要手动按 y
rm -i *.txt
# remove a.txt? y
# remove b.txt? y
# remove c.txt? y

# 用 yes：全自动
yes | rm -i *.txt
# 无需手动输入，全部自动确认
```

### 2. 生成自定义重复文本

```bash
yes "Apple Banana" | head -4
# 输出:
# Apple Banana
# Apple Banana
# Apple Banana
# Apple Banana
```

### 3. 生成大量数据填充文件

```bash
yes "line of text" | head -1000 > test_data.txt
wc -l test_data.txt
# 输出: 1000 test_data.txt
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `yes` 跑起来停不了 | 默认无限输出 | 按 Ctrl+C 终止，或通过 `head` 限制行数 |
| `yes --help` 输出的是 `--help` 本身 | macOS/Linux 的 yes 不支持任何选项，`--help` 被当作普通文本输出 | 用 `man yes` 查看文档 |
| 管道后的命令仍提示确认 | 该命令不从 stdin 读取 yes/no | 改用命令自身的 `-f`（force）选项 |
| 意外跑满 CPU | `yes` 无限输出会占用 100% CPU | 用 `head -n N` 做上限保护，或及时 Ctrl+C |

## 🔗 参考链接

- [macOS man page - yes](https://man.freebsd.org/cgi/man.cgi?query=yes)
- [GNU Coreutils yes](https://www.gnu.org/software/coreutils/manual/html_node/yes-invocation.html)
