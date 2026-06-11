# tr — 字符替换与删除工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs (Unix 原生工具) |
| 首次发布 | 1970s (Version 7 Unix) |
| 用途 | 对文本流中的字符进行替换、删除、压缩，不做单词级操作 |

## 🎯 用来做什么？

- **大小写转换** — 把文件内容一键转大写/小写
- **删除指定字符** — 去掉文本中的空格、标点、换行符
- **字符压缩** — 把连续重复的字符压缩成单个（如多个空格→一个）
- **字符替换** — 把一种字符集映射为另一种（如 DOS 换行→Unix 换行）

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `tr 'a-z' 'A-Z'` | 小写转大写 |
| `tr 'A-Z' 'a-z'` | 大写转小写 |
| `tr -d 'aeiou'` | 删除所有元音字母 |
| `tr -s ' '` | 压缩连续空格为单个空格 |
| `tr -d '\n'` | 删除所有换行符（把多行合并成一行） |
| `tr '[:lower:]' '[:upper:]'` | 用 POSIX 字符类做大小写转换（推荐） |
| `tr -cd 'a-zA-Z0-9'` | 只保留字母和数字，删除其他字符 |

## 💡 日常使用示例

### 示例 1：快速把文件内容转大写

```bash
# 原内容
echo "hello world" | tr '[:lower:]' '[:upper:]'
# 输出: HELLO WORLD

# 处理文件（不修改原文件）
cat notes.txt | tr 'a-z' 'A-Z' > notes_uppercase.txt
```

### 示例 2：清理混乱的空格

```bash
# 文本里有多个空格和多余换行
echo "too    many    spaces" | tr -s ' '
# 输出: too many spaces

# 去掉空行（连续换行符压缩为单个）
cat messy.txt | tr -s '\n'
```

### 示例 3：数据清洗 — 只保留数字和字母

```bash
# 假设有一个 CSV 里有脏数据
echo "user@123! #price$" | tr -cd 'a-zA-Z0-9 \n'
# 输出: user123 price
```

## 🚨 常见坑点

| 坑 | 原因 | 解决方法 |
|----|------|---------|
| `tr` 不支持单词/正则 | tr 只处理字符，不做单词替换 | 用 `sed` 或 `awk` 做单词级替换 |
| macOS 上 `tr` 不支持 `[a-z]` 类以外的复杂范围 | 与 GNU tr 差异 | 使用 POSIX 字符类 `[:lower:]` 替代 |
| 没有 `-i` 原地修改 | tr 只处理 stdin/stdout，不能直接改文件 | 用重定向写回：`tr ... < file > tmp && mv tmp file` |
| 中文/Unicode 多字节字符可能出问题 | tr 只处理单字节 | 用 `sed` 或 Python 处理 Unicode 文本 |

## 🔗 参考链接

- [GNU tr 官方文档](https://www.gnu.org/software/coreutils/manual/html_node/tr-invocation.html)
- `man tr`（macOS 手册页）
