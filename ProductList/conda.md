# Conda — Python 环境管理工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Anaconda Inc. |
| 首次发布 | 2012 年 |
| 用途 | 跨语言的包管理和虚拟环境管理工具，在数据科学和机器学习领域广泛使用 |
| macOS 自带 | ❌ 否（需要手动安装，下载 Miniconda 或 Anaconda） |

## 🎯 用来做什么？

- **管理虚拟环境**：为不同项目创建隔离的 Python 环境，避免依赖冲突
- **安装科学计算包**：安装 NumPy、Pandas、TensorFlow 等数据科学包，比 pip 更省心（自动处理底层库）
- **管理 Python 版本**：在同一台机器上安装和使用多个 Python 版本
- **跨平台统一**：Windows、macOS、Linux 命令完全一致，团队协作无缝切换

> **pip vs conda 的区别**：pip 只管理 Python 包，conda 管理包括 Python 本身在内的所有软件包。conda 能自动解决复杂的依赖冲突（比如某个包依赖 Python 3.8 而另一个依赖 Python 3.9）。

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `conda create -n myenv python=3.11` | 创建名为 myenv 的环境，指定 Python 版本 |
| `conda activate myenv` | 激活环境（macOS/Linux 上用 `source activate myenv` 也可以） |
| `conda deactivate` | 退出当前环境 |
| `conda install numpy pandas` | 在当前环境中安装包 |
| `conda list` | 列出当前环境所有已安装包 |
| `conda env list` | 列出所有环境（带星号的是当前激活的） |
| `conda remove -n myenv --all` | 删除整个环境 |
| `conda search tensorflow` | 搜索可用的包版本 |
| `conda update --all` | 更新当前环境所有包 |
| `conda info` | 显示 conda 版本和配置信息 |

## 💡 日常使用示例

### 示例 1：创建一个数据科学项目环境

```bash
# 创建新环境，指定 Python 3.11
conda create -n data-science python=3.11

# 激活环境
conda activate data-science

# 安装常用数据科学包
conda install numpy pandas matplotlib jupyter scikit-learn

# 确认安装成功
conda list | grep -E "numpy|pandas|matplotlib"

# 输出示例：
# matplotlib            3.9.2           py311h4b99c57_0
# numpy                 1.26.4          py311h7183622_0
# pandas                2.2.2           py311h04ac794_0
```

### 示例 2：在不同 Python 版本之间切换

```bash
# 用 Python 3.9 创建旧项目环境
conda create -n legacy python=3.9
conda activate legacy
python --version
# Python 3.9.18

# 用 Python 3.12 创建新项目环境
conda create -n modern python=3.12
conda activate modern
python --version
# Python 3.12.7

# 查看所有环境
conda env list
# 输出示例：
# base                  /opt/miniconda3
# data-science          /opt/miniconda3/envs/data-science
# legacy                /opt/miniconda3/envs/legacy
# modern              * /opt/miniconda3/envs/modern
# （星号表示当前激活的是 modern）
```

### 示例 3：从 environment.yml 文件重建环境

```bash
# 先把当前环境的配置导出
conda env export > environment.yml

# 文件内容概览（会自动记录所有包及其版本）：
# name: data-science
# channels:
#   - defaults
# dependencies:
#   - python=3.11
#   - numpy=1.26.4
#   - pandas=2.2.2
# ...

# 团队成员拿到后用这个文件一键重建相同环境
conda env create -f environment.yml
```

## 🚨 常见坑点

| 问题 | 现象 | 解决方法 |
|------|------|----------|
| **命令找不到（command not found）** | 安装后输入 `conda` 提示找不到命令 | 安装时勾选"Add to PATH"，或手动运行 `source ~/miniconda3/bin/activate` 初始化 |
| **环境激活后 pip 还是系统的** | `which pip` 指向系统 Python 的 pip | 确保先激活环境再装包；在 conda 环境里建议优先用 `conda install`，找不到再用 `pip install` |
| **conda 安装速度极慢** | 下载包卡在 `Solving environment` | 换国内镜像源：`conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/` |
| **磁盘占用太大** | Anaconda 完整版占几个 GB | 用 Miniconda（≈500MB）替代完整 Anaconda，按需装包 |
| **pip 和 conda 混合使用导致依赖混乱** | 用 pip 装了 conda 管理的包后环境无法更新 | 尽量只用一种方式管理，如果需要用 pip，在 conda 装完所有能找到的包后最后再用 pip |
| **Shell 重启后环境丢失** | 每次新开终端都要重新 `conda activate` | 用 `conda config --set auto_activate_base false` 控制是否自动激活 base 环境 |

## 🔗 参考链接

- [Conda 官方文档](https://docs.conda.io/en/latest/)
- [Miniconda 下载页](https://docs.conda.io/en/latest/miniconda.html)
- [Anaconda 官方文档](https://docs.anaconda.com/)
- [清华镜像站 Conda 配置](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
