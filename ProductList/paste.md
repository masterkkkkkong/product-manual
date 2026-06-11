# paste — 文件行合并工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs (Unix 原生工具) |
| 首次发布 | 1970s (Version 7 Unix) |
| 用途 | 将多个文件的行按列合并，或把一个文件的各行合并为单行 |

## 🎯 用来做什么？

- **按列合并文件** — 把两个文件的对应行拼成一行（类似 SQL JOIN）
- **创建 CSV/TSV** — 把多个单列数据文件合并为表格
- **串行化输出** — 把一个文件的多行内容转为单行（用分隔符连接）
- **构建命令参数** — 把多行文本转为单行参数列表

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `paste file1 file2` | 把两个文件的对应行用 Tab 拼接 |
| `paste -d, file1 file2` | 用逗号作为分隔符拼接 |
| `paste -s file` | 把一个文件的所有行串成一行（默认 Tab 分隔） |
| `paste -s -d, file` | 串行化并用逗号分隔 |
| `paste -d '\n' file1 file2` | 用换行符分隔（相当于隔行交叉合并） |
| `paste - - < file` | 把文件两行合并为一行 |

## 💡 日常使用示例

### 示例 1：合并两个名单

```bash
# 准备数据
printf "Alice\nBob\nCharlie\n" > names.txt
printf "100\n85\n92\n" > scores.txt

# 默认 Tab 分隔合并
paste names.txt scores.txt
# 输出:
# Alice	100
# Bob	85
# Charlie	92

# 用逗号分隔（生成 CSV）
paste -d, names.txt scores.txt
# 输出:
# Alice,100
# Bob,85
# Charlie,92
```

### 示例 2：把多行转为单行参数

```bash
# 准备一个列表
printf "libssl-dev\nbuild-essential\ncurl\n" > pkgs.txt

# 串行化为空格分隔的参数行
paste -s -d' ' pkgs.txt
# 输出: libssl-dev build-essential curl

# 配合 xargs 安装
paste -s -d' ' pkgs.txt | xargs brew install
```

### 示例 3：隔行合并 — 把两行并为一行

```bash
# 每两行合并为一行（常用于配对数据）
printf "Name: Alice\nAge: 30\nName: Bob\nAge: 25\n" | paste - -
# 输出:
# Name: Alice	Age: 30
# Name: Bob	Age: 25
```

## 🚨 常见坑点

| 坑 | 原因 | 解决方法 |
|----|------|---------|
| 文件行数不一致时填充空行 | paste 用空行补齐短文件 | 先用 `wc -l` 确认行数一致 |
| 默认分隔符是 Tab，肉眼看不出来 | 终端显示 Tab 和空格相似 | 用 `cat -A` 或 `od -c` 检查实际分隔符 |
| `paste - -` 含义不直观 | `-` 代表 stdin，重复使用表示交替读取 | 记住：两个 `-` = 把输入流的两行合并 |
| macOS 上 `-d` 不支持多字符分隔符 | POSIX 限制，和 GNU 版本不同 | 用管道传给 `tr '\t' '|'` 做二次替换 |

## 🔗 参考链接

- [GNU paste 官方文档](https://www.gnu.org/software/coreutils/manual/html_node/paste-invocation.html)
- `man paste`（macOS 手册页）
