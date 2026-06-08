# date — 日期时间显示与格式化工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 创始团队） |
| 首次发布 | 1971 年（Unix V1） |
| 用途 | 显示、格式化、计算系统日期和时间，在脚本中生成时间戳和文件名 |
| macOS 自带 | ✅ 是（BSD 版，与 Linux 的 GNU date 有差异） |

## 🎯 用来做什么？

- **显示当前时间**：快速看今天是几月几号星期几
- **格式化输出**：按任意格式输出时间，用于日志、文件名、报表
- **日期计算**：计算 N 天前/后的日期、转换时间戳、算星期几
- **设置系统时间**：用 root 权限手动修改系统时间（需要管理员权限）

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `date` | 显示当前日期时间（默认格式） |
| `date +"%Y-%m-%d"` | 按指定格式输出（如 `2026-06-09`） |
| `date -u` | 显示 UTC 时间（而非本地时区） |
| `date -r 1749000000` | 将 Unix 时间戳转换为可读日期（macOS） |
| `date -v-7d` | 计算 7 天前的日期（macOS） |
| `date -v+1m +%F` | 计算 1 个月后的日期，格式 YYYY-MM-DD（macOS） |
| `date -j -f "%Y-%m-%d" "2026-06-09" "+%A"` | 将日期字符串解析为星期几（macOS） |
| `date +%s` | 输出当前 Unix 时间戳（秒数） |

### macOS vs Linux 差异

| 功能 | macOS（BSD date） | Linux（GNU date） |
|------|------------------|-------------------|
| 日期计算 | `date -v+1d` | `date -d "+1 day"` |
| 解析日期 | `date -j -f "%F" "2026-01-01"` | `date -d "2026-01-01"` |
| 从时间戳转换 | `date -r 1749000000` | `date -d @1749000000` |
| `--help` | 不支持 | 支持 |

## 💡 日常使用示例

### 示例 1：在文件名中嵌入时间戳

生成带日期的备份文件名，是脚本中最常见的用法：

```bash
# 生成格式：backup_2026-06-09.sql
TIMESTAMP=$(date +%Y-%m-%d)
echo "backup_${TIMESTAMP}.sql"
# 输出：backup_2026-06-09.sql

# 更精细的格式（含时分秒，适合每小时备份）
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
echo "log_${TIMESTAMP}.txt"
# 输出示例：log_20260609_143022.txt
```

### 示例 2：日期计算（macOS）

macOS 的 `date -v` 做日期加减非常直观：

```bash
# 7 天前
date -v-7d +%F
# 输出：2026-06-02

# 下周三
date -v+1w -v+3d +%A
# 输出：Wednesday

# 上个月第一天
date -v-1m -v1d +%F
# 输出：2026-05-01
```

`-v` 的可选单位：

| 单位 | 含义 | 示例 |
|------|------|------|
| `y` | 年 | `-v+1y` 一年后 |
| `m` | 月 | `-v-3m` 三个月前 |
| `w` | 周 | `-v+2w` 两周后 |
| `d` | 天 | `-v-7d` 七天前 |
| `H` | 小时 | `-v+1H` 一小时后 |
| `M` | 分钟 | `-v-30M` 三十分钟前 |
| `S` | 秒 | `-v+60S` 六十秒后 |

### 示例 3：转换时间戳和计算星期

```bash
# 把 Unix 时间戳转成可读格式
date -r 1749000000
# 输出示例：Tue Jun  9 14:30:00 CST 2026

# 查看某个日期是星期几
date -j -f "%Y-%m-%d" "2026-12-25" "+%A"
# 输出：Friday（2026 年圣诞节是周五）

# 当前 Unix 时间戳
date +%s
# 输出：1749378600
```

### 示例 4：日志文件自动命名脚本

```bash
#!/bin/bash
# 每天运行一次，日志按日期存档
LOG_DIR="$HOME/logs"
mkdir -p "$LOG_DIR"

# 生成今天的日志文件名
TODAY=$(date +%Y-%m-%d)
LOG_FILE="$LOG_DIR/app_${TODAY}.log"

# 追加日志
echo "[$(date +%H:%M:%S)] 任务执行完成" >> "$LOG_FILE"
echo "日志已写入: $LOG_FILE"
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **macOS 上用 `-d` 报错** | `date: illegal option -- d` | macOS 的 date 是 BSD 版，不支持 `-d`。日期计算用 `-v`，日期解析用 `-j -f` |
| **Linux 的 date 脚本移植到 macOS** | 脚本报非法选项 | 检查 `date` 用法，macOS 用 `-v` 和 `-j -f`，Linux 用 `-d`。建议在脚本开头用 `uname` 判断系统 |
| **% 符号在 Shell 中不解析** | 输出 `%Y-%m-%d` 而不是日期 | `+` 后面的格式参数要用双引号包起来：`date +"%Y-%m-%d"` |
| **时区不对** | 时间和预期差几个小时 | 用 `date -u` 看 UTC 时间，检查系统时区：`ls -l /etc/localtime` 或 macOS 的 `systemsetup -gettimezone` |
| **设置时间需要权限** | `date 010203042026` 报 `Operation not permitted` | macOS 上需要用 `sudo` 或使用 `systemsetup -setdate` |
| **`date --help` 在 macOS 上无效** | 报 `illegal option -- -` | macOS 的 date 不支持长选项，用 `man date` 查看用法 |

## 🔗 参考链接

- [macOS date man page（官方）](https://man.freebsd.org/cgi/man.cgi?query=date)
- [GNU date 手册（coreutils）](https://www.gnu.org/software/coreutils/manual/html_node/date-invocation.html)
- [date 格式说明符速查表](https://www.man7.org/linux/man-pages/man1/date.1.html)
