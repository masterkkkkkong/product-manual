# strings — 从二进制文件中提取可打印字符串

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs / GNU Binutils |
| 首次发布 | Unix 早期版本 |
| 用途 | 从二进制文件、对象文件、库文件中提取人类可读的字符串 |

## 🎯 用来做什么？

- **分析二进制文件** — 查看可执行文件里包含了什么文本内容（错误消息、路径、版本号）
- **逆向工程辅助** — 快速了解一个程序的功能逻辑（通过硬编码的字符串推测）
- **日志/核心转储分析** — 从崩溃转储文件中提取有用的文本信息
- **查找版权和许可证信息** — 查看库文件中包含的许可证声明

## 🔧 常用命令

```bash
# 提取默认长度的字符串（4 个连续可打印字符以上）
strings 文件名

# 设置最小字符串长度为 6
strings -n 6 文件名

# 从目标文件中提取字符串
strings 目标文件.o

# 从动态库中提取字符串
strings /usr/lib/libz.dylib

# 递归扫描目录下所有文件的字符串（配合 find）
find /path -type f -exec strings {} \;

# 从指定架构的二进制中提取（macOS Universal Binary）
strings -arch x86_64 文件名
strings -arch arm64 文件名

# 在提取的字符串中搜索关键词
strings 文件名 | grep -i error
```

## 💡 日常使用示例

### 1. 查看 curl 可执行文件中包含的字符串

```bash
$ strings $(which curl) | head -20
# 输出示例（实际内容因版本而异）：
%require
usage: curl [options...] <url>
option --%s: %s
Out of memory
option --%s is ambiguous
URL rejected: Malformed input to a URL function
Failed to open/read local data from file/application
Failed writing body
Failed to connect to %s port %u after %ld ms: %s
```

### 2. 搜索动态库中的版本信息

```bash
$ strings /usr/lib/libz.dylib | grep -i version
# 输出示例：
1.2.11
zlib version 1.2.11
deflate 1.2.11
```

### 3. 结合 grep 快速定位错误信息

```bash
# 从系统日志分析工具中查找错误关键词
$ strings /usr/bin/log | grep -i "error\|fail\|warn" | head -5
# 输出示例：
Error: %s
failed to parse predicate
warning: predicate
unexpected error from tracev3
```

## 🚨 常见坑点

| 问题 | 可能原因 | 解决方法 |
|------|---------|----------|
| 输出中包含大量乱码 | strings 默认最小长度 4，短的文本序列被排除 | 用 `-n 3` 降低最小长度，配合 `grep` 过滤 |
| macOS 上 strings 来自 Xcode，非 GNU 版本 | macOS 自带的 strings 来自 LLVM | 功能基本一致，但 `--help` 语法不同，用 `strings -` 看用法 |
| Universal Binary 重复输出 | macOS 的 Mach-O 文件可能包含多架构 | 用 `-arch arm64` 指定单一架构 |
| 找不到 strings 命令 | 未安装 Xcode Command Line Tools | 运行 `xcode-select --install` |
| 扫描大文件时卡住 | 没限制输出 | 加 `-n 8` 提高最小长度，或 pipe 到 `less` |

## 🔗 参考链接

- 终端运行 `man strings`
- [GNU Binutils strings 文档](https://www.gnu.org/software/binutils/)
- LLVM 版本: [llvm-strings](https://llvm.org/docs/CommandGuide/llvm-strings.html)
