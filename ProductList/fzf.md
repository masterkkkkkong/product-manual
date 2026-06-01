# fzf — 命令行模糊搜索神器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Junegunn Choi |
| 首次发布 | 2015 年 |
| 仓库 | https://github.com/junegunn/fzf |
| 用途 | 通用命令行模糊搜索工具，从任何列表中快速查找和选择 |

## 🎯 用来做什么？

- **快速查找文件**：在目录树中模糊搜索文件名，替代 `find` 的交互式查找
- **增强 Shell 体验**：集成到 zsh/bash，实现 Ctrl+R 历史搜索、Ctrl+T 文件选择等
- **管道数据搜索**：从任意命令的输出中模糊过滤和选择，速度极快
- **预览文件内容**：选中文件时实时显示文件内容预览（配合 `bat` 效果更佳）

## 🔧 常用命令

```bash
# 1. 从文件列表中选择（默认当前目录递归搜索）
fzf

# 2. 从管道数据中筛选
cat data.txt | fzf

# 3. 带文件内容预览
fzf --preview 'cat {}'

# 4. 启动搜索模式（模糊/精确/前缀/后缀）
fzf -e              # 精确匹配（Exact）
fzf -i              # 大小写不敏感（默认就是）
fzf +i              # 大小写敏感

# 5. 选择多个项目（Tab 标记）
fzf -m

# 6. 自定义提示文字和查询位置
fzf --prompt="选择文件> " --query=".py"

# 7. 无交互模式：直接输出第一个匹配项
echo -e "foo\nbar\nbaz" | fzf -f "ba"   # 输出 bar 和 baz
```

## 💡 日常使用示例

### 示例 1：交互式文件搜索

```bash
# 在当前目录递归搜索所有文件，支持模糊匹配
# 输入部分文件名即可实时过滤
fzf

# 更实用的用法：搜索 .md 文件
find . -name "*.md" | fzf
```

效果：启动一个交互式界面，你输入的每个字符都会即时过滤结果列表。按上下键选择，Enter 确认。

### 示例 2：用 fzf 搜索历史命令

```bash
# 安装 shell 集成后，Ctrl+R 变成 fzf 风格的交互搜索
# 不用再按多次 Ctrl+R 翻找了

# 手动版本：
history | fzf
```

### 示例 3：fzf + 预览 + 打开文件

```bash
# 搜索文件，实时预览内容，选中后用 vscode 打开
vim "$(fzf --preview 'bat --style=numbers --color=always {} 2>/dev/null || head -100 {}')"

# 分开理解：
#   fzf --preview '...'   → 选中时在右侧显示文件内容
#   vim "$(...)"           → 把 fzf 选中的路径传给 vim
```

### 示例 4：快速切换 Git 分支

```bash
# 列出分支 → fzf 选择 → 切换到选中分支
git checkout "$(git branch --all | fzf | tr -d '[:space:]')"
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|---------|
| 按 Ctrl+R 没有反应 | 没有安装 shell 集成 | 运行 `$(brew --prefix)/opt/fzf/install` 按提示安装 |
| 预览闪烁或乱码 | 预览命令输出包含颜色转义序列 | 给预览命令加 `--color=always` 参数 |
| `fzf: command not found` | 没有安装 | `brew install fzf` |
| 搜索结果太多卡顿 | 在非常大的目录（如 node_modules）中搜索 | 用 `find ... | fzf` 指定搜索范围，或用 fd 替代 find |
| macOS 上 fzf 颜色不对 | macOS 终端默认颜色不同 | 设置环境变量 `FZF_DEFAULT_OPTS="--color=bg+:#363a4f,fg:#cad3f5"` |

## 🔗 参考链接

- [GitHub 仓库](https://github.com/junegunn/fzf)
- [Wiki / 高级用法](https://github.com/junegunn/fzf/wiki)
- [Homebrew 安装](https://formulae.brew.sh/formula/fzf)