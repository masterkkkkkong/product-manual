# sed — 流编辑器，文本替换与处理

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Lee E. McMahon（Bell Labs） |
| 首次发布 | 1974年 |
| 用途 | 以批处理方式对文本进行查找替换、插入删除，不打开编辑器即可修改文件或管道流 |

## 🎯 用来做什么？

- **批量替换文本** — 把文件里的 `old` 全部换成 `new`，一行命令搞定上百个文件
- **删除/提取特定行** — 删除注释行、空白行，或提取第 5~10 行
- **文本插入** — 在某行前后插入新内容
- **管道预处理** — 配合其他命令做链式处理，比如 `cat file | sed 's/foo/bar/' | grep something`

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `sed 's/old/new/' file` | 替换每行第一个 `old` 为 `new`（输出到终端，不改原文件） |
| `sed 's/old/new/g' file` | 替换每行所有 `old`（g = global） |
| `sed -i '' 's/old/new/g' file` | 直接修改原文件（macOS BSD sed 需带 `''`） |
| `sed -n '5,10p' file` | 只打印第 5 到第 10 行（-n 抑制默认输出，p 为打印） |
| `sed '/pattern/d' file` | 删除包含 pattern 的行 |
| `sed 's/^/HEAD: /' file` | 在每行开头插入文本 |
| `sed 's/$/ :TAIL/' file` | 在每行末尾追加文本 |
| `sed '3i\插入内容' file` | 在第 3 行之前插入一行 |

## 💡 日常使用示例

### 示例 1：批量替换文本

把文件中的所有 `apple` 替换为 `orange`：

```bash
$ echo -e "apple\nbanana\napple\ncherry" > /tmp/fruits.txt

$ sed 's/apple/orange/g' /tmp/fruits.txt
orange
banana
orange
cherry
```

直接修改原文件（macOS 需加 `''`，Linux 不需要）：

```bash
# macOS 语法（BSD sed）
sed -i '' 's/apple/orange/g' /tmp/fruits.txt

# Linux 语法（GNU sed）
# sed -i 's/apple/orange/g' /tmp/fruits.txt
```

### 示例 2：删除特定行

```bash
$ cat /tmp/data.txt
line 1
line 2    # important
line 3
line 4    # deprecated
line 5

# 删除第 2 行
$ sed '2d' /tmp/data.txt
line 1
line 3
line 4    # deprecated
line 5

# 删除包含 "#" 的行（注释）
$ sed '/#/d' /tmp/data.txt
line 1
line 3
line 5

# 删除空白行
$ sed '/^$/d' /tmp/data.txt
line 1
line 2    # important
line 3
line 4    # deprecated
line 5
```

### 示例 3：提取和格式化文本

提取 `/etc/shells` 中非注释的行（列出系统可用 Shell）：

```bash
$ sed '/^#/d' /etc/shells
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/fish
/bin/zsh
```

用正则提取 IP 地址（从配置文件中）：

```bash
$ echo "server=192.168.1.1:8080" | sed 's/.*=\([0-9.]*\).*/\1/'
192.168.1.1
```

批量给 Markdown 标题加编号（每行行首加 `# `）：

```bash
$ echo -e "简介\n安装\n使用" | sed 's/^/# /'
# 简介
# 安装
# 使用
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `sed: illegal option -- -` | macOS 上的 BSD sed 不支持 `--version` 等 GNU 风格的参数 | macOS 用 `brew install gnu-sed`，或记住 BSD sed 语法差异 |
| `-i` 报错需要传参数 | BSD sed 的 `-i` 强制要求接备份扩展名 | macOS 用 `sed -i '' 's/.../...' file`，Linux 用 `sed -i 's/.../...' file` |
| `s///` 替换路径时很别扭 | 路径含 `/` 与 sed 定界符冲突 | 改用其他定界符，如 `sed 's|/usr/bin|/usr/local/bin|'` |
| 替换没改所有出现 | 默认只替换每行第一个匹配 | 加 `g` 标记：`s/old/new/g` |
| 在管道里不输出结果 | 忘了 `-n` 和 `p` 配合使用 | 只想打印匹配行时：`sed -n '/pattern/p'` |

## 🔗 参考链接

- [GNU sed 手册](https://www.gnu.org/software/sed/manual/sed.html)
- [sed 单行脚本速查](http://sed.sourceforge.net/sed1line.txt)
- macOS 安装 GNU sed：`brew install gnu-sed`（命令为 `gsed`）
