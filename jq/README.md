# jq — JSON 命令行处理工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Stephen Dolan（现由 jqlang 社区维护） |
| 首次发布 | 2012 年 |
| 用途 | 在终端中解析、过滤、转换和格式化 JSON 数据 |
| macOS 自带 | ✅ 是（macOS Sequoia 自带，也可通过 Homebrew 升级） |

## 🎯 用来做什么？

- **解析 JSON 输出**：从 API 返回的 JSON 中提取特定字段
- **格式化 JSON**：把挤成一行的 JSON 变成美观的层级结构
- **过滤和查询**：用类似 SQL 的方式查询 JSON 数据
- **在 Shell 脚本中处理数据**：配合 curl 使用，一站式完成 API 请求和数据提取

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `jq . < file.json` | 格式化输出 JSON（美化打印） |
| `jq '.key' < file.json` | 提取指定键的值 |
| `jq '.[]' < file.json` | 遍历数组元素 |
| `jq '.[] \| select(.key > value)'` | 按条件过滤（类似 SQL WHERE） |
| `jq -r '.key' < file.json` | 原始输出，去掉 JSON 引号 |
| `jq -c '.' < file.json` | 紧凑输出（每行一个 JSON 对象） |
| `jq 'length' < file.json` | 获取数组长度或对象键数量 |
| `jq -s '.' file1.json file2.json` | 合并多个 JSON 文件为数组 |
| `jq '. \| {new_key: .old_key}' ` | 重塑 JSON 结构 |

## 💡 日常使用示例

### 示例 1：配合 curl 获取 API 数据并提取字段

```bash
# 从 GitHub API 获取 curl 仓库信息，只提取 star 数和描述
curl -s https://api.github.com/repos/curl/curl | jq '{name, stars: .stargazers_count, description}'
```

输出：

```json
{
  "name": "curl",
  "stars": 36000,
  "description": "A command line tool and library for transferring data with URL syntax"
}
```

用 `-r`（raw output）去掉 JSON 的引号，适合在脚本中赋值给变量：

```bash
stars=$(curl -s https://api.github.com/repos/curl/curl | jq -r '.stargazers_count')
echo "curl 的 Star 数：$stars"
# 输出：curl 的 Star 数：36000
```

### 示例 2：过滤数组中的元素（类似 SQL WHERE）

假设有一个包含用户列表的 JSON 文件 `users.json`：

```json
{
  "users": [
    {"name": "Alice", "age": 30, "city": "Beijing"},
    {"name": "Bob", "age": 25, "city": "Shanghai"},
    {"name": "Charlie", "age": 35, "city": "Shenzhen"}
  ]
}
```

```bash
# 找出年龄大于 28 的用户
cat users.json | jq '.users[] | select(.age > 28)'
```

输出：

```json
{
  "name": "Alice",
  "age": 30,
  "city": "Beijing"
}
{
  "name": "Charlie",
  "age": 35,
  "city": "Shenzhen"
}
```

```bash
# 只提取符合条件的用户名（原始字符串输出）
cat users.json | jq -r '.users[] | select(.age > 28) | .name'
# 输出：
# Alice
# Charlie
```

### 示例 3：重塑 JSON 结构

```bash
# 假设有一个简单的 JSON 对象
echo '{"name":"Alice","age":30,"email":"alice@example.com"}' | \
  jq '{user_name: .name, user_age: .age}'
```

输出：

```json
{
  "user_name": "Alice",
  "user_age": 30
}
```

```bash
# 合并多个 JSON 文件为数组
echo '{"id":1}' > a.json
echo '{"id":2}' > b.json
jq -s '.' a.json b.json
```

输出：

```json
[
  { "id": 1 },
  { "id": 2 }
]
```

（记得清理测试文件：`rm a.json b.json`）

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **引号嵌套错误** | `jq: error: syntax error` | 在 Shell 中，jq 过滤器建议用单引号 `'` 包裹，避免 `$` 等符号被 Shell 解析 |
| **Windows 换行符** | `parse error: Invalid numeric literal at line N` | JSON 文件可能包含 BOM 或 CRLF，用 `dos2unix` 转换或 `tr -d '\r'` 去除 |
| **大文件卡死** | 处理几百 MB 的 JSON 文件很慢 | 用 `jq --stream` 流式处理，避免一次性加载全部到内存 |
| **null 值输出** | 查询不存在的字段返回 `null` | 用 `// "default"` 设置默认值：`jq '.name // "unknown"'` |
| **管道符在 Windows Git Bash** | `|` 被识别为管道 | 在 Git Bash 中使用 `/usr/bin/jq` 而非 Windows 版本，或转义 |
| **macOS 系统 jq 版本较旧** | 某些功能（如 `--argjson`）不可用 | 用 `brew install jq` 安装最新版（当前 1.8.1），系统自带版本可能较旧 |

## 🔗 参考链接

- [jq 官方文档（jqlang）](https://jqlang.github.io/jq/)
- [jq 在线测试工具（jqplay.org）](https://jqplay.org/)
- [jq 手册（man page）](https://jqlang.github.io/jq/manual/)
- [jq 食谱 — 常见用法示例](https://jqlang.github.io/jq/recipes/)
