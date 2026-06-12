# printf — 格式化输出工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs / GNU Coreutils |
| 首次发布 | 1970s |
| 用途 | 按指定格式输出文本和变量值，支持字符串、整数、浮点数、进制转换等格式化 |

## 🎯 用来做什么？

- **格式化输出变量** — 对齐表格、补零、设置小数位数
- **进制转换** — 十进制 ↔ 十六进制 ↔ 八进制
- **Shell 脚本中生成格式化字符串** — 日期格式化、数字补位、数据清洗
- **代替 echo 做更精确的输出控制** — echo 不支持换行控制、格式化

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `printf "hello\n"` | 输出字符串（`\n` 换行，echo 不需要但 printf 必须写） |
| `printf "%s\n" "text"` | 以字符串格式输出 |
| `printf "%d\n" 42` | 以十进制整数输出 |
| `printf "%x\n" 255` | 以十六进制输出 |
| `printf "%o\n" 255` | 以八进制输出 |
| `printf "%.2f\n" 3.14159` | 保留两位小数 |
| `printf "%08d\n" 42` | 补零到 8 位 |
| `printf "| %-10s \| %5s \|\n" Name Score` | 左对齐/右对齐表格列 |

## 💡 日常使用示例

### 1️⃣ 生成对齐表格

```bash
$ printf "| %-10s | %5s | %4s |\n" "Name" "Score" "Grade"
| Name       | Score | Grade |
$ printf "| %-10s | %5d | %4s |\n" "Alice" 95 "A"
| Alice      |    95 |    A |
$ printf "| %-10s | %5d | %4s |\n" "Bob" 87 "B+"
| Bob        |    87 |   B+ |
```

`%-10s` = 左对齐占 10 格，`%5d` = 右对齐占 5 格。

### 2️⃣ 补零与进制转换

```bash
# 数字补零（批量文件命名必备）
$ for i in 1 2 10 100; do printf "photo_%03d.jpg\n" $i; done
photo_001.jpg
photo_002.jpg
photo_010.jpg
photo_100.jpg

# 十进制转十六进制
$ printf "255 = 0x%x = 0377\n" 255 255
255 = 0xff = 0377

# 十进制转八进制
$ printf "%d in octal is %o\n" 64 64
64 in octal is 100
```

### 3️⃣ 浮点数精度控制

```bash
# 保留 2 位小数
$ printf "Pi ≈ %.2f\n" 3.1415926535
Pi ≈ 3.14

# 科学计数法
$ printf "%e\n" 1234567
1.234567e+06

# 千位分隔符（macOS printf 不支持 %'d，GNU 版支持）
$ python3 -c "print(f'{1234567:,}')"
1,234,567
```

> **macOS 差异**：`printf` 的 `%'d`（千位分隔符）在 BSD 版中不支持。用 Python 或 `numfmt` 替代。

### 4️⃣ 给变量赋值（配合命令替换）

```bash
# 格式化日期编号
$ day=$(printf "%02d" 5)
$ echo $day
05

# 生成带编号的 SQL
$ for i in $(seq 3); do
    printf "INSERT INTO users VALUES (%d, 'user_%03d\@example.com');\n" $i $i
  done
INSERT INTO users VALUES (1, 'user_001@example.com');
INSERT INTO users VALUES (2, 'user_002@example.com');
INSERT INTO users VALUES (3, 'user_003@example.com');
```

### 5️⃣ 输出彩色文本（转义序列）

```bash
# ANSI 颜色码：\033[颜色代码m
$ printf "\033[32m%s\033[0m\n" "绿色文字"
$ printf "\033[31m%s\033[0m\n" "红色文字"
$ printf "\033[1m%s\033[0m\n" "粗体文字"

# 组合：绿底黑字
$ printf "\033[42m\033[30m 成功 \033[0m 操作已完成\n"
```

> 在终端中会显示彩色效果（这里无法渲染，在真正的终端里试一试）。

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| 输出没有换行 | `printf` 不会自动加换行符，和 `echo` 不同 | 手动加 `\n`：`printf "hello\n"` |
| `%` 没有输出 | `%` 在 printf 中是转义字符 | 用 `%%` 输出一个字面量 `%` |
| macOS 上 `%'d` 不生效 | BSD `printf` 不支持千位分隔符 | 用 Python 或 `numfmt --grouping` |
| 浮点数格式化结果不对 | 使用了 `%d` 而非 `%f` | 整数用 `%d`，浮点数用 `%f` |
| `printf $var` 不安全 | 变量中包含 `%` 会出错 | 总是用 `printf "%s" "$var"` |

## 🔗 参考链接

- [macOS printf 手册](https://ss64.com/mac/printf.html)
- [GNU printf 手册](https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html#index-printf)
- [printf 格式说明符速查](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/printf.html)
