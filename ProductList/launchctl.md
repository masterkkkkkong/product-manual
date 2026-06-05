# launchctl — macOS 服务/守护进程管理工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple |
| 首次发布 | macOS 10.4 Tiger（2005 年） |
| 用途 | 管理和控制 macOS 上的后台服务（daemon/agent），包括启动、停止、加载、卸载、查看状态 |
| macOS 自带 | ✅ 是（系统核心组件） |

## 🎯 用来做什么？

- **查看后台服务状态**：列出所有正在运行或已注册的 launchd 服务，查看 PID 和退出状态
- **加载/卸载服务配置**：安装或移除 `.plist` 配置文件，控制服务是否随系统/用户登录启动
- **手动启停服务**：临时启动或停止某个后台服务（用于调试或运维）
- **一次性提交后台任务**：用 `submit` 子命令快速运行一个后台长期进程，无需写 `.plist` 文件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `launchctl list` | 列出当前用户域下所有已注册的服务，显示 PID、状态码、标签名 |
| `launchctl list <label>` | 查看指定服务的详细信息 |
| `launchctl load <path.plist>` | 加载一个 `.plist` 配置文件，注册服务 |
| `launchctl unload <path.plist>` | 卸载一个 `.plist` 配置文件，停止服务 |
| `launchctl start <label>` | 手动启动一个已注册的服务 |
| `launchctl stop <label>` | 手动停止一个正在运行的服务 |
| `launchctl submit -l <label> -- <command>` | 快速提交一个后台进程（无需 plist） |
| `launchctl print gui/501` | 打印当前用户域的完整服务状态（gui/<uid> 指定用户） |
| `launchctl kill SIGTERM <label>` | 向指定服务发送信号 |

## 💡 日常使用示例

### 示例 1：查看当前已注册的服务

```bash
$ launchctl list | head -10
PID	Status	Label
-	0	com.apple.SafariHistoryServiceAgent
62392	-9	com.apple.progressd
-	0	com.apple.enhancedloggingd
93294	-9	com.apple.cloudphotod
-	-9	com.apple.MENotificationService
3262	0	com.apple.Finder
3772	0	com.apple.homed
71807	0	com.apple.dataaccess.dataaccessd
```

- `PID`：进程 ID（`-` 表示未运行）
- `Status`：退出状态码（`0` 正常，`-9` 被 SIGKILL 终止）
- `Label`：服务的唯一标识名

### 示例 2：加载一个自定义的 plist 服务

假设你写了一个 `~/Library/LaunchAgents/com.example.myapp.plist` 配置文件，想让它生效：

```bash
# 加载服务配置（-w 表示同时启用，即使之前被禁用）
launchctl load -w ~/Library/LaunchAgents/com.example.myapp.plist

# 手动启动服务
launchctl start com.example.myapp

# 确认已运行
launchctl list com.example.myapp

# 如果不再需要，卸载
launchctl unload -w ~/Library/LaunchAgents/com.example.myapp.plist
```

### 示例 3：快速提交一个后台日志监控进程

不想写 plist？用 `submit` 一行搞定：

```bash
# 提交一个后台进程，每 60 秒写一条时间到文件
launchctl submit -l com.example.heartbeat \
  -o /tmp/heartbeat.log \
  -e /tmp/heartbeat.err \
  -- /bin/bash -c "while true; do date >> /tmp/heartbeat.log; sleep 60; done"

# 查看运行状态
launchctl list com.example.heartbeat

# 停止并移除
launchctl stop com.example.heartbeat
launchctl remove com.example.heartbeat
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `launchctl load` 后服务没有运行 | plist 中 `KeepAlive` 或 `RunAtLoad` 未设置；或服务没有满足启动条件 | 检查 plist 配置；用 `launchctl start <label>` 手动启动测试 |
| `Permission denied` 加载系统级 plist | `/Library/LaunchDaemons/` 下的 plist 需要 root 权限 | 加 `sudo`：`sudo launchctl load ...` |
| 修改了 plist 但服务没变 | launchd 不会自动检测 plist 变化 | 先 `unload` 再 `load`（或 `-w` 选项重载） |
| 找不到 `~/Library/LaunchAgents/` | 目录不存在 | `mkdir -p ~/Library/LaunchAgents/` |
| 用 `-w` 后想重新启用被禁用的服务 | 禁用状态的标志存在 `/var/db/com.apple.xpc.launchd/` 下 | 重新 `load -w` 即可覆盖禁用标记 |

## 🔗 参考链接

- [Apple Developer: launchd 概览](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html)
- [launchctl 手册](https://ss64.com/osx/launchctl.html)
- [Launchd 配置文件（plist）格式说明](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLaunchdJobs.html)