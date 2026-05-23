# awk — 文本模式扫描与处理语言

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Alfred Aho、Peter Weinberger、Brian Kernighan（AT&T Bell Labs） |
| 首次发布 | 1977年 |
| 用途 | 逐行扫描文本文件，按模式匹配并处理字段，适合日志分析、报表生成、CSV 处理 |

## 🎯 用来做什么？

- **日志分析** — 从几十 MB 的日志里快速提取 IP、时间、错误码等字段
- **CSV/TSV 处理** — 按列筛选、统计、格式化，比 Excel 快，比写 Python 省事
- **生成报表** — 求和、平均、计数、分组聚合一行搞定
- **管道过滤** — 配合 grep、sed、sort 等工具做链式文本处理

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `awk '{print $1}' file` | 打印每行第一个字段（默认空格/Tab 分隔） |
| `awk '{print $1, $3}' file` | 打印第 1 和第 3 字段 |
| `awk '$2 > 50 {print $0}' file` | 筛选第 2 字段大于 50 的行 |
| `awk -F',' '{print $1}' file.csv` | 按逗号分隔（处理 CSV） |
| `awk '{sum += $1} END {print sum}' file` | 对第一列求和 |
| `awk 'NR > 1 && NR < 10' file` | 打印第 2 到第 9 行（跳过表头） |
| `awk '{count[$1]++} END {for (k in count) print k, count[k]}' file` | 按第一列分组计数 |
| `awk 'length($0) > 80' file` | 找出超过 80 个字符的行 |

## 💡 日常使用示例

### 示例 1：从成绩表中提取信息

假设有一个成绩文件 `/tmp/scores.txt`，内容是 Tab 分隔的姓名和分数：

```
$ cat /tmp/scores.txt
Alice	85
Bob	92
Charlie	78
David	95
Eve	88
```

提取所有人名：

```bash
$ awk '{print $1}' /tmp/scores.txt
Alice
Bob
Charlie
David
Eve
```

筛选分数 ≥ 90 的"优秀"同学：

```bash
$ awk '$2 >= 90 {print $1, "优秀"}' /tmp/scores.txt
Bob 优秀
David 优秀
```

计算总分和平均分：

```bash
$ awk '{sum += $2; count++} END {print "总分:", sum, "平均:", int(sum/count)}' /tmp/scores.txt
总分: 438 平均: 87
```

### 示例 2：分析 Web 服务器访问日志（Nginx/Apache）

典型的日志行格式：IP - - [日期] "METHOD URL HTTP/1.1" 状态码 字节数

```bash
$ awk '{print $1, $9}' /tmp/access.log
192.168.1.1 200
10.0.0.5 404
192.168.1.1 200
10.0.0.5 500
```

统计每个 IP 的访问次数：

```bash
$ awk '{count[$1]++} END {for (ip in count) print ip, count[ip]}' /tmp/access.log
192.168.1.1 2
10.0.0.5 2
```

统计每种 HTTP 状态码的数量：

```bash
$ awk '{status[$9]++} END {for (s in status) print s, status[s]}' /tmp/access.log
200 2
404 1
500 1
```

### 示例 3：处理 CSV 文件

```bash
$ cat /tmp/products.csv
name,price,stock,catgory
苹果,5.5,100,水果
香蕉,3.0,200,水果
牛奶,12.0,50,乳制品

$ awk -F',' 'NR > 1 && $3 < 100 {print $1, "库存不足:", $3}' /tmp/products.csv
牛奶 库存不足: 50
```

- `-F','` — 指定逗号为分隔符
- `NR > 1` — 跳过表头行
- `$3 < 100` — 只处理库存小于 100 的行

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `$0` 和 `$1` 分不清 | `$0` = 整行，`$1` = 第一个字段 | 记住 `$0` 是完整行，`$1` 起是字段 |
| 分隔符不是空格 | 默认以空白（空格/Tab）分隔 | 用 `-F` 选项，如 `-F','` |
| macOS 上 awk 版本较老 | macOS 自带的是 2020 年版 | 用 `brew install gawk` 安装 GNU awk |
| 条件判断语法写错 | awk 里 `if` 可以用在外层，但简写更常见 | 模式/动作结构 `条件 {动作}`，无需写 `if` |
| 处理大文件时报错 | awk 默认字段数有限制 | 用 `gawk` 或处理前先 `awk '{NF=NF}1'` 优化 |

## 🔗 参考链接

- [GNU Awk 用户指南](https://www.gnu.org/software/gawk/manual/gawk.html)
- [awk 单行脚本速查](http://www.pement.org/awk/awk1line.txt)
- 安装 GNU awk：`brew install gawk`
