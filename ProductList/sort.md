# sort — 文本行排序工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | 贝尔实验室（Unix 原生命令） |
| 首次发布 | 1970 年代早期 |
| 用途 | 将文本文件的每一行按指定规则排序（字母序、数字序、按字段排序），是文本分析管道的基础工具 |

## 🎯 用来做什么？

- **排序数据** — 把无序的列表排成字母或数字顺序
- **配合 uniq 去重** — `sort file | uniq` 是经典去重组合（uniq 只能处理相邻重复行）
- **按字段排序** — 对 CSV 或表格数据的指定列排序（如按成绩、日期、价格）
- **找出最大/最小值** — `sort -n | tail -1` 找最大值，`sort -n | head -1` 找最小值

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `sort file` | 按字母序排序（默认） |
| `sort -n file` | 按数字大小排序（解决 10 排在 2 前面的问题） |
| `sort -r file` | 逆序排序 |
| `sort -u file` | 排序并去重（等效于 `sort file \| uniq`） |
| `sort -k N file` | 按第 N 列（字段）排序 |
| `sort -t',' file` | 指定分隔符（-t 后跟分隔符） |
| `sort -k2 -n file` | 按第 2 列数值排序 |
| `command \| sort \| uniq` | 管道配合去重，数据清洗标配 |

## 💡 日常使用示例

### 示例 1：成绩排序

```bash
# 创建成绩表（姓名 分数）
cat > /tmp/grades.txt << 'EOF'
Alice 85
Bob 92
Charlie 78
Diana 95
Eve 88
EOF

# 按分数升序
sort -k2 -n /tmp/grades.txt
# 输出：
# Charlie 78
# Alice 85
# Eve 88
# Bob 92
# Diana 95

# 按分数降序（最高分在前）
sort -k2 -rn /tmp/grades.txt
# 输出：
# Diana 95
# Bob 92
# Eve 88
# Alice 85
# Charlie 78
```

### 示例 2：处理 CSV 数据

```bash
# 创建 CSV 文件（以逗号分隔）
cat > /tmp/sales.csv << 'EOF'
Apple,100,2025
Banana,50,2026
Cherry,200,2025
Date,75,2026
EOF

# 按第 2 列（销售额）排序，指定分隔符为逗号
sort -t',' -k2 -n /tmp/sales.csv
# 输出：
# Banana,50,2026
# Date,75,2026
# Apple,100,2025
# Cherry,200,2025
```

### 示例 3：找出占用磁盘最大的文件

```bash
# 列出当前目录文件大小，按大小排序
ls -lh *.log 2>/dev/null | sort -k5 -rh | head -5
# -k5：第 5 列是文件大小
# -r：从大到小
# -h：人类可读大小（Mac 上 ls -lh 输出，sort 的 -h 选项需要 GNU sort）
# 注意：macOS 上 sort 不支持 -h，用 sort -n 替代
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|---------|
| 数字排序错误 | 10 排在了 2 前面（`sort` 默认按字母序） | 用 `sort -n` 按数值排序 |
| 字段分隔符不对 | 按第 2 列排序结果不对 | 明确指定分隔符 `-t','` 或 `-t' '` |
| macOS 不支持 `-h` | `sort -h` 报错 | macOS 是 BSD 版 sort，不支持 human-numeric。可用 `ls -lS`（按大小排序）或安装 GNU sort |
| 中文排序乱 | 中文字符排序顺序不合预期 | macOS 上可用 `sort -d`（字典序），或 `export LANG=zh_CN.UTF-8` |
| 排序后 uniq 仍不生效 | 只用了 `uniq` 没先 `sort` | uniq 只去除**相邻**重复行，必须先 sort |

## 🔗 参考链接

- [GNU Coreutils: sort](https://www.gnu.org/software/coreutils/manual/html_node/sort-invocation.html)
- [macOS sort man page](https://ss64.com/osx/sort.html)
