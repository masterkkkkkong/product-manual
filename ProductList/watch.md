# watch — 定时执行命令

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | procps-ng 项目 |
| 首次发布 | 1990s |
| 用途 | 每隔指定秒数自动执行一次命令并全屏展示结果——实时监控系统状态、进程、文件变化的神器 |

> ⚠️ **macOS 差异**：`watch` 不是 macOS 内置命令，需要 `brew install watch` 安装。安装后即可使用。Linux 通常预装。

## 🎯 用来做什么？

- **实时监控磁盘空间** — `watch df -h` 看磁盘变化，自动刷新
- **监控进程或端口** — `watch 'ps aux | grep python'` 看程序是否还在跑
- **查看日志增量** — `watch -d tail -5 app.log` 高亮新出现的内容
- **等待某个条件满足** — 跑测试时 `watch -n 1 'curl -s localhost:8080/health'` 直到返回值正常

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `watch cmd` | 每 2 秒执行一次 cmd（默认间隔） |
| `watch -n 5 cmd` | 每 5 秒执行一次 |
| `watch -d cmd` | 高亮两次运行之间的**差异**（最常用） |
| `watch -d=cumulative cmd` | 累积高亮——变更的部分持续保留高亮 |
| `watch -t cmd` | 隐藏标题行（不显示时间/间隔信息） |
| `watch -g cmd && action` | 监控到输出变化即退出，适合结合后续命令 |
| `watch -e cmd` | 命令出错时立即退出（返回非零退出码） |

## 💡 日常使用示例

### 示例 1：监控磁盘使用率

```bash
# 每 3 秒刷新磁盘空间，并高亮变化
watch -n 3 -d df -h

# 实时监控当前目录大小变化
watch -d du -sh .
# 如果正在下载或写入文件，数值每次刷新都可能变化，高亮显示

# 监控某个特定挂载点
watch -d 'df -h / | tail -1'
```

### 示例 2：监控某个进程

```bash
# 先启动一个模拟进程
sleep 300 &

# 每隔 2 秒检查这个进程是否还在（假设 PID 是 1234）
watch "ps -p 1234 -o pid,state,etime,args --no-headers 2>/dev/null || echo 'Process gone'"

# 用 pgrep 监控 Python 进程数量
watch 'pgrep -c python'
# 输出类似：
# Every 2.0s: pgrep -c python
# 3
```

### 示例 3：等待服务启动

```bash
# 在一个终端启动一个简单的 HTTP 服务
python3 -m http.server 8080 --bind 127.0.0.1 &

# 在另一个终端用 watch 等待它就绪
watch -n 1 -g 'curl -s -o /dev/null -w "%{http_code}" http://localhost:8080' && echo "Server is ready!"

# -g 参数会在输出从 "000"（连接失败）变为 "200"（成功）时自动退出
# 然后执行 echo 提示服务已就绪
```

💡 这个模式在 CI/CD 部署和容器运维中非常常用——`watch -g 'kubectl get pods' && echo "Deploy ready"`。

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|---------|
| macOS 没有 `watch` | `watch: command not found` | `brew install watch` 安装，或直接用 `while true; do clear; cmd; sleep 2; done` 替代 |
| 管道和多命令需要引号 | `watch ps aux \| grep python` 只捕捉到了 watch 本身 | 用引号包裹：`watch 'ps aux | grep python'` |
| 特殊字符需要转义 | 命令中带 `$`、`\` 等被 shell 提前解析 | 用单引号 `'...'` 而非双引号 `"..."` |
| `-d` 高亮不管用 | 终端背景色为白色时高亮看不到 | 换个配色方案，或检查 `$TERM` 设置 |
| 命令执行时间超过刷新间隔 | 前一次还没跑完，watch 又开始新的一轮 | watch 没有排队机制，复杂命令适当调大 `-n` 间隔 |
| 退出 watch 的方式 | 以为 Ctrl+D 能退出 | 按 `Ctrl+C` 退出 watch |

## 🔗 参考链接

- [watch man page (Linux)](https://man7.org/linux/man-pages/man1/watch.1.html)
- [Homebrew watch formula](https://formulae.brew.sh/formula/watch)
- [procps-ng 项目](https://gitlab.com/procps-ng/procps)
