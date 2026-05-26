# cat — 文件内容查看与拼接工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Ken Thompson / Dennis Ritchie（Bell Labs） |
| 首次发布 | 1971 年（Unix Version 1） |
| 用途 | 读取文件内容并输出到终端，也可拼接多个文件 |

## 🎯 用来做什么？

- **快速查看文件内容**——不用打开编辑器，直接看文件里有什么
- **拼接多个文件**——把几个文件合并成一个输出
- **创建小文件**——配合重定向 `>` 快速写内容
- **管道传递数据**——把文件内容喂给其他命令处理

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `cat file.txt` | 显示文件全部内容 |
| `cat file1.txt file2.txt` | 按顺序拼接显示多个文件 |
| `cat -n file.txt` | 带行号显示（包括空行） |
| `cat -b file.txt` | 带行号显示（跳过空行） |
| `cat > new.txt` | 从键盘输入内容写入新文件（Ctrl+D 结束） |
| `cat file1.txt >> file2.txt` | 把 file1 的内容追加到 file2 末尾 |
| `tac file.txt` | 倒序显示文件内容（最后一行最先输出） |

## 💡 日常使用示例

### 示例 1：快速预览配置文件

```bash
$ cat ~/.zshrc | head -20

# 显示 .zshrc 前 20 行
export PATH="/usr/local/bin:$PATH"
alias ll="ls -la"
...
```

### 示例 2：拼接多个日志文件分析

```bash
$ cat access-2024-01-*.log > all-january.log

# 把一月所有日志合并成一个大文件
# 然后用 grep 分析：
$ grep "ERROR" all-january.log | wc -l
42
```

### 示例 3：快速创建脚本文件

```bash
$ cat > hello.sh << 'EOF'
#!/bin/bash
echo "Hello, $(whoami)!"
echo "Today is $(date)"
EOF
$ chmod +x hello.sh
$ ./hello.sh
Hello, mk!
Today is Wed May 27 14:30:00 CST 2026
```

## 🚨 常见坑点

| 问题 | 原因 | 解决 |
|------|------|------|
| 大文件刷屏停不下来 | cat 会一次性输出全部内容 | 用 `cat file \| less` 分页查看，或用 `head`/`tail` 只看部分 |
| `cat file > file` 导致文件变空 | shell 重定向会先清空文件再读取 | 绝对不要把输入和输出指向同一个文件 |
| 二进制文件输出乱码 | cat 按文本方式读取，二进制数据无意义 | 用 `xxd` 或 `hexdump` 查看二进制文件 |
| macOS 和 Linux 的 `cat -n` 行为不同 | macOS BSD cat 的 `-n` 会编号所有行；GNU cat 默认也编号所有行，但 `-b` 语义一致 | 跨平台脚本用 `-b` 跳过空行更稳妥 |

## 🔗 参考链接

- [cat - Wikipedia](https://en.wikipedia.org/wiki/Cat_(Unix))
- macOS 手册：`man cat`
