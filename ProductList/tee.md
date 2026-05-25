# tee — 输出分流器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Unix 原生命令 |
| 首次发布 | 1970s |
| 用途 | 同时将数据输出到**文件**和**终端**——像 T 型三通管，一边流到文件，一边继续流向终端 |

## 🎯 用来做什么？

- **记录命令执行日志** — 脚本执行时既在屏幕上看到输出，又自动保存到文件
- **管道中多路保存** — 在长管道中间插一个 `tee`，将中间结果存下来方便调试
- **配合 sudo 写入受保护文件** — `echo ... | sudo tee /etc/hosts` 比 `sudo echo ... > file` 更可靠
- **追加写入而不覆盖** — 用 `tee -a` 追加到文件末尾，适合持续记录日志

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `command \| tee file.txt` | 将命令输出同时显示在屏幕并写入 file.txt（覆盖写入） |
| `command \| tee -a file.txt` | 追加写入——不覆盖已有内容 |
| `command \| tee file1.txt file2.txt` | 同时写入多个文件 |
| `command \| tee /dev/tty \| grep ...` | 标准用法：输出到屏幕 + 进入下一个管道 |
| `echo \| sudo tee file` | 用 sudo 写入受保护文件（替代 `sudo echo`） |
| `command \| tee >(cmd1) \| cmd2` | 用进程替换分流到多个处理命令 |
| `command 2>&1 \| tee log.txt` | 同时捕获 stderr 和 stdout 到日志 |

## 💡 日常使用示例

### 示例 1：边看边存——运行测试或构建

```bash
# 运行测试，同时在屏幕看到输出并保存到文件
cd /tmp
python3 -c "
import time
for i in range(5):
    print(f'Test case {i+1}: PASS')
    time.sleep(0.2)
" | tee test_result.txt

# 屏幕上会逐行显示：
# Test case 1: PASS
# Test case 2: PASS
# Test case 3: PASS
# Test case 4: PASS
# Test case 5: PASS

# 检查是否已保存到文件
cat test_result.txt
# 输出同上——屏幕和文件完全一致
```

### 示例 2：管道中查看中间结果

```bash
cat > /tmp/numbers.txt << 'EOF'
apple
banana
cherry
date
elderberry
EOF

# 假设你运行一条长管道，想知道 grep 后的结果是什么
cat /tmp/numbers.txt | grep 'e' | tee filtered.txt | sort -r

# 屏幕输出（sort -r 的结果，即倒序）：
# elderberry
# date
# cherry
# banana
# apple

# 但 filtered.txt 存的是 tee 截取的那一步（grep 之后、sort 之前）：
cat filtered.txt
# 输出：
# apple
# banana
# cherry
# date
# elderberry
```

### 示例 3：用 sudo 写入受保护文件

```bash
# 常见错误：重定向时不走 sudo
# 下面这行会报 "Permission denied"
# echo "127.0.0.1 myapp.local" > /etc/hosts

# 正确做法：用 tee 配合 sudo
echo "127.0.0.1 myapp.local" | sudo tee -a /etc/hosts

# tee 运行在 sudo 权限下，接管了写文件的动作
# 终端会输出追加的内容作为回显
# 输出：
# 127.0.0.1 myapp.local

# 验证：
tail -1 /etc/hosts
# 输出：
# 127.0.0.1 myapp.local
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|---------|
| `tee` 默认**覆盖**文件 | 多次运行导致之前的内容丢失 | 加 `-a` 参数：`command \| tee -a file.log` |
| 用 `sudo echo > file` 报 Permission denied | shell 的 `>` 重定向是当前 shell 进程执行的，sudo 只管 echo | 改为 `echo \| sudo tee file` |
| `tee` 会阻断管道 | 以为 tee 只是"偷看"，实际上它输出了全部内容到下一步 | 理解 `tee` 输出 = 输入（不减量），就是 T 型三通 |
| macOS `tee` 不支持 `--help` | 运行 `tee --help` 报错 | macOS 的 BSD tee 用 `man tee` 看帮助 |
| 大量输出时 tee 拖慢速度 | 写磁盘 I/O 成为瓶颈 | 如只需日志不关心实时输出，用 `command > log.txt 2>&1` 替代 |

## 🔗 参考链接

- [macOS tee man page](https://ss64.com/osx/tee.html)
- [GNU Coreutils tee documentation](https://www.gnu.org/software/coreutils/manual/html_node/tee-invocation.html)
