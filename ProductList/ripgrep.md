# ripgrep (rg) — 超快的代码搜索工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Andrew Gallant (BurntSushi) |
| 首次发布 | 2016年 |
| 用途 | 递归搜索文件中匹配正则表达式的行，比 grep 快 5-10 倍，默认遵守 .gitignore |

## 🎯 用来做什么？

- **在代码库中快速搜索** — 比 `grep -r` 快几个数量级，大型项目秒出结果
- **替代 IDE 搜索** — 终端里的 Ctrl+Shift+F，配合 vim/neovim 尤其爽
- **管道处理** — 搜索结果可配合 `less`、`sed`、`awk` 等工具继续处理
- **代码审查辅助** — 快速定位函数调用、变量引用、TODO 标记等

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `rg PATTERN` | 在当前目录递归搜索 PATTERN |
| `rg -i PATTERN` | 忽略大小写搜索 |
| `rg -C N PATTERN` | 显示匹配行上下各 N 行上下文 |
| `rg -l PATTERN` | 只列出包含匹配的文件路径 |
| `rg -g '*.py' PATTERN` | 只搜索 .py 文件 |
| `rg -t py PATTERN` | 按文件类型搜索（py, rs, js ...） |
| `rg -w PATTERN` | 全词匹配 |
| `rg --count-matches PATTERN` | 统计每个文件的匹配次数 |
| `rg -F PATTERN` | 纯文本搜索（不解释正则特殊字符） |

## 💡 日常使用示例

### 示例 1：在项目里找所有 TODO

```
~/myproject$ rg "TODO"
src/main.py:15:# TODO: handle edge case when input is empty
src/main.py:42:# TODO: add logging
src/utils.py:8:# TODO: refactor this function
README.md:3:# TODO: write proper docs
```

默认递归所有文件，跳过 `.gitignore` 和二进制文件，按文件名分组输出。

### 示例 2：排查日志中的错误行

```
$ rg "ERROR|Exception" /var/log/app.log
/var/log/app.log:245:[2025-01-15 10:23:01] ERROR - Connection timeout
/var/log/app.log:312:[2025-01-15 10:23:05] Exception: NullPointerException
    at com.app.service.UserService.getUser(UserService.java:45)
```

配合 `-C 3` 看上下文：

```
$ rg -C 3 "ERROR" /var/log/app.log
/var/log/app.log:242:[2025-01-15 10:23:00] INFO - Request received
/var/log/app.log:243:[2025-01-15 10:23:01] DEBUG - Params: {id: 123}
/var/log/app.log:244:[2025-01-15 10:23:01] WARN - Retry attempt 1/3
/var/log/app.log:245:[2025-01-15 10:23:01] ERROR - Connection timeout
/var/log/app.log:246:[2025-01-15 10:23:02] INFO - Falling back to cache
```

### 示例 3：按文件类型精确搜索

```
$ rg -t py "def test_" --no-heading
tests/test_calc.py:1:def test_add():
tests/test_calc.py:5:def test_add_strings():
tests/test_fruit.py:1:def test_apple():
tests/test_fruit.py:5:def test_banana():
```

只看文件名（适合管道给其他工具）：

```
$ rg -l "def " --glob '*.py'
src/main.py
tests/test_calc.py
tests/test_fruit.py
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| 搜索没结果但文件里明明有 | rg 默认遵守 `.gitignore`，忽略隐藏文件和二进制文件 | 加 `-u` 或 `--no-ignore` |
| 搜中文或特殊字符没结果 | 正则特殊字符（`.`、`*`、`[]`）被解释 | 加 `-F` 做纯文本搜索 |
| 搜索太慢 | 搜索了 `node_modules/` 等大目录 | 用 `-g '!node_modules'` 排除 |
| 想要和 grep 一样的彩输出但没颜色 | rg 在管道模式下自动禁用颜色 | 加 `--color=always` |
| macOS 上 Homebrew 安装说 rg 已存在 | 可能是更旧的 ripgrep 版本 | 用 `brew upgrade ripgrep` |

## 🔗 参考链接

- [GitHub 仓库](https://github.com/BurntSushi/ripgrep)
- [官方文档](https://github.com/BurntSushi/ripgrep/blob/master/GUIDE.md)
- 安装：`brew install ripgrep`
