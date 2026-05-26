# cut — 文本列提取工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Bell Labs（AT&T Unix） |
| 首次发布 | 约 1982 年（System III） |
| 用途 | 从文本行中按分隔符或字符位置提取指定字段 |

## 🎯 用来做什么？

- **提取 CSV 的某一列**——比如只要日期列或金额列
- **截取固定宽度字段**——日志文件中每条记录前 10 个字符是时间戳
- **精简命令输出**——从 `ps`、`ls` 等命令的输出中只保留需要的字段
- **管道数据预处理**——喂给 `sort`、`uniq` 之前先裁出目标列

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `cut -d',' -f1 data.csv` | 按逗号分隔，取第 1 列 |
| `cut -d',' -f1,3 data.csv` | 按逗号分隔，取第 1 和第 3 列 |
| `cut -d':' -f1 /etc/passwd` | 按冒号分隔，取用户名 |
| `cut -c1-10 file.txt` | 取每行第 1~10 个字符 |
| `cut -c5- file.txt` | 从第 5 个字符取到行尾 |
| `cut -d$'\t' -f2 file.tsv` | 按 Tab 分隔（TSV 文件），取第 2 列 |

## 💡 日常使用示例

### 示例 1：提取 CSV 中的特定列

```bash
$ cat sales.csv
date,product,amount
2024-01-15,apple,3.50
2024-01-16,banana,2.00
2024-01-17,cherry,4.25

$ cut -d',' -f2 sales.csv
product
apple
banana
cherry

$ tail -n +2 sales.csv | cut -d',' -f3
3.50
2.00
4.25
```

### 示例 2：从日志中提取时间戳（固定宽度）

```bash
$ cat app.log
2024-01-15 08:30:01 INFO  Server started
2024-01-15 08:30:05 ERROR Connection failed
2024-01-15 08:30:10 INFO  Retry success

$ cut -c1-10 app.log
2024-01-15
2024-01-15
2024-01-15
```

### 示例 3：提取系统用户列表

```bash
$ cut -d':' -f1 /etc/passwd | head -5
nobody
root
daemon
_uucp
_taskgated
```

## 🚨 常见坑点

| 问题 | 原因 | 解决 |
|------|------|------|
| 分隔符是 Tab 但用了空格 | 肉眼分不清 Tab 和空格 | 用 `cut -d$'\t'` 明确指定 Tab，或先用 `cat -A` 查看隐藏字符 |
| CSV 中有引号包裹的逗号 | `"San Francisco, CA"` 里的逗号被当成分隔符 | `cut` 不支持引号转义，这种场景换用 `awk -F','` 或 Python 处理 |
| 输出顺序和 `-f` 参数不一致 | `-f3,1` 输出仍然是 3 再 1（按原顺序） | `cut` 保持原始列顺序，不会按参数重排。需要重排用 `awk` |
| 多字符分隔符不支持 | `-d` 只接受单个字符 | 需要多字符分隔符时用 `awk -F'::'` 代替 |

## 🔗 参考链接

- [cut (Unix) - Wikipedia](https://en.wikipedia.org/wiki/Cut_(Unix))
- macOS 手册：`man cut`
