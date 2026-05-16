# zsh — Shell 工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Paul Falstad（原创），Peter Stephenson（后续维护） |
| 首次发布 | 1990 年 |
| 用途 | 增强型 Unix Shell，兼容 Bash 但同时内置更强大的自动补全、通配符、主题系统。macOS 从 Catalina（2019）起默认 Shell 改为 zsh |

## 🎯 用来做什么？

- **日常命令行操作**：作为 Bash 的替代品，兼容性好，上手零成本
- **自动补全狂魔**：zsh 的补全功能比 Bash 强很多——敲 `git ch` 按 Tab 会建议 `checkout`、`cherry-pick`、`cherry`，再按一次还能显示参数说明
- **主题与美化**：配合 Oh My Zsh 或 Powerlevel10k，终端可以变得非常漂亮，显示 Git 分支、Python 虚拟环境、命令执行时间等
- **高级通配符和文件操作**：`**/*.py` 递归匹配、`^` 排除匹配、`zmv` 批量重命名等，文件处理效率翻倍

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `echo $ZSH_VERSION` | 查看当前 zsh 版本 |
| `which zsh` | 查看 zsh 安装位置 |
| `chsh -s /bin/zsh` | 将默认 Shell 切换为 zsh（macOS 上一般已是） |
| `source ~/.zshrc` | 重载配置文件，使修改生效 |
| `echo $SHELL` | 确认当前正在使用的 Shell |
| `<TAB>` | 自动补全命令、参数、文件路径、Git 分支等 |
| `<TAB><TAB>` | 连续按两次 Tab 显示补全候选列表及说明 |
| `echo !$` | 引用上一条命令的最后一个参数 |
| `cd /u/lo/co` + `<TAB>` | zsh 支持路径缩写补全：`/usr/local/bin` 只需敲首字母 |
| `ls **/*.py` | 递归查找当前目录下所有 .py 文件（** 是 zsh 特色） |
| `ls -l ^*.py` | 列出所有非 .py 结尾的文件（^ 是 zsh 排除语法） |

## 💡 日常使用示例

### 示例 1：利用自动补全快速操作 Git

```bash
# 假如你有一个分支叫 feature/add-login-page
# 不用敲全名：
git checkout feat<TAB>
# zsh 会自动补全为：feature/add-login-page

# 查看状态也不用敲全：
git st<TAB>
# 显示 git status, stash, shortlog 等候选，选一个回车即可

# 查看 diff：
git d<TAB>
# 显示 diff, difftool 等
```

### 示例 2：目录快速跳转——不用 cd 也能走

```bash
# 假设你经常去 /Users/mk/projects/my-app/src/components
# 传统做法：
cd /Users/mk/projects/my-app/src/components

# zsh 路径缩写（如果设置了 setopt auto_cd 和 cdpath）：
# 直接用目录名即可（前提是在 CDPATH 中）
# 或者用通配符模糊跳转：
cd /u/mk/pro/my-app/sr/co
# zsh 会自动展开匹配：/Users/mk/projects/my-app/src/components

# 回到上一个目录：
cd -
# 回到之前所在的目录，比反复敲路径方便多了
```

### 示例 3：批量重命名和文件操作

```bash
# 场景：将当前目录下所有 .txt 重命名为 .md
# 方法一：zmv 模块（zsh 特有的批量重命名工具）
autoload -Uz zmv
zmv '(*).txt' '$1.md'

# 方法二：只用通配符配合循环
for f in *.txt; do
  mv "$f" "${f%.txt}.md"
done

# 场景：找出 src/ 下所有大于 100KB 的图片
ls src/**/*.(jpg|png|gif)(Lm+100)
# ** 递归所有目录，(jpg|png|gif) 多后缀匹配，(Lm+100) 大于 100KB 限定符
```

## 🚨 常见坑点

| 坑点 | 说明 | 解决方法 |
|------|------|---------|
| 配置不生效 | 修改了 `~/.zshrc` 后没有重载 | 运行 `source ~/.zshrc` |
| Bash 脚本在 zsh 下报错 | zsh 不完全兼容 Bash（主要差在数组、`source` 行为） | 脚本首行写 `#!/bin/bash` 强制用 Bash 执行；或在 zsh 中运行 `bash script.sh` |
| 按 Tab 没反应或补全奇怪 | 补全系统未正确加载 | 检查 `~/.zshrc` 中是否有 `autoload -Uz compinit && compinit` |
| Oh My Zsh 启动变慢 | 插件装太多或主题太重 | 禁用不需要的插件，或换轻量主题；用 `zsh -i -c time exit` 测试启动时间 |
| macOS 升级后 Shell 被重置 | macOS 大版本升级有时会把 Shell 重置为 zsh（但配置不会丢） | 确认 `chsh -l` 能看到 zsh，然后 `chsh -s /bin/zsh` |
| 通配符被转义 | 有些命令期待的是字符串而不是文件列表（如 `find . -name "*.txt"`） | 用单引号包住通配符防止 zsh 展开 |

## 🔗 参考链接

- [Zsh 官方文档](https://zsh.sourceforge.io/Doc/)
- [Oh My Zsh 官网](https://ohmyz.sh/)
- [Powerlevel10k 主题](https://github.com/romkatv/powerlevel10k)
- [Zsh 入门指南（中文）](https://github.com/ohmyzsh/ohmyzsh/wiki)
