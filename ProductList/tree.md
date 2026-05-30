# tree — 目录结构树状显示工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Steve Baker / Florian Sesser |
| 首次发布 | 1993 年 |
| 用途 | 以树形结构递归显示目录内容，比 `ls -R` 更直观易读 |

> macOS 默认不安装 tree，需要 `brew install tree`。tree 2.3.2 是目前最新稳定版。

## 🎯 用来做什么？

- **快速查看项目结构** — 一键看整个项目的目录和文件布局
- **文档截图** — 项目 README 里的目录结构图，用 tree 生成最省事
- **定位文件位置** — 在嵌套很深的项目里，一眼看出文件在哪个子目录
- **排除干扰文件** — 配合 `-I` 或 `--gitignore` 过滤掉 node_modules、.git 等
- **输出为 JSON/XML** — 用 `-J` 或 `-X` 导出结构给脚本处理

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `tree` | 显示当前目录树 |
| `tree /路径` | 显示指定目录的树 |
| `tree -L 2` | 限制深度为 2 层 |
| `tree -d` | 只显示目录，不显示文件 |
| `tree -a` | 显示隐藏文件（以 `.` 开头的文件） |
| `tree -I "node_modules\|.git"` | 排除指定模式的文件/目录 |
| `tree --gitignore` | 遵循 `.gitignore` 规则过滤 |
| `tree -h` | 显示人类可读的文件大小 |
| `tree -s` | 显示文件大小（字节） |
| `tree -p` | 显示文件权限 |
| `tree -J` | 输出 JSON 格式 |
| `tree -X` | 输出 XML 格式 |
| `tree -o 文件名` | 输出到文件而不是终端 |
| `tree --dirsfirst` | 目录排在文件前面 |
| `tree -C` | 强制彩色输出 |

## 💡 日常使用示例

### 示例 1：查看项目目录结构（经典用法）

```bash
$ cd /Users/mk/obsidian_files/obs_repository
$ tree -L 2 -d
.
├── AI-report
│   ├── 2026-05-13
│   ├── 2026-05-14
│   └── 2026-05-15
├── DeepSeek-usage
├── Excalidraw
├── GitHub-trending
├── Hermes-folder
├── Hermes-to-me
│   └── knowledge-base
├── Ideas
├── Ideas-Converted
├── Organized
├── Product manual
│   └── ProductList
├── Project-Guides
├── Project-PRDs
├── Project-Plans
└── works
    ├── Weekly-Reports
    └── _weekly-input
```

### 示例 2：生成 README 用的目录结构图

```bash
# 排除常见的不需要展示的目录
$ tree -L 2 -I "node_modules|.git|__pycache__|.DS_Store" --dirsfirst
.
├── src
│   ├── api
│   ├── components
│   └── utils
├── tests
├── docs
├── package.json
└── README.md
```

### 示例 3：输出 JSON 格式给脚本处理

```bash
$ tree -J -L 1 --dirsfirst
[
  {"type":"directory","name":".","contents":[
    {"type":"directory","name":"docs"},
    {"type":"directory","name":"src"},
    {"type":"file","name":"package.json"},
    {"type":"file","name":"README.md"}
  ]}
]

# 配合 jq 使用
$ tree -J -L 2 | jq '.[].contents[] | select(.type=="directory") | .name'
"docs"
"src"
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `command not found: tree` | macOS 未预装 | `brew install tree` |
| 输出太长刷屏 | 目录嵌套很深或文件太多 | 加 `-L 3` 限制深度，加 `-d` 只看目录 |
| 没看到隐藏文件 | `.` 开头的文件默认不显示 | 加 `-a` |
| node_modules 污染输出 | 文件夹太大 | 加 `-I node_modules` 排除 |
| 文件名里中文乱码 | 终端编码问题 | 加 `-N` 显示非打印字符原文 |
| 输出到文件时没有颜色 | 颜色默认在管道/重定向时关闭 | 加 `-C` 强制着色，或直接重定向（无颜色） |
| macOS 上 `--du` 计算大小较慢 | 需要递归统计所有文件 | 只在浅层目录使用 |

## 🔗 参考链接

- [tree 官方手册](https://manpages.ubuntu.com/manpages/jammy/man1/tree.1.html)
- [Homebrew tree 包](https://formulae.brew.sh/formula/tree)