# htop — 交互式系统进程监控器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Hisham Muhammad / htop dev team |
| 首次发布 | 2004 年 |
| 用途 | 交互式查看系统进程、CPU/内存使用情况，比 top 更直观、可操作 |

> macOS 默认不安装 htop，需要 `brew install htop`。htop 3.5.1 是目前最新版。显示完整进程需要 `sudo htop`。

## 🎯 用来做什么？

- **实时监控系统资源** — 一眼看到 CPU、内存、Swap 使用率
- **找出资源消耗大户** — 按 CPU 或内存排序，发现异常进程
- **管理进程** — 直接在界面上结束进程、调整优先级（nice 值）
- **按用户过滤** — 只看自己或其他用户的进程
- **树状查看** — 看清进程间的父子关系（`-t` 树视图）

## 🔧 常用命令

htop 是交互式界面，以下是一些启动参数和快捷键：

| 命令 / 快捷键 | 说明 |
|------|------|
| `htop` | 启动 htop（普通模式，仅显示用户进程） |
| `sudo htop` | 以 root 权限启动，显示所有进程 |
| `htop -u $USER` | 只显示当前用户的进程 |
| `htop -s PERCENT_CPU` | 按 CPU 使用率排序 |
| `htop -t` | 以树状视图启动 |
| `htop -d 50` | 设置刷新间隔为 50 十分之一秒（5 秒） |
| `htop --readonly` | 只读模式（禁止 kill/renice，安全） |
| `F6` | 选择排序列（CPU、MEM、PID 等） |
| `F9` | 结束选中的进程 |
| `F10` / `q` | 退出 htop |
| `/` | 搜索进程名 |
| `t` | 切换树状 / 普通视图 |
| `u` | 按用户过滤进程 |
| `H` / `K` | 切换显示用户线程 / 内核线程 |
| `Space` | 标记/取消标记进程 |

## 💡 日常使用示例

### 示例 1：基本使用 — 找出 CPU 占用最高的进程

```bash
# 启动 htop（建议用 sudo 看全量进程）
$ sudo htop

# 界面说明：
# 顶部：CPU（每个核心一条）、内存、Swap 彩色进度条
# 中部：进程列表（PID、USER、PRI、NI、VIRT、RES、SHR、S、CPU%、MEM%、TIME+、COMMAND）
# 底部：功能键提示 F1-F10

# 按 F6 → 选择 PERCENT_CPU → Enter 即按 CPU 降序排列
# 按 F9 → 选择 SIGKILL(9) → Enter 即可杀死进程
# 按 q 退出
```

### 示例 2：只看某个用户的进程

```bash
# 启动后按 u，输入用户名，回车
# 或直接命令行指定
$ htop -u mk
```

### 示例 3：树状视图查看父子进程关系

```bash
# 启动后按 t 切换
# 或命令行模式
$ htop -t

# 树状视图会把进程按父子关系缩进展示，例如：
# ├─ tmux: server
# │  ├─ -zsh
# │  │  └─ htop
# │  └─ -zsh
# │     └─ python train.py
# 便于看出谁启动了谁
```

### 示例 4：只读模式（安全监控）

```bash
# 只看不动，适合共享屏幕演示
$ htop --readonly
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| 只显示一两个进程 | 普通用户权限不够 | `sudo htop` 显示所有进程 |
| 颜色和柱状图显示乱码 | 终端不支持 Unicode | `htop -U` 使用纯 ASCII |
| 找不到进程（比如 docker） | 过滤或搜索条件有误 | 按 `/` 搜索，或检查是否用了 `u` 过滤 |
| 退出 htop 后进程被杀了 | 不会 — htop 读/shell 只做显示 | htop 是只读监控器，kill 需要手动操作 |
| macOS 上显示不完整 | macOS 的进程模型有差异 | `sudo htop` 解决大部分问题 |
| 刷新速度太快/太慢 | 默认每秒刷新 | `htop -d 100` 设为 10 秒刷新一次 |

## 🔗 参考链接

- [htop 官方 GitHub](https://github.com/htop-dev/htop)
- [Homebrew htop 包](https://formulae.brew.sh/formula/htop)