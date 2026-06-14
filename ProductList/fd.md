# fd — 超快文件查找工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | David Peter (sharkdp) |
| 首次发布 | 2017 年 |
| 用途 | 比 `find` 更快的文件搜索工具，语法更简单、输出更美观 |
| 官网 | [github.com/sharkdp/fd](https://github.com/sharkdp/fd) |

## 🎯 用来做什么？

- **快速找文件** — `fd 关键词` 比 `find` 快几倍，语法直观
- **按类型、扩展名、大小搜索** — 只找图片、只找 Markdown、只找大于 1MB 的文件
- **批量操作文件** — 找到文件后直接执行命令（删除、压缩、改名）
- **替代 `find`** — 日常开发中 90% 的场景 `fd` 比 `find` 更好用

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `fd PATTERN` | 搜索文件名匹配 PATTERN 的文件（默认正则、智能大小写） |
| `fd -e py` | 只找扩展名为 `.py` 的文件 |
| `fd -t d` | 只找目录 |
| `fd -t f` | 只找文件 |
| `fd -H PATTERN` | 包含隐藏文件（默认忽略以 `.` 开头的文件） |
| `fd -I PATTERN` | 忽略 `.gitignore` 规则 |
| `fd -E node_modules` | 排除 `node_modules` 目录 |
| `fd PATTERN /path` | 在指定目录下搜索 |
| `fd -x cmd {}` | 对每个结果执行命令 |
| `fd -X cmd` | 把所有结果一次传给命令 |

## 💡 日常使用示例

### 示例 1：快速找文件（对比 find）

```bash
# 用 fd 找图片（一行搞定）
fd -e jpg -e png

# 等价于 find（复杂很多）
find . -type f \( -name '*.jpg' -o -name '*.png' \)

# 找 README 相关的文件（忽略大小写是默认的）
fd readme
```

### 示例 2：按类型和大小过滤

```bash
# 找所有 Python 文件（当前目录及子目录）
cd /Users/mk/obsidian_files
fd -e py

# 找所有目录名为 node_modules 的
fd -t d -g 'node_modules'

# 找大于 1MB 的文件
fd -S +1M -t f

# 最近 1 小时内修改过的文件
fd --changed-within 1h
```

### 示例 3：找到文件后批量操作

```bash
# 批量删除所有 .DS_Store 文件
fd -H '^\\.DS_Store$' -t f -X rm

# 统计所有 Markdown 文件的行数
fd -e md -X wc -l

# 批量改权限（只对 .sh 文件）
fd -e sh -x chmod +x {}

# 找到日志文件并压缩
fd -e log -x gzip {}
```

### 示例 4：在代码项目中快速定位

```bash
cd /Users/mk/obsidian_files/obs_repository

# 找所有 Python 测试文件
fd -g 'test_*.py'

# 找所有 JSON 配置文件
fd -e json

# 排除 node_modules 后搜索
fd -E node_modules -E .git -e js
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `fd: command not found` | 没安装 | `brew install fd`（macOS） |
| 搜不到隐藏文件 | 默认忽略 `.` 开头的文件 | 加 `-H` 或 `--hidden` |
| 搜到了 `.gitignore` 里的文件 | 默认遵守 `.gitignore` | 加 `-I` 或 `--no-ignore` |
| 搜不到任何结果 | 默认是正则匹配，不是通配符 | 用 `-g` 切换到 glob 模式，或用 `*` 通配 |
| macOS 上搜不出扩展名不同的文件 | macOS 默认大小写不敏感 | 文件名本身不敏感是 macOS 特性，`fd` 的智能大小写不受影响 |
| 以为 `fd` 是 `find` 的 alias | 安装后需要确认用的是哪个 | `which fd` 确认路径 |

> 💡 **最佳实践**：日常搜索先用 `fd`，遇到复杂的逻辑条件（时间组合、权限组合）再切回 `find`。

## 🔗 参考链接

- [GitHub: sharkdp/fd](https://github.com/sharkdp/fd)
- [fd 命令选项完整文档](https://github.com/sharkdp/fd#command-line-options)
- [对比 find 的基准测试](https://github.com/sharkdp/fd#benchmark)
