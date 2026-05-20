# Make — 构建自动化工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | GNU 项目（Stuart Feldman 创建） |
| 首次发布 | 1976 年 |
| 用途 | 通过 Makefile 自动化编译、测试、部署等重复性任务 |

## 🎯 用来做什么？

- **自动编译项目** — 一行命令编译整个项目，增量编译只修改了的部分
- **任务编排** — 定义清理、测试、打包、部署等工作流
- **跨项目标准化** — 用统一的 `make build`、`make test`、`make clean` 约定
- **依赖管理** — 自动检测哪些文件需要重新编译

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `make` | 运行 Makefile 中第一个目标（通常是 all） |
| `make target` | 运行指定目标 |
| `make -j4` | 并行执行 4 个任务，加快构建 |
| `make clean` | 清理编译产物 |
| `make install` | 安装到系统目录 |
| `make -n` | 预览要执行的命令（dry run，不实际执行） |
| `make -C dir` | 在指定目录执行 make |
| `make -f Makefile.custom` | 使用自定义名称的 Makefile |

## 💡 日常使用示例

### 示例 1：Python 项目自动化 Makefile

创建一个标准化的开发工作流：

```makefile
# Makefile
.PHONY: install test lint clean run

install:
	pip install -r requirements.txt

test:
	python -m pytest tests/ -v

lint:
	ruff check .

clean:
	rm -rf __pycache__ .pytest_cache *.pyc
	find . -type d -name __pycache__ -exec rm -rf {} + 2>/dev/null || true

run:
	python main.py
```

使用方式：

```bash
# 安装依赖
make install

# 运行测试
make test

# 清理缓存
make clean
```

### 示例 2：前端项目自动化

```makefile
# Makefile
.PHONY: build dev test clean

build:
	npm run build

dev:
	npm run dev

test:
	npm test

clean:
	rm -rf node_modules dist
```

### 示例 3：多步骤部署流水线

```makefile
# Makefile
.PHONY: all deps lint test build deploy clean

all: deps lint test build

deps:
	pip install -r requirements-dev.txt
	npm install

lint:
	flake8 src/
	eslint src/

test:
	python -m pytest tests/ -v --cov=src

build:
	python setup.py sdist bdist_wheel
	docker build -t myapp:latest .

deploy: build
	docker push myapp:latest
	kubectl apply -f k8s/

clean:
	rm -rf dist/ build/ *.egg-info
	rm -rf node_modules
```

使用：

```bash
# 一键完成全部流程
make deploy

# 或只执行某一步
make lint
make test
```

## 🚨 常见坑点

| 错误现象 | 原因 | 解决方法 |
|----------|------|----------|
| `Makefile:1: *** missing separator. Stop.` | 命令前用了空格而不是 Tab | Makefile 中命令缩进只能用 **Tab**，不能用空格 |
| `make: Nothing to be done for 'target'` | 目标已最新，或目标不是 .PHONY | 如果是伪目标（不产生文件），声明为 `.PHONY: target` |
| 变量赋值不生效 | 搞混 `=`、`:=`、`?=` 的区别 | `=` 递归展开，`:=` 立即求值，`?=` 仅在未定义时赋值 |
| macOS 上是 GNU Make 3.81（较旧） | macOS 自带旧版本 | 用 `brew install make` 安装新版，新版的命令是 `gmake` |

## 🔗 参考链接

- [GNU Make 官方手册](https://www.gnu.org/software/make/manual/make.html)
- [Makefile 教程](https://makefiletutorial.com/)
- [Make 速查表](https://gist.github.com/isaacs/62a2d1825d04437c6d08)
