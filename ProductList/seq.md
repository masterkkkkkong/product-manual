# seq — 数字序列生成器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | GNU Coreutils / macOS 内置 |
| 首次发布 | 1990s |
| 用途 | 生成等差数字序列，常用于循环、批量创建文件、测试数据生成 |

## 🎯 用来做什么？

- **生成连续数字列表** — 快速生成 1 到 N 的数字，每行一个
- **for 循环中生成迭代范围** — 配合 Shell 脚本做批量操作
- **生成带前导零或自定义分隔符的序列** — 用于命名文件、生成测试数据

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `seq 5` | 生成 1 到 5，每行一个 |
| `seq 3 8` | 生成 3 到 8 |
| `seq 0 2 10` | 从 0 开始，步进 2，到 10 |
| `seq -w 1 10` | 等宽输出（数字前补零对齐） |
| `seq -s ', ' 1 5` | 用指定分隔符替换换行符 |
| `seq -f "%.2f" 1 0.5 3` | 格式化输出（浮点数） |
| `seq -t "," 1 3` | 末尾也加分隔符 |

> macOS 上 `seq` 功能基本与 GNU 版一致，但 `--help` 不支持，用 `man seq`。

## 💡 日常使用示例

### 1️⃣ 批量生成文件名

```bash
$ seq -w 1 12
01
02
03
04
05
06
07
08
09
10
11
12

# 配合 touch 批量创建文件
$ for i in $(seq -w 1 5); do touch "chapter-$i.md"; done
$ ls chapter-*.md
chapter-01.md  chapter-02.md  chapter-03.md  chapter-04.md  chapter-05.md
```

等宽输出保证了排序正确（`01` 不会排到 `10` 后面）。

### 2️⃣ 生成自定义分隔符的列表

```bash
# 用逗号分隔，适合拼 SQL IN 子句
$ seq -s ', ' 100 105
100, 101, 102, 103, 104, 105,

# 去掉末尾的逗号（用 printf + 参数替换）
$ seq -s ', ' 100 105 | sed 's/, $//'
100, 101, 102, 103, 104, 105
```

### 3️⃣ 浮点数序列（步进可以是小数）

```bash
$ seq -f "%.1f" 0 0.5 3
0.0
0.5
1.0
1.5
2.0
2.5
3.0
```

### 4️⃣ 在 for 循环中批量操作

```bash
# 批量重命名文件
$ for i in $(seq 5); do mv "img_${i}.jpg" "photo_$(printf "%03d" $i).jpg"; done

# 批量创建测试目录
$ for i in $(seq -w 1 5); do mkdir "test_run_$i"; done
$ ls -d test_run_*
test_run_01  test_run_02  test_run_03  test_run_04  test_run_05
```

### 5️⃣ 生成测试 CSV 数据

```bash
$ for i in $(seq 5); do echo "$i,$((i * 10)),$(date -v+"${i}d" +%Y-%m-%d)"; done
1,10,2026-06-14
2,20,2026-06-15
3,30,2026-06-16
4,40,2026-06-17
5,50,2026-06-18
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `seq --help` 报错 | macOS 的 `seq` 来自 BSD，不支持 GNU 风格长参数 | 用 `man seq` 查看帮助 |
| `seq 5` 结果带 `.0` 后缀 | 浮点数默认格式化会有小数位 | 不用 `-f` 时默认是整数；若浮点输入则用 `-f "%.0f"` 去小数 |
| 大数字序列很慢 | `seq 1 100000` 会输出 10 万行，在终端中卡住 | 重定向到文件 `seq 1 100000 > numbers.txt` |
| 分隔符末尾多一个 | `-s` 在每个元素后都加分隔符，末尾也有 | 用 `sed 's/, $//'` 或 `tr '\n' ',' | sed 's/,$//'` |
| macOS 上步进不支持负数 | BSD `seq` 限制 | 用 `jot` 或 `for i in $(seq 10 -1 1)` 反向 |

## 🔗 参考链接

- [macOS seq 手册](https://ss64.com/mac/seq.html)
- [GNU seq 手册](https://www.gnu.org/software/coreutils/manual/html_node/seq-invocation.html)
