# Regex — 正则表达式

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Stephen Cole Kleene（理论起源），Ken Thompson（grep 实现），Henry Spencer / Philip Hazel（库实现） |
| 首次发布 | 1950s（理论），1973 年（grep 首个实现） |
| 用途 | 用模式（pattern）匹配、查找、替换文本的一门微型语言，几乎所有编程语言和文本编辑器都支持 |

## 🎯 用来做什么？

- **文本搜索与过滤**：从日志、配置文件、CSV 中快速找出符合条件的内容——比肉眼找快一万倍
- **批量查找替换**：一次性替换多种模式，比如把 `user_name` 改成 `userName` 这样的命名转换
- **数据提取**：从非结构化文本（API 响应、HTML、日志）中提取特定字段
- **输入验证**：校验邮箱、手机号、URL 等格式是否符合规范

## 🔧 常用语法

| 模式 | 说明 |
|------|------|
| `.` | 匹配任意单个字符（除换行符） |
| `\d` | 匹配一个数字，等价于 `[0-9]` |
| `\w` | 匹配一个字母/数字/下划线，等价于 `[a-zA-Z0-9_]` |
| `\s` | 匹配一个空白字符（空格、Tab、换行） |
| `^` | 匹配字符串的开头 |
| `$` | 匹配字符串的结尾 |
| `*` | 前一个字符出现 0 次或多次 |
| `+` | 前一个字符出现 1 次或多次 |
| `?` | 前一个字符出现 0 次或 1 次 |
| `{n,m}` | 前一个字符出现 n 到 m 次 |
| `[abc]` | 匹配方括号中的任意一个字符 |
| `[^abc]` | 匹配除方括号内字符以外的任意字符 |
| `(expr)` | 分组捕获，将匹配的内容存下来供后续引用 |
| `(?:expr)` | 非捕获分组，只用来控制优先级，不存内容 |
| `\|` | 或运算，`cat\|dog` 匹配 "cat" 或 "dog" |

**常用命令速查**

| 命令 | 说明 |
|------|------|
| `grep 'pattern' file.txt` | 在文件中搜索包含 pattern 的行 |
| `grep -i 'pattern' file.txt` | 忽略大小写搜索 |
| `grep -r 'pattern' .` | 递归搜索当前目录所有文件 |
| `grep -o 'pattern' file.txt` | 只输出匹配的部分（而不是整行） |
| `sed 's/old/new/g' file.txt` | 将文件中所有 old 替换为 new |
| `sed -E 's/pattern/replace/g' file.txt` | 启用扩展正则（支持 +、?、{}） |
| `awk '/pattern/ {print $1}' file.txt` | 输出匹配行的第一列 |

## 💡 日常使用示例

### 示例 1：从日志中提取 IP 地址

```bash
# 假设 access.log 内容如下：
# 192.168.1.1 - - [10/May/2026:12:00:00 +0800] "GET /api/users HTTP/1.1" 200 1234
# 10.0.0.5 - - [10/May/2026:12:01:00 +0800] "POST /api/login HTTP/1.1" 401 56

# 提取所有 IP 地址（IPv4 格式）：
grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' access.log
# 输出：
# 192.168.1.1
# 10.0.0.5

# 统计每个 IP 的访问次数：
grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' access.log | sort | uniq -c | sort -rn
# 输出：
# 1 192.168.1.1
# 1 10.0.0.5
```

### 示例 2：批量替换文件名中的空格

```bash
# 场景：一堆文件中文件名带空格，想统一替换为下划线
# 先看看有哪些文件：
ls -1 *.txt
# "my notes.txt"
# "project report.txt"
# "final version.txt"

# 用 sed 替换（注意 macOS 和 Linux 的 sed 差异很大，这里用 rename 或 bash 循环更安全）：
for f in *.txt; do
  mv "$f" "$(echo "$f" | sed 's/ /_/g')"
done

# 验证：
ls -1 *.txt
# my_notes.txt
# project_report.txt
# final_version.txt
```

### 示例 3：验证和提取邮箱地址

```bash
# 匹配标准邮箱格式（简化版，不覆盖所有边界情况）：
echo "联系我: alice@example.com 或者 bob@gmail.com" | grep -oE '\b[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}\b'
# 输出：
# alice@example.com
# bob@gmail.com

# 在 Python 中用正则提取：
python3 -c "
import re
text = '用户邮箱：alice@example.com，备用邮箱：bob@gmail.com'
emails = re.findall(r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}', text)
print(emails)
"
# 输出：
# ['alice@example.com', 'bob@gmail.com']
```

## 🚨 常见坑点

| 坑点 | 说明 | 解决方法 |
|------|------|---------|
| 贪婪匹配 vs 懒惰匹配 | `.*` 会匹配到最后一个符合条件的字符，不是第一个 | 用 `.*?` 表示懒惰（最小）匹配，匹配到第一个就停 |
| macOS 的 sed 不支持 `-r`/`\d` | macOS 自带的 sed 是 BSD 版，和 Linux GNU sed 不同 | 在 macOS 上用 `sed -E` 替代 `sed -r`，用 `[0-9]` 替代 `\d`；或安装 gsed：`brew install gnu-sed` |
| 转义符地狱 | 在命令行写正则时，shell 也会解释 `\`、`$`、`*` 等符号 | 用单引号包裹正则可以防止 shell 转义（如 `grep 'pattern'`）；在字符串中需要 `\\d` 来表示 `\d` |
| 不同工具正则方言不同 | grep 默认是基础正则（BRE），不支持 `+`、`?`、`{}` | grep 加 `-E` 启用扩展正则（ERE）；Python/re 用另一个语法；JavaScript 又不同 | 
| 回溯灾难（Catastrophic Backtracking） | 复杂嵌套重复模式（如 `(a+)+b`）匹配长字符串时会导致 CPU 100% | 避免嵌套量词（如 `(a+)+`、`(.*)*`）；用原子分组 `(?>...)`、或使用分词器替代纯正则 |
| ^ 和 $ 的含义错觉 | `^` 匹配行首，`$` 匹配行尾，不是整个字符串的首尾 | 在多行模式下 `^` 和 `$` 行为不变；如果需匹配整个字符串，用 `\A` 和 `\Z`（某些引擎支持） |

## 🔗 参考链接

- [Regex101 — 在线测试和调试正则表达式](https://regex101.com/)
- [RegExr — 可视化正则学习工具](https://regexr.com/)
- [Regex 文档（JavaScript）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_expressions)
- [Python re 模块文档](https://docs.python.org/3/library/re.html)
- [Mastering Regular Expressions 书籍（Jeffrey Friedl 著）](https://www.oreilly.com/library/view/mastering-regular-expressions/0596528124/)
