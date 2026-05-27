# pbcopy / pbpaste — macOS 剪贴板命令行工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple（macOS 内置） |
| 首次发布 | macOS 原生命令 |
| 用途 | 在终端和系统剪贴板之间传数据：`pbcopy` 写入剪贴板，`pbpaste` 读取剪贴板 |

## 🎯 用来做什么？

- **终端输出存到剪贴板**——把命令结果复制出来，粘贴到聊天工具、邮件或文档
- **剪贴板内容喂给命令**——从网页或编辑器复制数据，直接传到终端处理
- **代码片段快速拷贝**——把脚本输出或文件内容直接送到剪贴板，省掉手动选中 + C 的操作
- **管道数据处理的中转站**——在 GUI 和 CLI 之间搭建数据桥梁

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `echo "hello" \| pbcopy` | 把文本写入剪贴板 |
| `pbpaste` | 输出剪贴板内容 |
| `cat file.txt \| pbcopy` | 把文件内容复制到剪贴板 |
| `pbpaste > file.txt` | 把剪贴板内容保存到文件 |
| `pbpaste \| grep "关键字"` | 搜索剪贴板内容 |
| `ls \| pbcopy` | 把当前目录列表复制到剪贴板 |
| `python3 script.py \| pbcopy` | 把脚本输出复制到剪贴板 |
| `pbpaste \| python3` | 把剪贴板内容作为 Python 代码执行 |
| `sort data.csv \| head -10 \| pbcopy` | 处理后的数据管道到剪贴板 |

## 💡 日常使用示例

### 示例 1：代码片段快速分享

```bash
# 正在排查一个 bug，想把错误信息发给同事
$ cat error.log | tail -20 | pbcopy
# 现在终端 20 行已在剪贴板，打开微信/Slack 直接 ⌘V

# 或者把文件内容全复制出来
$ cat ~/.zshrc | pbcopy
# 然后粘贴到聊天工具给别人看配置
```

### 示例 2：从网页复制数据到终端处理

```bash
# 在浏览器里选中一行 JSON 数据，⌘C 复制
$ pbpaste
{"name": "Alice", "age": 30, "city": "Beijing"}

# 用 jq 格式化（如果安装了 jq）
$ pbpaste | jq .
{
  "name": "Alice",
  "age": 30,
  "city": "Beijing"
}

# 或者提取特定字段
$ pbpaste | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['name'])"
Alice
```

### 示例 3：批量命令行结果存入文档

```bash
# 查看磁盘使用情况并复制
$ df -h | head -10 | pbcopy
# 剪贴板内容：
# Filesystem      Size   Used  Avail Capacity iused      ifree %iused  Mounted on
# /dev/disk1s5   233Gi   20Gi  212Gi     9%  ...

# 配合 Markdown 输出
$ echo "## 系统状态报告\n" > report.md
$ echo "\`\`\`" >> report.md
$ df -h | head -10 >> report.md
$ echo "\`\`\`" >> report.md
$ cat report.md | pbcopy
# 直接粘贴到在线文档或 GitHub Issue
```

## 🚨 常见坑点

| 问题 | 原因 | 解决 |
|------|------|------|
| `pbcopy`/`pbpaste` 在 Linux 上不存在 | 这是 macOS 专属命令 | Linux 用 `xclip` 或 `xsel` 替代，或安装 `pbcopy` 的 alias |
| 二进制数据导致乱码 | 剪贴板存储的是文本，二进制文件不合适 | 文本工具如 `cat` 输出二进制会乱码，先确认复制的数据是文本 |
| 前后台剪贴板混淆 | macOS 的通用剪贴板（⌘C/⌘V）等价于 `pbcopy`/`pbpaste` | 注意：某些密码管理工具会清空剪贴板，可能影响你的操作 |
| 超长内容粘贴到终端变慢 | `pbpaste` 输出大量内容到终端需要时间 | 用 `pbpaste \| less` 分页查看，或用 `pbpaste \| head -N` 只看前 N 行 |
| 通过 SSH 连接时不可用 | `pbcopy` 需要本地 UI 环境 | SSH 到远程服务器时不能用。可通过 `pbcopy` forward 或其他方式替代 |

## 🔗 参考链接

- macOS 手册：`man pbcopy`
- [macOS pbcopy command on SS64](https://ss64.com/mac/pbcopy.html)