# df & du — 磁盘空间查看工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 基础工具） |
| 首次发布 | 1970s |
| 用途 | **df**（disk free）查看磁盘分区使用情况；**du**（disk usage）查看文件/目录占用空间大小。两者配合使用，精确诊断磁盘空间问题 |

## 🎯 用来做什么？

- **检查磁盘还剩多少空间** — 系统提示"磁盘空间不足"时，先用 df 看哪个分区满了
- **找出谁占用了大量空间** — 用 du 从根目录一层层查下去，揪出"空间杀手"
- **监控日志文件大小** — 定期检查日志目录是否异常增长
- **清理前的摸底排查** — 删文件之前先统计空间占用，做到心中有数

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `df -h` | 查看所有分区磁盘使用量（-h = human readable，显示 G/M 单位） |
| `df -h /` | 只查看根分区的使用情况 |
| `df -h /Users` | 查看用户目录所在分区的使用情况 |
| `du -sh 目录` | 统计指定目录的总大小（-s = summary，-h = human readable） |
| `du -sh *` | 统计当前目录下所有子目录和文件的大小（⚠️ 可能不包含隐藏文件） |
| `du -sh .[!.]* *` | 统计当前目录下所有内容，包含隐藏文件 |
| `du -sh 目录/* 2>/dev/null \| sort -rh` | 统计子目录大小并按从大到小排序 |
| `du -ah 目录` | 列出目录下每个文件和子目录的大小（-a = all） |
| `du -d 1 -h 目录` | 只统计一层子目录，不深入嵌套（macOS/BSD 用法） |

**💡 macOS 特别提醒**：
- macOS 的 `du` 用 `-d` 控制目录深度（如 `-d 1`），Linux 用 `--max-depth=1`
- macOS 查看磁盘时，`/`（根分区）和 `/System/Volumes/Data`（数据分区）是分开的，千万别只查根分区

## 💡 日常使用示例

### 示例 1：快速查看磁盘空间

```bash
# 所有分区一目了然
df -h
```

输出示例（macOS）：

```
Filesystem        Size    Used   Avail Capacity iused ifree %iused  Mounted on
/dev/disk3s1s1   460Gi    17Gi   314Gi     6%    426k  3.3G    0%   /
/dev/disk3s5     460Gi   112Gi   314Gi    27%    1.0M  3.3G    0%   /System/Volumes/Data
```

**怎么看**：
- `/`（根分区）：460G 总容量，用了 17G，还剩 314G，用了 6%
- `/System/Volumes/Data`（数据分区）：同样 460G 总容量，用了 112G，还剩 314G，用了 27%
- **容量是共享的**（APFS 容器），所以两个分区的"可用"是一样的

### 示例 2：找出哪些目录最占空间

```bash
# 分析用户目录下各子目录的大小，从大到小排列（macOS）
du -sh /Users/mk/*/ 2>/dev/null | sort -rh | head -5
```

输出示例：

```
 24G	/Users/mk/Library/
 20G	/Users/mk/Virtual Machines.localized/
 15G	/Users/mk/Downloads/
3.8G	/Users/mk/code_file/
319M	/Users/mk/projects/
```

**进阶用法**：查更深一层的目录

```bash
# 看看 Library 里谁最吃空间
du -d 1 -h /Users/mk/Library/ 2>/dev/null | sort -rh | head -5
```

### 示例 3：精确统计某个项目的大小

```bash
# 统计产品手册仓库占了多少空间
du -sh /Users/mk/obsidian_files/obs_repository/Product\ manual/
```

输出示例：

```
1.1M	/Users/mk/obsidian_files/obs_repository/Product manual/
```

```bash
# 看看里面每个文件的大小
du -ah /Users/mk/obsidian_files/obs_repository/Product\ manual/ProductList/ | sort -rh | head -5
```

输出示例：

```
8.0K	.../ProductList/zsh.md
8.0K	.../ProductList/vscode.md
8.0K	.../ProductList/uv.md
8.0K	.../ProductList/time.md
8.0K	.../ProductList/tar.md
```

### 示例 4：监控日志目录是否膨胀

```bash
# 一天查一次日志目录大小，看增长速度
du -sh /var/log/
```

```bash
# 列出所有日志文件的大小
du -ah /var/log/ 2>/dev/null | sort -rh
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `du -sh *` 统计少了 | 隐藏文件（以 `.` 开头的）不被 `*` 匹配 | 用 `du -sh .[!.]* *` 或 `du -sh .[!.]* 2>/dev/null; du -sh *` |
| macOS 根分区显示的"已用"很小 | APFS 把数据和系统分开了（`/System/Volumes/Data`） | 用 `df -h /System/Volumes/Data` 看真实数据占用 |
| `du -sh` 和 Finder 显示的大小不一样 | du 统计的是物理占用（含稀疏文件空洞），Finder 是逻辑大小 | 都是对的，视角不同；关心占用空间就看 du |
| `sort -rh` 不生效 | macOS 默认的 sort 支持 `-h`，但部分旧版本可能没有 | 用 `sort -rn` 代替（按数值排序） |
| `du` 很慢，跑了好几分钟 | 目录层级深、文件多（如 node_modules） | 用 `-d 1` 限制深度，或用 `ncdu`（brew install ncdu）交互式查看 |
| 明明没文件了 df 显示还没释放 | 有进程还在使用被删的文件（lsof 查谁占了） | `lsof \| grep deleted` 找到进程后重启它 |
| 权限不足，输出很多 "Permission denied" | 没有读取某些系统目录的权限 | 加到最前面用 `sudo du -sh /root/...`，或直接 `2>/dev/null` 忽略 |

## 🔗 参考链接

- [macOS df 手册（ss64）](https://ss64.com/osx/df.html)
- [macOS du 手册（ss64）](https://ss64.com/osx/du.html)
- [Linux df 手册](https://man7.org/linux/man-pages/man1/df.1.html)
- [Linux du 手册](https://man7.org/linux/man-pages/man1/du.1.html)
- 系统自带工具，无需安装