# bat — 带语法高亮的 cat 增强版

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | sharkdp (David Peter) |
| 首次发布 | 2018 年 |
| 仓库 | https://github.com/sharkdp/bat |
| 用途 | 替代 `cat` 的命令行文件查看工具，自动语法高亮、显示行号、Git 集成 |

## 🎯 用来做什么？

- **查看代码文件**：自动识别语言并高亮语法，比 `cat` 直观 10 倍
- **显示行号**：默认显示行号，调试时快速定位
- **Git 集成**：修改过的行前显示 Git 标记（+ 新增 / ~ 修改），一目了然
- **配合管道使用**：支持 `--paging=never` 模式，无缝替代 `cat`

## 🔧 常用命令

```bash
# 1. 查看文件（默认带语法高亮和行号）
bat file.py

# 2. 禁用分页（像 cat 一样直接输出）
bat --paging=never file.py

# 3. 只显示选中行范围
bat --line-range 10:30 file.py

# 4. 显示非打印字符（空格、制表符、换行符）
bat -A file.txt

# 5. 显示多个文件并拼接
bat file1.py file2.py

# 6. 列出所有支持的语言
bat --list-languages

# 7. 用特定语言高亮（强制显示为某种语言）
bat -l json data.txt

# 8. 显示所有可用的主题并预览
bat --list-themes
```

## 💡 日常使用示例

### 示例 1：用 bat 替代 cat

```bash
# 以前看代码：
cat app.py

# 现在看代码：
bat app.py

# 效果：自动检测 Python 语法，关键词/字符串/注释全部着色，左侧有序号
# 文件大时会自动分页（按 q 退出，方向键翻页）
```

示例输出效果（文本描述）：
```
 1 │ import json
 2 │ from typing import Optional
 3 │
 4 │ def greet(name: str) -> str:
 5 │     """Say hello with JSON formatting."""
 6 │     return json.dumps({"message": f"Hello, {name}!"})
   │                                                       ← 绿色注释
```

### 示例 2：管道中使用 bat

```bash
# cat 配合管道：
cat config.json | jq .

# bat 配合管道（禁用分页）：
bat --paging=never config.json | jq .

# 或者用标准输入管道：
find . -name "*.py" | xargs bat --paging=never
```

### 示例 3：只查看文件的某一段

```bash
# 只看第 30 到 50 行
bat --line-range 30:50 long_file.py

# 查看第 1 行到第 20 行（适合快速看文件头部）
bat --line-range 1:20 long_file.py
```

### 示例 4：在 fzf 中作为预览器（经典组合）

```bash
# 配合 fzf 实现文件搜索 + 内容预览
fzf --preview 'bat --color=always --style=numbers {}'

# 配合 ripgrep 搜索代码并预览结果
rg "def " --line-number | fzf --preview 'echo {} | cut -d: -f1 | xargs bat --color=always --line-range $(echo {} | cut -d: -f2):'
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| 输出被截断到一屏 | bat 默认启用分页器 | 加 `--paging=never` 或 alias `cat=bat --paging=never` |
| 管道中 bat 输出格式错误 | 管道中 bat 自动禁用颜色和分页（这是对的，但有些场景需要颜色） | 加 `--color=always` 强制保留颜色 |
| `bat: command not found` | 没有安装 | `brew install bat` |
| 语法高亮不正确 | bat 自动检测语言失败 | 用 `-l` 强制指定语言：`bat -l yaml file.txt` |
| 中文字符显示为方块 | 系统缺少中文字体 | 安装 Nerd Font 或设置终端支持中文的字体 |
| macOS 上 bat 版本与 brew 版本不一致 | 用 pip 装的旧版 | 统一用 `brew install bat` 安装，或用 `bat --version` 检查 |

## 🔗 参考链接

- [GitHub 仓库](https://github.com/sharkdp/bat)
- [官方 Wiki](https://github.com/sharkdp/bat/wiki)
- [Homebrew 安装](https://formulae.brew.sh/formula/bat)