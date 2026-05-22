# diff — 文件差异比较工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | AT&T Bell Labs（Unix 原生工具） |
| 首次发布 | 1970年代（V5 Unix） |
| 用途 | 逐行比较两个文件或两个目录的内容差异，输出最小编辑指令集 |

## 🎯 用来做什么？

- **代码审查** — 比较修改前后的文件，看改了什么 (`diff -u`)
- **配置文件对比** — 比较本地和服务器配置的区别
- **目录同步** — 比较两个目录有哪些文件不同 (`diff -rq`)
- **生成补丁(patch)** — 输出可被 `patch` 命令应用的差异文件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `diff -u file1 file2` | 统一格式输出差异（最常用，可读性好） |
| `diff -c file1 file2` | 上下文格式输出（3 行上下文） |
| `diff -q file1 file2` | 只报告文件是否相同（安静模式） |
| `diff -rq dir1 dir2` | 递归比较两个目录 |
| `diff -u -w file1 file2` | 忽略空格和空行的差异 |
| `diff --color -u file1 file2` | 彩色输出（macOS 13+ 支持） |
| `diff -Naur dir1 dir2` | 生成标准补丁文件（结合 `-N` 处理新文件） |

## 💡 日常使用示例

### 示例 1：比较两个文件（代码改动审查）

假设你改了一个配置文件，想确认改了啥：

```bash
$ cat > /tmp/config-v1.txt << 'EOF'
server_port=8080
debug=false
log_level=info
database_url=localhost:5432
EOF

$ cat > /tmp/config-v2.txt << 'EOF'
server_port=3000
debug=true
log_level=info
database_url=prod-db:5432
EOF

$ diff -u /tmp/config-v1.txt /tmp/config-v2.txt
--- /tmp/config-v1.txt	2026-05-23 01:01:00
+++ /tmp/config-v2.txt	2026-05-23 01:01:30
@@ -1,4 +1,4 @@
-server_port=8080
+server_port=3000
-debug=false
+debug=true
 log_level=info
-database_url=localhost:5432
+database_url=prod-db:5432
```

解读：
- `-` 开头：左边文件独有的行（被删掉的旧内容）
- `+` 开头：右边文件独有的行（新增的新内容）
- 空格开头：两文件相同的行（上下文）

### 示例 2：比较两个目录

看看两个项目目录有什么不同：

```bash
$ mkdir -p /tmp/proj-v1/src /tmp/proj-v2/src
$ echo "version 1" > /tmp/proj-v1/README.md
$ echo "version 2" > /tmp/proj-v2/README.md
$ touch /tmp/proj-v1/src/main.py
$ touch /tmp/proj-v2/src/main.py /tmp/proj-v2/src/utils.py

$ diff -rq /tmp/proj-v1 /tmp/proj-v2
Files /tmp/proj-v1/README.md and /tmp/proj-v2/README.md differ
Only in /tmp/proj-v2: utils.py
```

- `Files ... differ`：文件存在但内容不同
- `Only in ...`：某目录独有的文件

### 示例 3：生成和应用补丁

```bash
# 先生成补丁文件
$ diff -u fix-bug-v1.py fix-bug-v2.py > bugfix.patch

# 别人拿到补丁后应用
$ patch -p0 < bugfix.patch

# 或者回退
$ patch -R -p0 < bugfix.patch
```

这种方式是开源社区（Linux、Git 早期）广泛使用的协作模式。

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| `diff -u` 输出为空 | 两个文件完全相同 | 用 `diff -q` 确认，再加 `|| echo "differ"` |
| 太多无用的空格差异 | 缩进或行尾空格不同 | 加 `-w` 忽略所有空格，或 `-b` 忽略行尾空格 |
| 目录比较大很慢 | 递归遍历所有文件 | 加 `-q` 只报告是否相同，不加 `-u` 看内容 |
| macOS 上没有 `--color` | 旧版 BSD diff 不支持 | 用 `brew install diffutils` 装 GNU 版 `gdiff` |
| `diff -r` 漏掉了新文件 | 默认只比较同名文件 | 加 `-N`（GNU）或先 `ls` 对比文件列表 |
| 比较二进制文件输出乱码 | `diff` 默认按文本比较 | 用 `diff -q` 或用 `cmp` 替代 |

## 🔗 参考链接

- [macOS diff 手册 (man page)](https://man.freebsd.org/cgi/man.cgi?diff)
- [GNU diffutils 文档](https://www.gnu.org/software/diffutils/manual/)
- macOS 自带，无需安装
