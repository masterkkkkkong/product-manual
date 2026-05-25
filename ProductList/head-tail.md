# head & tail — 文件首尾查看

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Unix 原生命令（AT&T Bell Labs） |
| 首次发布 | 1970s |
| 用途 | 快速查看文件的开头（head）或结尾（tail）部分，是数据分析日志排查最常用的命令 |

## 🎯 用来做什么？

- **快速预览大文件** — 不用打开编辑器，`head` 秒出前 10 行，适合看 CSV 的列名和数据样例
- **实时跟踪日志** — `tail -f` 持续输出文件新增内容，调试服务或监控日志的首选
- **查看文件末尾** — 看日志最新报错、检查命令执行结果
- **搭配管道过滤数据** — `head` 和 `tail` 是数据分析管道中的"切刀"，配合 `sort`、`grep` 等使用

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `head file.txt` | 显示文件前 10 行（默认） |
| `head -n 20 file.txt` | 显示文件前 20 行 |
| `head -c 100 file.txt` | 显示文件前 100 个字节 |
| `tail file.txt` | 显示文件最后 10 行（默认） |
| `tail -n 50 file.txt` | 显示文件最后 50 行 |
| `tail -f app.log` | 实时追踪日志，新内容出现即自动刷新（最常用） |
| `tail -f -n 0 app.log` | 从头开始追踪，只显示新追加的内容 |
| `tail -f \| grep ERROR` | 实时过滤日志，只看 Error 行 |
| `command \| head` | 只看管道前几条结果（如 `ls -t \| head`） |

## 💡 日常使用示例

### 示例 1：快速预览 CSV 数据

```bash
# 创建示例 CSV
cat > /tmp/sales.csv << 'EOF'
date,product,amount,city
2026-01-01,MacBook Pro,25999,Beijing
2026-01-01,iPhone 16,9999,Shanghai
2026-01-02,MacBook Pro,25999,Guangzhou
2026-01-02,iPad Air,5999,Shenzhen
2026-01-03,iPhone 16,9999,Beijing
2026-01-03,MacBook Air,12999,Shanghai
2026-01-04,iPad Air,5999,Guangzhou
2026-01-04,MacBook Pro,25999,Shenzhen
2026-01-05,iPhone 16,9999,Beijing
2026-01-05,MacBook Air,12999,Shanghai
2026-01-06,iPad Air,5999,Guangzhou
2026-01-06,MacBook Pro,25999,Shenzhen
EOF

# 查看前 3 行（确认列名和数据格式）
head -n 3 /tmp/sales.csv
# 输出：
# date,product,amount,city
# 2026-01-01,MacBook Pro,25999,Beijing
# 2026-01-01,iPhone 16,9999,Shanghai

# 查看最后 2 行（最新数据）
tail -n 2 /tmp/sales.csv
# 输出：
# 2026-01-06,iPad Air,5999,Guangzhou
# 2026-01-06,MacBook Pro,25999,Shenzhen
```

### 示例 2：实时监控日志（tail -f）

```bash
# 打开终端 A：模拟日志生成
for i in $(seq 1 10); do
  echo "$(date '+%H:%M:%S') Request #$i processed"
  sleep 1
done > /tmp/app.log &

# 打开终端 B：实时跟踪日志
tail -f /tmp/app.log
# 每 1 秒自动刷新，看到类似：
# 10:01:23 Request #1 processed
# 10:01:24 Request #2 processed
# ...

# 实时过滤：只看 ERROR（按 Ctrl+C 退出）
tail -f /tmp/app.log | grep "ERROR"
```

💡 生产环境中，`tail -f` 配合 `grep`、`awk` 实时过滤是排查线上问题的标准姿势。

### 示例 3：跳过表头+取中间数据

```bash
# 跳过 CSV 表头，取第 2-6 行（即数据行前 5 条）
tail -n +2 /tmp/sales.csv | head -n 5
# 输出：
# 2026-01-01,MacBook Pro,25999,Beijing
# 2026-01-01,iPhone 16,9999,Shanghai
# 2026-01-02,MacBook Pro,25999,Guangzhou
# 2026-01-02,iPad Air,5999,Shenzhen
# 2026-01-03,iPhone 16,9999,Beijing

# 只看最大的 5 笔销售额（假设 amount 在第 3 列）
sort -t, -k3 -rn /tmp/sales.csv | head -5
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|---------|
| macOS 和 Linux 的 `head -n` 参数差异 | 某些 Linux 写法 `head -20` 在 macOS 上不生效 | macOS 必须用 `-n 20`，不能直接 `head -20` |
| `tail -n +2` 的含义易混淆 | 以为 +2 表示"最后 2 行"，实际表示"从第 2 行开始到末尾" | `head -n 5`（前5行）/ `head -n -5`（除后5行外的所有行，macOS 不支持这个语法） |
| `tail -f` 不会自动退出 | 终端一直挂在那里，Ctrl+C 没反应 | 按 `Ctrl+C` 停止；或加 `--pid=PID` 在进程结束后自动退出 |
| 大文件 `tail -f` 消耗内存 | 日志量巨大时可能占用大量 I/O | 加 `-n 0` 只从当前尾部开始跟踪，不读取历史内容 |

## 🔗 参考链接

- [macOS head man page](https://ss64.com/osx/head.html)
- [macOS tail man page](https://ss64.com/osx/tail.html)
- [GNU Coreutils head documentation](https://www.gnu.org/software/coreutils/manual/html_node/head-invocation.html)
- [GNU Coreutils tail documentation](https://www.gnu.org/software/coreutils/manual/html/node/tail-invocation.html)
