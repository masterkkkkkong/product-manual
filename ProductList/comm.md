# comm — 文件行对比工具

> 逐行比较两个已排序文件，找出共有和独有的内容。

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Unix 基础工具（POSIX） |
| 首次发布 | 1980 年代（4.3BSD） |
| 用途 | 比较两个已排序的文本文件，输出它们的共有行和各自独有行 |
| macOS 版本 | 自带 `/usr/bin/comm` |
| 相近工具 | `diff`（行对比 + 差异展示）、`cmp`（字节对比）、`grep -f`（交集/差集） |

## 🎯 用来做什么？

- **找两个列表的交集和差集** — A 文件哪些行 B 也有？A 独有的有哪些？
- **核对数据一致性** — 名单对比、API 返回值与预期值的差异检查
- **配合 sort 做文件分析** — 先用 `sort` 排序，再用 `comm` 对比

## 🔧 常用命令

| 命令 | 作用 |
|------|------|
| `comm file1 file2` | 三列输出：A独有 \| 共有 \| B独有 |
| `comm -1 file1 file2` | 去掉第一列（不显示 A 独有的行） |
| `comm -2 file1 file2` | 去掉第二列（不显示共有行） |
| `comm -3 file1 file2` | 去掉第三列（不显示 B 独有的行） |
| `comm -12 file1 file2` | 只显示共有行（交集） |
| `comm -23 file1 file2` | 只显示 A 独有的行（差集 A-B） |
| `comm -13 file1 file2` | 只显示 B 独有的行（差集 B-A） |
| `comm -i file1 file2` | 忽略大小写比较 |
| `comm --check-order` | （Linux 版）检查文件是否已排序，macOS 无此选项 |

> ⚠️ **重要前提**：两个文件必须已排序。未排序的结果无意义。

## 💡 日常使用示例

### 示例 1：找两个列表的交集和差集

```bash
# 准备两个文件
printf "apple\nbanana\ncherry\ndate\n" > /tmp/fruits_a.txt
printf "banana\ndate\nelderberry\nfig\n" > /tmp/fruits_b.txt

# comm 默认输出三列
comm /tmp/fruits_a.txt /tmp/fruits_b.txt
```

输出：
```
apple
		banana
cherry
		date
	elderberry
	fig
```

三列含义：
- 第一列（无缩进）= A 独有的：apple, cherry
- 第二列（1 个 Tab）= B 独有的：elderberry, fig
- 第三列（2 个 Tab）= 两者共有的：banana, date

### 示例 2：只取共有行（交集）

```bash
comm -12 /tmp/fruits_a.txt /tmp/fruits_b.txt
```

输出：
```
banana
date
```

### 示例 3：只取独有行（差集）

```bash
# A 独有的（在 A 不在 B）
comm -23 /tmp/fruits_a.txt /tmp/fruits_b.txt
# 输出: apple  cherry

# B 独有的（在 B 不在 A）
comm -13 /tmp/fruits_a.txt /tmp/fruits_b.txt
# 输出: elderberry  fig
```

### 示例 4：实用场景—比对用户列表

```bash
# 假设有两个用户名单
sort /etc/passwd | cut -d: -f1 > /tmp/local_users.txt
printf "root\ndaemon\npostgres\nnginx\n" > /tmp/required_users.txt

# 找出系统里没有的必需用户
comm -23 /tmp/required_users.txt /tmp/local_users.txt
# 输出: nginx  postgres  ← 需要创建
```

## 🚨 常见坑点

| 症状 | 原因 | 解决 |
|------|------|------|
| 输出完全乱套 | 文件没排序。comm 假设文件已排序，未排序时结果不可靠 | 先 `sort file > file_sorted` 再对比 |
| comm: file 1 is not in sorted order | Linux 版会用 `--check-order` 报错，macOS 不会报错但结果错 | macOS 上自己负责排序，或者用 `diff` 代替 |
| 三列挤在一起看不清 | 默认输出用 Tab 分隔，内容已含 Tab 会错位 | 用 `column -t` 排版：`comm file1 file2 \| column -t` |
| 大小写导致漏匹配 | "Apple" 和 "apple" 被当成不同行 | 加 `-i` 忽略大小写（macOS 支持） |
| 想对比但不想排序原文件 | comm 要求排序，不想动原文件 | `comm <(sort file1) <(sort file2)` 用进程替换 |
| 和 diff 的区别 | diff 输出差异上下文，comm 只输出行列存在性 | 要"多了哪些、少了哪些"用 comm；要看逐行差异用 diff |

### 进程替换写法（免去创建临时文件）

```bash
comm -12 <(sort file1.txt) <(sort file2.txt)
```

这个写法直接在 shell 中对文件排序再输入到 comm，不污染原始文件。

## 🔗 参考链接

- [macOS comm man page](https://man.freebsd.org/cgi/man.cgi?query=comm)
- [GNU coreutils comm 文档](https://www.gnu.org/software/coreutils/manual/html_node/comm-invocation.html)