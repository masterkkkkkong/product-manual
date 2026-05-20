# Screen — 终端会话管理工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | GNU 项目 |
| 首次发布 | 1987 年 |
| 用途 | 在单个终端窗口中管理多个会话，断开后任务继续运行 |

## 🎯 用来做什么？

- **远程任务不断连** — SSH 连接断开会话仍在后台运行，重新连上可以恢复
- **多任务并行** — 在一个终端窗口中运行多个独立的 shell 会话
- **共享会话** — 多人同时连接到同一个终端会话，协作调试
- **日志记录** — 记录终端的全部输出到文件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `screen -S 会话名` | 创建并进入一个命名的会话 |
| `screen -ls` | 列出所有正在运行的会话 |
| `screen -r 会话名` | 重新附着到指定会话 |
| `screen -d -r 会话名` | 从其他地方断开并重新附着（强制） |
| `screen -X -S 会话名 quit` | 关闭指定会话 |
| `screen -S 会话名 -X stuff "命令"` | 向会话发送命令（脚本化） |
| `screen -dmS 会话名 命令` | 在后台创建一个会话并运行命令 |
| `screen -L` | 启动会话并自动记录日志到 screenlog.0 |

### 常用快捷键（进入会话后使用）

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+A` `C` | 创建新窗口 |
| `Ctrl+A` `0-9` | 切换到编号窗口 |
| `Ctrl+A` `N` / `P` | 切换到下一个/上一个窗口 |
| `Ctrl+A` `"` | 列出所有窗口 |
| `Ctrl+A` `K` | 杀掉当前窗口 |
| `Ctrl+A` `D` | 从当前会话分离（detach），任务继续运行 |
| `Ctrl+A` `[` | 进入滚动模式（用方向键翻页，按 Esc 退出） |
| `Ctrl+A` `?` | 查看帮助 |

## 💡 日常使用示例

### 示例 1：长时间运行的任务不断连

有时需要运行一个耗时几个小时的脚本，又担心 SSH 断开会中断：

```bash
# 1. 创建一个命名的会话
screen -S long_task

# 2. 在会话中运行命令（假设是个训练脚本）
python train.py --epochs 100

# 3. 按 Ctrl+A D 分离（detach）
# 终端显示：[detached from 12345.long_task]

# 4. 关掉终端都没关系，下次 SSH 登录后
screen -r long_task
# 就能看到任务还在跑，输出继续滚动
```

### 示例 2：在后台运行服务

在服务器上保持一个 Web 服务始终运行：

```bash
# 在后台启动一个服务会话
screen -dmS web_server

# 向会话中发送启动命令
screen -S web_server -X stuff "cd /home/user/myapp && python app.py\n"

# 查看会话是否在运行
screen -ls
# There is a screen on:
#     12345.web_server	(Detached)

# 重新附着到会话查看输出
screen -r web_server
```

### 示例 3：并行执行多个任务

同时运行多个监控任务：

```bash
# 创建三个独立的会话
screen -S monitor_cpu
# 运行 htop（等效）... 按 Ctrl+A C 创建新窗口...

# 更好的方式是分开会话
screen -S sys_log
# tail -f /var/log/system.log
# 按 Ctrl+A D 分离

screen -S web_log
# tail -f /var/log/apache/access.log
# 按 Ctrl+A D 分离

# 在任何时候切过去看
screen -r sys_log
screen -r web_log
```

## 🚨 常见坑点

| 错误现象 | 原因 | 解决方法 |
|----------|------|----------|
| `Cannot open your terminal '/dev/ttysxxx'` | 终端权限问题 | 用 `script /dev/null -c screen` 绕开 |
| `There is no screen to be resumed` | 没有分离的会话，或会话已被清除 | 先 `screen -ls` 确认有哪些会话 |
| `Cannot make directory '/var/run/screen'` | macOS 上 socket 目录权限 | `screen -ls` 显示 socket 路径，或指定 `-S` 避免 |
| 重新附着后看不到输出 | 新窗口内容为空 | 用 `Ctrl+A [ ` 进入滚动模式查看历史 |
| macOS 自带 Screen 版本较旧 (4.00) | 功能有限且不支持 HiDPI | 用 `brew install screen` 安装新版 |
| 意外输入 `Ctrl+A` 导致的快捷键触发 | 按错键 | 再按一次 `Ctrl+A` 发送字面 `Ctrl+A`，或用 `\` 转义 |

## 🔗 参考链接

- [GNU Screen 官方手册](https://www.gnu.org/software/screen/manual/screen.html)
- [Screen 速查表](https://gist.github.com/jaseg/6644359)
- [tmux vs screen 对比](https://hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)
