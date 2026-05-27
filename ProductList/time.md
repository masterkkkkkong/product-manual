# time — 命令执行时间测量工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Ken Thompson（Bell Labs） |
| 首次发布 | Unix Version 3（1973） |
| 用途 | 测量命令执行耗时，分 real / user / sys 三个维度，用于性能分析和脚本优化 |

## 🎯 用来做什么？

- **脚本性能分析**——找出慢在哪一步，优化最有价值的部分
- **对比不同方案的效率**——同样的功能，A 方案和 B 方案哪个更快
- **定位性能瓶颈**——user 时间高说明 CPU 密集，sys 时间高说明系统调用多
- **日常命令耗时追踪**——"这条命令到底跑了多久？"

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `time command` | 测量命令执行时间（bash/zsh 内置） |
| `/usr/bin/time -p command` | 使用系统 `/usr/bin/time`，`-p` 输出为秒级精度 |
| `/usr/bin/time -l command` | macOS 增强版，额外显示内存占用信息 |
| `time python3 script.py` | 测量 Python 脚本执行时间 |
| `time curl https://api.example.com` | 测量 HTTP 请求响应时间 |
| `time (cmd1 \| cmd2)` | 测量组合管道命令的时间 |
| `time for i in {1..100}; do cmd; done` | 测量循环执行的总时间 |

> **两种 time 的区别**
>
> - `time`（无路径）是 shell 内置关键字，格式为 `real XmY.Zs`
> - `/usr/bin/time` 是独立可执行文件，格式不同，`-p` 输出为 `real X.Y`
>
> macOS 上 `/usr/bin/time` 比许多 Linux 发行版功能更丰富（支持 `-l` 等选项）。

## 💡 日常使用示例

### 示例 1：比较两种命令的速度

```bash
# 想看看 grep 和 ripgrep 谁更快

# 先选个测试文件，比如一个大的日志文件
$ wc -l /var/log/system.log
 52341 /var/log/system.log

# 测试 grep
$ /usr/bin/time -p grep "error" /var/log/system.log > /dev/null
real 0.58
user 0.49
sys 0.08

# 测试 ripgrep（如果安装了）
$ /usr/bin/time -p rg "error" /var/log/system.log > /dev/null
real 0.12
user 0.08
sys 0.04

# 结论：rg 快了约 5 倍
```

### 示例 2：定位脚本中的慢操作

```bash
$ cat analyze.py
import time
import json

start = time.time()
# 模拟慢操作 1
data = json.loads('{"a": 1}' * 10000)
print("Step 1:", time.time() - start)

start = time.time()
# 模拟慢操作 2
result = [i**2 for i in range(1000000)]
print("Step 2:", time.time() - start)

# 用 time 整体计时
$ time python3 analyze.py
Step 1: 0.002
Step 2: 0.08

real	0m0.312s
user	0m0.235s
sys	0m0.048s
```

### 示例 3：API 响应速度测试

```bash
# 测试一个 HTTP 请求的往返时间
$ cd /tmp

$ cat test_api.sh
#!/bin/bash
url="https://httpbin.org/get"
echo "Testing: $url"

# 用 time 测量并解析结果
/usr/bin/time -p curl -s -o /dev/null "$url" 2>&1

$ chmod +x test_api.sh
$ ./test_api.sh
Testing: https://httpbin.org/get
real 1.23
user 0.08
sys 0.02

# 用 -l 看内存使用（macOS 专属）
$ /usr/bin/time -l curl -s -o /dev/null https://httpbin.org/get 2>&1
        1.25 real         0.09 user         0.02 sys
           29360128  maximum resident set size  # 约 28 MB 内存占用
```

## 🚨 常见坑点

| 问题 | 原因 | 解决 |
|------|------|------|
| `time` 和 `/usr/bin/time` 输出不同 | shell 内置 time 格式不同，`/usr/bin/time` 可自定义 | 跨平台脚本始终用 `command time cmd`（强制使用外部命令）或 `/usr/bin/time -p` |
| 管道命令的 `time` 只认最后一段 | `time cmd1 \| cmd2` 只测量 cmd2 的时间 | 用 `time bash -c 'cmd1 \| cmd2'` 或 `time (cmd1 \| cmd2)` 测整个管道 |
| macOS 上 `time` 显示的 user/sys 是内核时间，不是 wall clock | 初学者容易混淆 real（实际经过）和 user（CPU 时间） | real 是你感受到的时间；user 是程序实际用 CPU 的时间；多核并行时 user > real |
| 结果被重定向到文件时抓不到 | `time` 输出走 stderr，`> file` 只能捕获 stdout | 用 `(time cmd) 2> time.log` 或 `time cmd 2>&1 \| tee log.txt` |
| 内置 time 的精度不够 | shell 内置 time 只显示 3 位小数 | 用 `/usr/bin/time -p` 或 `gtime`（coreutils）获得微秒级精度 |

## 🔗 参考链接

- macOS 手册：`man time`
- [GNU time 文档](https://www.gnu.org/software/time/)
- [Understanding `time` output (real/user/sys)](https://stackoverflow.com/questions/556405/what-do-real-user-and-sys-mean-in-the-output-of-time1)