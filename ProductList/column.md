# column — 终端表格排版工具

> 把杂乱的文本对齐成整齐的表格，终端里看数据不再眼花。

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Unix 基础工具（util-linux） |
| 首次发布 | 1980 年代（4.3BSD） |
| 用途 | 将文本数据按列对齐排版，生成易读的表格 |
| macOS 版本 | 自带 `/usr/bin/column`，与 Linux 版略有不同 |

## 🎯 用来做什么？

- **格式化表格数据** — TSV/CSV 文件或管道输出转成对齐的列
- **配合其他命令美化输出** — `ps`、`mount`、`df` 的输出用 `column` 排版更好看
- **查看结构化文本** — 数据库导出、日志文件、配置列表看一眼就能对齐

## 🔧 常用命令

| 命令 | 作用 |
|------|------|
| `column file.txt` | 以空格分隔，自动对齐列 |
| `column -t file.txt` | 表格模式：检测分隔符（默认空格/Tab）并对齐 |
| `column -s, -t file.csv` | 指定分隔符为逗号，表格模式对齐 |
| `column -s: -t /etc/passwd` | 按冒号分列，对齐 /etc/passwd |
| `column -c 80 file.txt` | 指定输出宽度为 80 列（填充多列） |
| `command \| column -t` | 管道输入，实时对齐 |

> **注意**：macOS 版 `column` 不提供 Linux 版 `-J`（JSON）、`-N`（列名）等高级选项。

## 💡 日常使用示例

### 示例 1：对齐 ps 输出

```bash
# ps 默认输出很乱
ps aux | head -5

# 用 column -t 对齐（排除表头）
ps aux | head -5 | column -t
```

输出示例：
```
USER       PID  %CPU  %MEM  VSZ      RSS   TT  STAT  STARTED    TIME       COMMAND
mk         431  0.5   2.3   4083456  87688  ??  S     10:15AM    0:12.34    /Applications/iTerm2
mk         897  0.1   1.2   3456784  54321  ??  S     10:20AM    0:03.21    /Applications/Safari
```

### 示例 2：对齐 CSV 或 TSV 文件

```bash
# 制表符分隔的数据
printf "name\tage\tcity\nalice\t30\tbeijing\nbob\t25\tshanghai\n" > /tmp/people.tsv
column -t /tmp/people.tsv
```

输出：
```
name   age  city
alice  30   beijing
bob    25   shanghai
```

### 示例 3：对齐 /etc/passwd

```bash
column -s: -t /etc/passwd | head -5
```

输出：
```
nobody       *   -2  -2  Unprivileged User    /var/empty               /usr/bin/false
root         *   0   0   System Administrator  /var/root                /bin/sh
daemon       *   1   1   System Services       /var/root                /usr/bin/false
_uucp        *   4   4   Unix to Unix Copy     /var/spool/uucp          /usr/sbin/uucico
_taskgated   *   13  13  Task Gate             /var/empty               /usr/bin/false
```

### 示例 4：配合 df 看磁盘

```bash
df -h | column -t
```

输出（对齐后）：
```
Filesystem     Size   Used   Avail   Capacity   Mounted   on
/dev/disk1s1   233G   120G   100G    55%        /
/dev/disk1s4   233G   20G    100G    10%        /System/Volumes/VM
```

## 🚨 常见坑点

| 症状 | 原因 | 解决 |
|------|------|------|
| 输出了莫名其妙的多列 | 默认用空格分列，有空格的值被拆散了 | 用 `-s` 指定唯一分隔符（如 Tab 或逗号） |
| 忘了加 `-t`，输出完全不对 | 没有 `-t` 时 column 会按列填充宽度（像报纸排版），不是对齐表格 | 大多数时候你应该用 `column -t` |
| 中文对齐不齐 | 中文字符占 2 个英文字符宽度，column 按半角计算 | 用英文字段名，或减少中文长度 |
| 某列数据太长整行被截断 | 终端宽度不够 | 用 `-c` 显式指定宽度：`column -t -c 200 file` |
| 和 Linux 上的输出不一样 | macOS 版 column 功能较少 | 如果需要 Linux 版的功能（-J, -N, --table-*），安装 `coreutils`：`brew install coreutils`，然后用 `gcolumn` |

## 🔗 参考链接

- [macOS column man page](https://man.freebsd.org/cgi/man.cgi?query=column)
- [GNU column 文档（需要 coreutils）](https://www.gnu.org/software/coreutils/manual/html_node/column-invocation.html)