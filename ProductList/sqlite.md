# SQLite — 轻量级嵌入式数据库

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | D. Richard Hipp |
| 首次发布 | 2000 年 |
| 用途 | 零配置、无服务器的嵌入式关系数据库，数据存为单一文件 |

## 🎯 用来做什么？

- **本地数据存储**：桌面软件、移动 APP 的数据持久化，无需安装数据库服务
- **开发与测试**：在开发/测试环境中替代 MySQL/PostgreSQL，轻量无负担
- **数据分析**：从 CSV/JSON 导入数据后用 SQL 查询分析，比 Excel 更强大
- **嵌入式场景**：IoT 设备、浏览器（Chrome/Firefox 用 SQLite 存书签和历史）

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `sqlite3 <文件>` | 打开（或创建）一个数据库文件 |
| `.tables` | 列出所有表（注意前面有 `.` 是元命令） |
| `.schema <表名>` | 查看表的创建语句 |
| `.headers on` | 查询结果显示列名 |
| `.mode column` | 列对齐模式，更易读 |
| `.quit` | 退出 sqlite3 交互模式 |
| `.import <文件.csv> <表名>` | 从 CSV 文件导入数据 |
| `.output <文件>` | 将查询结果输出到文件 |

## 💡 日常使用示例

### 示例 1：创建数据库、建表、插入和查询

```bash
# 创建数据库（文件不存在则自动创建）
sqlite3 myapp.db

# 进入交互模式后执行
sqlite> CREATE TABLE users (
   ...>   id INTEGER PRIMARY KEY AUTOINCREMENT,
   ...>   name TEXT NOT NULL,
   ...>   email TEXT UNIQUE,
   ...>   age INTEGER,
   ...>   created_at TEXT DEFAULT CURRENT_TIMESTAMP
   ...> );

sqlite> INSERT INTO users (name, email, age)
   ...> VALUES ('Alice', 'alice@example.com', 30);

sqlite> INSERT INTO users (name, email, age)
   ...> VALUES ('Bob', 'bob@example.com', 25);

sqlite> .headers on
sqlite> .mode column
sqlite> SELECT * FROM users;
id  name   email              age  created_at
--  -----  -----------------  ---  -------------------
1   Alice  alice@example.com  30   2026-05-16 01:00:00
2   Bob    bob@example.com    25   2026-05-16 01:00:00

sqlite> SELECT name, age FROM users WHERE age >= 28;
name   age
-----  ---
Alice  30

sqlite> .quit
```

### 示例 2：不进入交互模式，直接执行 SQL

```bash
# 单条 SQL
sqlite3 myapp.db "SELECT name, age FROM users WHERE age < 30;"
Bob|25

# 美化输出
sqlite3 -header -column myapp.db "SELECT name, age FROM users WHERE age < 30;"
name  age
----  ---
Bob   25

# 多条 SQL
sqlite3 myapp.db "
  INSERT INTO users (name, email, age) VALUES ('Charlie', 'charlie@example.com', 35);
  SELECT count(*) AS total_users FROM users;
"
total_users
-----------
3
```

### 示例 3：从 CSV 导入数据并分析

```bash
# 假设有一个 CSV 文件 sales.csv
cat sales.csv
date,product,amount
2026-01-01,A,100
2026-01-01,B,150
2026-01-02,A,200
2026-01-02,B,120

# 创建表并导入
sqlite3 sales.db ".mode csv" ".import sales.csv sales"

# 分析数据
sqlite3 -header -column sales.db "
  SELECT product, SUM(amount) as total, AVG(amount) as avg, COUNT(*) as orders
  FROM sales
  GROUP BY product;
"
product  total  avg     orders
-------  -----  ------  ------
A        300    150.0   2
B        270    135.0   2
```

## 🚨 常见坑点

| 坑点 | 说明 | 解决方法 |
|------|------|---------|
| `.import` 出错 | CSV 文件路径不对或表结构不匹配 | 先用 `.headers on` 确认表结构；CSV 第一行最好有列名 |
| 并发写问题 | SQLite 不擅长高并发写入 | SQLite 适合单进程/低并发场景，高并发请用 PostgreSQL |
| 没有用户管理 | 无用户名密码概念 | 靠文件系统权限保护 `.db` 文件 |
| ALTER TABLE 有限 | 不支持 `DROP COLUMN` 和 `ALTER COLUMN` | 需要先创建新表，复制数据，再删旧表 |
| 默认不显示列名 | `.mode` 默认是 list 模式 | 查询前执行 `.headers on` 和 `.mode column` |
| macOS 自带 sqlite3 | 版本可能较旧 | `brew install sqlite3` 升级到最新版 |

## 🔗 参考链接

- [SQLite 官网](https://www.sqlite.org/)
- [SQLite 命令行工具文档](https://www.sqlite.org/cli.html)
- [SQLite 语法参考](https://www.sqlite.org/lang.html)
- [DB Browser for SQLite（图形化工具）](https://sqlitebrowser.org/)
