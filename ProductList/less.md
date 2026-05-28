# less — 文件内容分页查看器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Mark Nudelman |
| 首次发布 | 1984 年 |
| 用途 | 交互式查看大文件内容，支持上下翻页、搜索、行号跳转，不会一次性加载全部内容到内存 |

## 🎯 用来做什么？

- **查看大文件** — 不用编辑器打开几百 MB 的日志文件，less 瞬间启动
- **搜索和导航** — 在文件中向前/向后搜索关键字，快速跳转到特定行号
- **配合管道实时查看** — `grep ... | less` 让任何命令的输出都可翻页浏览
- **替代 cat 查看长内容** — cat 会把全部内容刷到终端，less 只展示一屏

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `less file.txt` | 打开文件浏览 |
| `command \| less` | 查看管道输出（例如 `ps aux \| less`） |
| `less -N file.txt` | 显示行号 |
| `less +100 file.txt` | 打开文件并直接跳到第 100 行 |
| `less -S file.txt` | 不自动换行，长行用水平滚动查看 |
| `less -F file.txt` | 内容很短时直接退出（行为类似 cat） |
| `less +/keyword file.txt` | 打开文件并高亮搜索 "keyword" |
| `less -R file.txt` | 保留 ANSI 颜色转义码（查看彩色输出） |

**常用快捷键（打开 less 后）**：

| 按键 | 作用 |
|------|------|
| `j` / `k` | 向下/向上滚动一行 |
| `f` / `b` | 向下/向上翻一页 |
| `g` / `G` | 跳到文件开头 / 末尾 |
| `/keyword` | 向前搜索 "keyword" |
| `?keyword` | 向后搜索 "keyword" |
| `n` / `N` | 跳转到下一个 / 上一个匹配结果 |
| `q` | 退出 less |
| `F` | 滚动到末尾，实时追踪新增内容（类似 `tail -f`） |
| `h` | 查看帮助页面 |
| `-N` | 切换行号显示（输入后回车生效） |

## 💡 日常使用示例

### 示例 1：查看大日志文件并搜索错误

```bash
# 打开一个大的系统日志（假设路径）
less /var/log/system.log

# 进入 less 后输入（不需要回车确认）：
# /error     → 搜索 "error"，按 n 查看下一个匹配
# ?Warning   → 向后搜索 "Warning"
# G          → 跳到文件末尾看最新的日志
# 50g        → 跳到第 50 行
```

### 示例 2：配合管道浏览命令输出

```bash
# 查看所有进程，用 less 分页浏览
ps aux | less

# 在 less 中可以上下翻页、搜索 /python、按 q 退出

# 查看磁盘使用情况，带行号显示
df -h | less -N

# 查看当前目录文件，按修改时间排序后分页
ls -lt | less
```

### 示例 3：实时监控日志（像 tail -f 一样）

```bash
# 用 less 打开日志后按大写 F，效果等同于 tail -f
less /var/log/system.log
# 进入后按 F（大写），less 会滚动到文件末尾并持续追踪新内容
# 按 Ctrl+C 停止追踪
```

**💡 如果要用 less 追日志还带搜索，可以这样：**

```bash
# 打开日志后，输入 /error 搜索，然后按 F
# 新到来的行里如果包含 error 也会被高亮
less /var/log/system.log
# 按 /error → 回车 → 按 F
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| 打开二进制文件后界面乱码 | less 试图显示二进制数据 | 使用 `less -f` 前加上 `strings`：`strings binary.bin \| less` |
| 退出后终端内容被清空 | less 默认不清屏 | 用 `less -X` 退出时保留屏幕内容 |
| 按 q 退不出 less | 可能处于搜索或命令输入模式 | 按 Esc 取消当前输入，再按 q |
| 长行被截断不好看 | 默认自动换行 | 在 less 中输入 `-S` 回车（大写 S），改为水平滚动 |
| 中文内容显示为乱码 | 编码问题 | macOS 上一般正常，Linux 可设置 `export LESSCHARSET=utf-8` |
| `ps aux \| less` 输出了全部内容没分页 | 管道断开或 stderr 未重定向 | 确认没有用 `2>&1` 或检查命令本身是否直接输出到了终端 |
| macOS 用 `less -F` 对大文件没反应 | `-F` 只对短于一屏的内容生效 | 大文件始终正常分页，`-F` 不影响大文件 |

## 🔗 参考链接

- [less 官方手册（GNU）](https://www.gnu.org/software/less/)
- [less 快捷键速查表](https://gist.github.com/henrik/1969983)
- macOS 自带 less，无需安装