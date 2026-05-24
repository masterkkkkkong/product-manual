# uniq — 去除重复行

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | 贝尔实验室（Unix 原生命令） |
| 首次发布 | 1970 年代早期 |
| 用途 | 从已排序的文本中报告或删除重复行，常与 `sort` 配合使用（`sort file \| uniq`） |

## 🎯 用来做什么？

- **去重统计** — 统计每个值的出现次数（如日志中每种错误类型出现了几次）
- **找出交集/差集** — 配合 `-d`（只显示重复行）和 `-u`（只显示不重复行）
- **数据清洗** — 去除重复的 IP、用户名、订单 ID
- **管道数据汇总** — `sort \| uniq -c \| sort -rn` 是最经典的分析管道，统计频次并排序

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `sort file \| uniq` | 去重（最常用） |
| `sort file \| uniq -c` | 统计每行出现的次数（显示计数） |
| `sort file \| uniq -d` | 只显示重复的行（出现过 2 次及以上） |
| `sort file \| uniq -u` | 只显示没有重复的行（只出现过 1 次） |
| `sort file \| uniq -i` | 忽略大小写去重 |
| `cut -d' ' -f1 file \| sort \| uniq -c` | 按第一列分组统计 |
| `sort file \| uniq -c \| sort -rn` | **经典管道**：统计频次并按频次降序排列 |

> ⚠️ **核心要点**：`uniq` 只能去除**相邻**的重复行，所以必须先 `sort`。除非你确定文件已经排好序了。

## 💡 日常使用示例

### 示例 1：基础去重和统计

```bash
# 创建示例文件
cat > /tmp/items.txt << 'EOF'
apple
banana
apple
cherry
banana
banana
date
EOF

# 排序后去重
sort /tmp/items.txt | uniq
# 输出：
# apple
# banana
# cherry
# date

# 统计每个水果的出现次数
sort /tmp/items.txt | uniq -c
# 输出：
#    2 apple
#    3 banana
#    1 cherry
#    1 date

# 按频次降序排列（最上面的出现最多）
sort /tmp/items.txt | uniq -c | sort -rn
# 输出：
#    3 banana
#    2 apple
#    1 date
#    1 cherry
```

### 示例 2：日志分析——统计每个 IP 的请求数

```bash
# 模拟 nginx 访问日志中的 IP 列表
cat > /tmp/access.log << 'EOF'
192.168.1.1
192.168.1.2
192.168.1.1
192.168.1.3
192.168.1.1
192.168.1.2
EOF

# 统计每个 IP 的访问次数（从多到少）
sort /tmp/access.log | uniq -c | sort -rn
# 输出：
#    3 192.168.1.1
#    2 192.168.1.2
#    1 192.168.1.3
```

### 示例 3：找出只出现一次的行（数据异常检测）

```bash
# 假设订单号列表
cat > /tmp/orders.txt << 'EOF'
ORD-001
ORD-002
ORD-001
ORD-003
ORD-002
EOF

# 找出只出现一次的订单（可能是遗漏？
sort /tmp/orders.txt | uniq -u
# 输出：
# ORD-003

# 找出重复的订单（重复提交？）
sort /tmp/orders.txt | uniq -d
# 输出：
# ORD-001
# ORD-002
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|---------|
| uniq 没有去重 | 重复行还在，`uniq file` 没效果 | 文件未排序！先 `sort file \| uniq` |
| 误以为 uniq 能全局去重 | 相隔两行的相同值没被去掉 | uniq 只比较相邻行，必先 sort |
| 统计数字看起来不对 | `uniq -c` 输出空格混乱 | 格式是固定的，`\t` 前是计数后是内容，用 sort 时正常 |
| 大小写敏感问题 | "Apple" 和 "apple" 被当成不同值 | 加 `-i`：`sort file \| uniq -ci` |

## 🔗 参考链接

- [GNU Coreutils: uniq](https://www.gnu.org/software/coreutils/manual/html_node/uniq-invocation.html)
- [macOS uniq man page](https://ss64.com/osx/uniq.html)
