# find — 文件查找工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 原生工具） |
| 首次发布 | 1970年代（V1 Unix） |
| 用途 | 递归搜索目录树，按文件名、类型、大小、时间等条件查找文件并执行操作 |

## 🎯 用来做什么？

- **按名字找文件** — 忘记放哪了？`find . -name "*.py"` 瞬间定位
- **按条件筛选** — 找大于 100MB 的垃圾文件、7 天内改过的源代码、空目录
- **批量操作** — 找到文件后直接删、改权限、打包 (`-exec` 或配合 `xargs`)
- **管道给其他工具** — 和 `xargs`、`grep` 组合成强大的文件处理流水线

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `find . -name "模式"` | 按文件名查找（区分大小写） |
| `find . -iname "模式"` | 按文件名查找（忽略大小写） |
| `find . -type f` | 只找文件（`d`=目录, `l`=链接） |
| `find . -size +100M` | 找大于 100MB 的文件（`-100k`=小于, `100k`=精确） |
| `find . -mtime -7` | 找 7 天内修改过的文件（`+7`=超过 7 天） |
| `find . -empty` | 找空文件或空目录 |
| `find . -name "*.log" -delete` | 找到后直接删除 |
| `find . -name "*.py" -exec wc -l {} +` | 找到后执行命令（`+`=批量传参, `\;`=逐个执行） |

## 💡 日常使用示例

### 示例 1：找大文件清理磁盘空间

看看当前目录下哪些文件超过 100MB：

```bash
$ find ~/Downloads -type f -size +100M -exec ls -lh {} \;
-rw-r--r--  1 mk  staff   256M May 20 14:30 /Users/mk/Downloads/big_video.mp4
-rw-r--r--  1 mk  staff   180M May 19 09:15 /Users/mk/Downloads/dataset.zip
```

组合 `sort` 按大小排序：

```bash
$ find ~/Downloads -type f -size +50M -exec ls -lh {} \; | sort -k5 -h
-rw-r--r--  1 mk  staff    52M May 18 10:00 model.pt
-rw-r--r--  1 mk  staff   180M May 19 09:15 dataset.zip
-rw-r--r--  1 mk  staff   256M May 20 14:30 big_video.mp4
```

### 示例 2：按时间批量清理日志

删除 30 天前的 `.log` 文件：

```bash
$ find /var/log -name "*.log" -mtime +30 # 先看看有哪些
$ find /var/log -name "*.log" -mtime +30 -delete # 确认后删掉
```

> ⚠️ macOS 上没有 `/var/log` 的大量日志，可以试试 `~/Library/Logs/`

### 示例 3：统计所有 Python 文件的行数

```bash
$ find . -name "*.py" -exec wc -l {} +
    124 ./src/main.py
     56 ./src/utils.py
     89 ./tests/test_main.py
    269 total
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `find: -maxdepth: unknown primary` | macOS 的 BSD find 不支持 `-maxdepth` | 用 Homebrew 装 GNU find：`brew install findutils`，然后用 `gfind` |
| `find . -name "*.py"` 找不到文件 | `-name` 模式要用引号括起来，否则 shell 会先展开 | 始终用引号：`find . -name "*.py"` |
| `find . -delete` 误删文件 | `-delete` 是静默执行的，没有确认提示 | 先用 `-print` 预览结果，确认无误后再加 `-delete` |
| 权限不足的目录报错 | `find` 会遍历所有子目录 | 加 `2>/dev/null` 忽略错误：`find / -name "*.conf" 2>/dev/null` |
| 文件名含空格导致 `xargs` 出错 | `xargs` 默认按空格切分 | 用 `find ... -print0 \| xargs -0` 安全处理 |

## 🔗 参考链接

- [macOS find 手册 (man page)](https://man.freebsd.org/cgi/man.cgi?find)
- [GNU findutils 文档](https://www.gnu.org/software/findutils/manual/html_mono/find.html)
- macOS 自带，无需安装
