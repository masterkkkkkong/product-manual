# Git — 分布式版本控制系统

## 📌 简介

| 项目 | 内容 |
|------|------|
| **全称** | Git（没有全称，Linus Torvalds 起的名） |
| **开发者** | Linus Torvalds（Linux 之父）于 2005 年开发 |
| **维护者** | 目前由 Junio C Hamano 维护 |
| **用途** | 分布式版本控制系统，追踪代码/文件的每一次修改 |

## 🎯 用来做什么？

- **记录历史**：每次修改都被记录下来，可以随时回到任意版本
- **协作开发**：多人同时修改同一个项目，不会互相覆盖
- **分支管理**：可以在独立的分支上开发新功能，完成后合并回主分支
- **备份代码**：配合 GitHub/GitLab 使用，代码云端存储

## 🔧 常用命令

### 1. 配置（装好后先做一次）
```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

### 2. 创建仓库
```bash
# 方式一：在现有目录初始化
cd 你的项目文件夹
git init

# 方式二：克隆远程仓库
git clone https://github.com/用户名/仓库名.git
```

### 3. 日常开发流程（最常用）

```bash
# ① 查看当前状态
git status

# ② 添加文件到暂存区
git add 文件名        # 添加单个文件
git add .             # 添加所有更改

# ③ 提交到本地仓库
git commit -m "做了哪些改动"

# ④ 推送到远程仓库（如 GitHub）
git push
```

### 4. 分支管理
```bash
# 查看分支
git branch

# 创建新分支
git branch 新分支名

# 切换分支
git checkout 分支名

# 创建并切换到新分支（一步完成）
git checkout -b 新分支名

# 合并分支（先切回 main，再合并）
git checkout main
git merge 要合并的分支名

# 删除分支
git branch -d 分支名
```

### 5. 查看历史
```bash
# 查看提交历史
git log

# 简略版
git log --oneline

# 查看某个文件的历史
git log -- 文件名

# 看谁改了哪些内容
git blame 文件名
```

### 6. 撤销操作
```bash
# 撤销工作区的修改（还没 git add）
git checkout -- 文件名

# 撤销暂存区的文件（已经 git add，但还没 commit）
git reset HEAD 文件名

# 撤销上一次提交（已经 git commit，但还没 push）
git reset --soft HEAD~1    # 保留更改
git reset --hard HEAD~1    # 彻底删除更改（慎用！）
```

### 7. 远程仓库操作
```bash
# 添加远程仓库
git remote add origin https://github.com/用户名/仓库名.git

# 查看远程仓库
git remote -v

# 拉取最新代码
git pull

# 强制推送（谨慎使用）
git push --force
```

## 💡 日常使用示例

### 示例 1：第一次推送本地项目到 GitHub
```bash
cd my-project
git init
git add .
git commit -m "第一次提交"
git remote add origin https://github.com/用户名/my-project.git
git push -u origin main
```
> `-u` 是 `--set-upstream`，设置上游分支，以后直接 `git push` 即可。

### 示例 2：从零参与一个已有项目
```bash
git clone https://github.com/某项目/某项目.git
cd 某项目
git checkout -b my-feature
# 修改代码...
git add .
git commit -m "添加了XX功能"
git push -u origin my-feature
```
> 然后在 GitHub 上发起 Pull Request。

### 示例 3：解决冲突
当 `git pull` 提示冲突时：
1. Git 会在冲突文件中标记 `<<<<<<<` 和 `>>>>>>>`
2. 手动编辑文件，保留想要的部分，删掉标记
3. 然后：
```bash
git add 冲突文件
git commit -m "解决冲突"
```

## 🚨 常见坑点

| 错误 | 原因 | 解决 |
|------|------|------|
| `fatal: not a git repository` | 当前目录不是 Git 仓库 | `git init` 或在正确目录执行 |
| `Please tell me who you are` | 没配用户名和邮箱 | `git config --global user.name "xx"` |
| `Merge conflict` | 两人改了同一文件同一行 | 手动编辑解决冲突 |
| `Permission denied (publickey)` | SSH 密钥未配置 | 用 HTTPS 方式克隆，或添加公钥到 GitHub |

## 🔗 参考链接

- [Git 官方文档](https://git-scm.com/doc)
- [Pro Git 书籍（免费）](https://git-scm.com/book/zh/v2)
- [GitHub 学习中心](https://docs.github.com/zh/get-started)
