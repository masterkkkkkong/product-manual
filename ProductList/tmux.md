# tmux — 终端复用器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Nicholas Marriott 等 |
| 首次发布 | 2007 年 |
| 用途 | 在单个终端窗口中管理多个会话，支持分屏、后台运行、会话持久化 |

## 🎯 用来做什么？

- **会话持久化**：SSH 断开后任务不中断，重连后恢复现场
- **多任务分屏**：一个窗口同时编辑代码、运行服务器、查看日志
- **后台守护**：在远程服务器上启动长期运行的任务，断开后继续执行
- **协作共享**：多人通过同一个 tmux 会话实时查看终端

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `tmux new -s <name>` | 创建一个名为 `<name>` 的新会话 |
| `tmux ls` | 列出所有运行中的会话 |
| `tmux attach -t <name>` | 重新连接到指定会话 |
| `tmux kill-session -t <name>` | 关闭指定会话 |
| `<prefix>` + `c` | 在当前窗口新建标签页（默认 prefix 是 Ctrl+b） |
| `<prefix>` + `,` | 重命名当前标签页 |
| `<prefix>` + `%` | 水平（左右）分屏 |
| `<prefix>` + `"` | 垂直（上下）分屏 |
| `<prefix>` + `方向键` | 切换到相邻的窗格 |
| `<prefix>` + `d` | 脱离当前会话（detach，进程继续后台运行） |

## 💡 日常使用示例

### 示例 1：远程服务器上安全跑任务

```bash
# 登录服务器后创建会话
ssh user@myserver
tmux new -s deploy

# 在会话里跑长时间任务（比如部署脚本）
./deploy.sh

# 按 Ctrl+b 然后按 d 脱离会话
# SSH 断开也无所谓

# 重新连上服务器后恢复会话
tmux attach -t deploy
# 看到 deploy.sh 还在跑，输出都在
```

### 示例 2：开发时分屏，边写代码边测试

```bash
# 创建会话
tmux new -s dev

# 按 Ctrl+b 然后按 " 垂直分屏
# 上半部分用 vim 编辑代码
vim app.py

# Ctrl+b 方向键↓ 切换到下半部分
# 下半部分运行测试
python -m pytest test_app.py -v
```

这样一来改完代码不用切窗口，直接按方向键下去重跑测试即可。

### 示例 3：用多个标签页管理不同任务

```bash
# 创建新会话
tmux new -s work

# Ctrl+b, c   — 新建标签页（像浏览器标签一样）
# Ctrl+b, 0   — 跳到第 0 个标签页
# Ctrl+b, 1   — 跳到第 1 个标签页
# Ctrl+b, ,   — 重命名当前标签页（比如起名 "server"）

# 常用场景：
# 标签页 0: 跑开发服务器  python manage.py runserver
# 标签页 1: 编辑代码      vim views.py
# 标签页 2: 查看日志      tail -f log/development.log
# 标签页 3: 偶尔运行命令  curl http://localhost:8000/api
```

## 🚨 常见坑点

| 坑点 | 说明 | 解决方法 |
|------|------|---------|
| 脱离后找不到会话 | 默认 tmux 会话可能因为服务器重启丢失 | 用 `tmux ls` 查看所有会话 |
| 鼠标滚动失效 | tmux 默认不用鼠标滚动 | 按 `Ctrl+b [` 进入复制模式，然后用方向键或 PgUp/PgDn 滚动 |
| 复制粘贴困难 | tmux 有自己的剪贴板系统 | 按住 `Shift` 再选择文本可以绕过 tmux 复制模式，直接使用系统剪贴板 |
| prefix 键冲突 | 某些工具（如 VSCode 终端）也用了 `Ctrl+b` | 修改 `~/.tmux.conf`：`set -g prefix C-a` 改为更顺手的键 |
| 窗口太小错误 | SSH 窗口太小无法创建分屏 | 放大终端窗口，或用 `tmux new -s <name> -x 200 -y 60` 指定尺寸 |
| macOS 上需安装 | tmux 不内置在 macOS 中 | `brew install tmux` |

## 🔗 参考链接

- [tmux GitHub 仓库](https://github.com/tmux/tmux)
- [tmux 官方手册](https://man.openbsd.org/tmux)
- [tmux 快速入门指南](https://github.com/tmux/tmux/wiki)
- [tmux Cheat Sheet](https://tmuxcheatsheet.com/)
