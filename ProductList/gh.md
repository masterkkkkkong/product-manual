# GitHub CLI (gh) — 在终端管理 GitHub 仓库和流程

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | GitHub Inc. |
| 首次发布 | 2019 年 |
| 用途 | 在命令行中直接操作 GitHub：管理仓库、Issue、PR、Release、Gist 等，不用离开终端 |

## 🎯 用来做什么？

- **管理仓库**：创建、克隆、Fork 仓库，查看仓库信息，全部在终端完成
- **处理 Issue 和 PR**：创建、查看、评论、合并 Pull Request，管理 Issue 和标签
- **CI/CD 操作**：查看 GitHub Actions 工作流运行状态、重试失败 job、管理 workflow
- **GitHub API 快捷访问**：`gh api` 命令直接调用任意 GitHub REST API，免去 curl + token 的麻烦

## 🔧 常用命令

```bash
# 认证登录 GitHub
gh auth login

# 查看当前仓库信息
gh repo view

# 克隆一个仓库
gh repo clone owner/repo

# 创建 Issue
gh issue create --title "bug: 登录失败" --body "描述问题..."

# 列出当前仓库的 Issue
gh issue list

# 创建 PR
gh pr create --title "修复登录bug" --body "修复了..." --base main

# 查看 PR 列表
gh pr list

# 合并 PR
gh pr merge 42

# 查看 Actions 运行状态
gh run list

# 查看 Release
gh release list

# 直接调用 GitHub REST API
gh api repos/owner/repo
```

## 💡 日常使用示例

### 示例 1：从零开始创建一个仓库并推送代码

```bash
# 1. 先认证（首次使用）
gh auth login
# 选择 Login with a web browser，按提示操作即可

# 2. 在本地初始化项目
mkdir my-awesome-project
cd my-awesome-project
git init
echo "# My Awesome Project" > README.md
git add -A
git commit -m "init"

# 3. 在 GitHub 上创建远程仓库并推送
gh repo create my-awesome-project --public --source=. --remote=origin --push

# 输出示例
✓ Created repository owner/my-awesome-project on GitHub
✓ Added remote origin
✓ Pushed commits to origin

# 4. 确认
gh repo view --web   # 在浏览器中打开
```

### 示例 2：处理 Pull Request 全流程

```bash
# 1. 查看当前仓库中所有待处理的 PR
gh pr list

# 输出示例
Showing 2 of 2 pull requests in owner/my-awesome-project
#42   fix-login-bug   修复登录闪退问题    feature/login
#41   add-dark-mode   新增暗黑模式          feature/dark

# 2. 查看 PR #42 的详情
gh pr view 42

# 3. 将 PR 的代码拉到本地查看
gh pr checkout 42

# 4. 添加评论
gh pr comment 42 --body "改动看起来不错，建议加个单元测试"

# 5. 合并 PR（使用 squash 方式）
gh pr merge 42 --squash --subject "fix: 修复登录闪退"

# 输出示例
✓ Merged pull request #42 (fix-login-bug)
```

### 示例 3：查看和处理 GitHub Actions 工作流

```bash
# 1. 查看最近的 workflow 运行
gh run list --limit 5

# 输出示例
STATUS   NAME                 WORKFLOW     BRANCH    ELAPSED  AGE
✓        build                CI           main      1m23s    2m ago
✗        test                 CI           feature   2m05s    5m ago
...       lint                 CI           main      45s      10m ago

# 2. 查看失败的运行详情
gh run view 1234567890

# 3. 重试失败的 job
gh run rerun 1234567890

# 4. 查看实时日志
gh run watch 1234567890
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `gh` 命令找不到 | 未安装 GitHub CLI | `brew install gh`（macOS），或从 [cli.github.com](https://cli.github.com) 下载 |
| `gh auth login` 显示超时 | 浏览器认证流程被阻断 | 改用 Token 方式：`gh auth login --with-token < token.txt`，或在环境变量设置 `GH_TOKEN` |
| `gh pr create` 报错 `no commits` | 当前分支没有新提交 | 确认已 `git add` 和 `git commit`，且分支和 base 分支相比有差异 |
| `gh repo view` 提示 "not a git repository" | 当前目录不在 Git 仓库中 | 先 `cd` 到仓库目录，或 `git init` / `git clone` |
| `gh issue list` 为空但 GitHub 上明明有 Issue | 访问了错误的仓库 | 用 `-R owner/repo` 指定仓库，或 `cd` 到正确目录 |
| 推送大仓库很慢 | 网络问题或仓库过大 | 使用 `--depth 1` 浅克隆，或检查网络连接 |
| macOS 上 `gh pr checkout` 报 `git fetch` 错误 | 远程分支关联问题 | 先 `git fetch origin`，再重试 `gh pr checkout` |
| `gh help` 内容太多找不到需要的子命令 | 子命令太多 | 用 `gh <command> --help` 查看具体命令的帮助，或 `gh help` 浏览分类 |

> **macOS 特别说明**：gh 在 macOS 上表现与 Linux 一致。推荐用 Homebrew 安装：`brew install gh`。认证时如果默认浏览器打不开，可以用 `gh auth login -h github.com --web` 重新尝试。

## 🔗 参考链接

- [GitHub CLI 官网](https://cli.github.com/)
- [GitHub CLI 手册](https://docs.github.com/en/github-cli/github-cli/about-github-cli)
- [GitHub CLI GitHub 仓库](https://github.com/cli/cli)
- [GitHub CLI 命令参考](https://cli.github.com/manual/)
