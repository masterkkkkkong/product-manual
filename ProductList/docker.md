# Docker — 容器化应用平台

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Docker Inc.（最初由 Solomon Hykes 创建） |
| 首次发布 | 2013 年 |
| 用途 | 将应用及其依赖打包到轻量级容器中，确保在任何环境下都能一致运行 |

## 🎯 用来做什么？

- **消除环境差异**：在本地开发环境打包，部署到服务器或别人电脑上也能跑，告别"在我电脑上好好的"问题
- **快速搭建开发环境**：跑一个 `docker run` 就能启动 MySQL、Redis、PostgreSQL 等数据库，不需要手动安装配置
- **隔离应用依赖**：每个容器独立运行，不同项目可以用不同版本的 Node/Python/Java，互不干扰
- **微服务和 CI/CD**：每个服务独立容器化，配合 Docker Compose 一键启动整套架构

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `docker ps` | 列出当前正在运行的容器 |
| `docker ps -a` | 列出所有容器（包括已停止的） |
| `docker images` | 列出本地已下载的镜像 |
| `docker pull <镜像名>` | 从 Docker Hub 下载镜像 |
| `docker run <镜像名>` | 从镜像启动一个新容器 |
| `docker stop <容器ID>` | 停止一个运行中的容器 |
| `docker rm <容器ID>` | 删除一个已停止的容器 |
| `docker rmi <镜像ID>` | 删除一个本地镜像 |
| `docker exec -it <容器ID> bash` | 进入正在运行的容器内部操作 |

## 💡 日常使用示例

### 示例 1：启动一个 MySQL 数据库

```bash
# 从 Docker Hub 拉取 MySQL 镜像（可省略 pull，run 时自动拉取）
docker pull mysql:8.0

# 启动 MySQL 容器
#   --name         给容器取名
#   -e MYSQL_ROOT_PASSWORD  设置 root 密码
#   -d            后台运行（detached）
#   -p 3306:3306  将宿主机的 3306 端口映射到容器的 3306
docker run --name my-mysql -e MYSQL_ROOT_PASSWORD=mysecret -d -p 3306:3306 mysql:8.0

# 查看容器是否在运行
docker ps

# 用宿主机的 MySQL 客户端连接（或其他工具如 Sequel Ace）
mysql -h 127.0.0.1 -u root -p
# 输入密码 mysecret 即可进入 MySQL>

# 停止并删除容器
docker stop my-mysql
docker rm my-mysql
```

### 示例 2：用容器跑一个静态网站

```bash
# 用 nginx 镜像快速启动一个文件服务器
# 将宿主机的当前目录挂载到容器的 /usr/share/nginx/html
docker run -d -p 8080:80 -v $(pwd):/usr/share/nginx/html --name my-site nginx:alpine

# 访问 http://localhost:8080 就能看到当前目录的内容
# 修改文件后刷新浏览器即可看到变化，无需重启容器

# 停止并删除
docker stop my-site && docker rm my-site
```

### 示例 3：用 Docker Compose 启动一个 Web 应用 + Redis

创建 `docker-compose.yml`：

```yaml
version: '3.8'
services:
  web:
    image: node:20-alpine
    working_dir: /app
    volumes:
      - ./my-app:/app
    ports:
      - "3000:3000"
    command: sh -c "npm install && npm run dev"
    depends_on:
      - redis

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
```

启动整套服务：

```bash
# 在 docker-compose.yml 同级目录下执行
docker compose up -d

# 查看服务状态
docker compose ps

# 查看日志
docker compose logs -f

# 停止服务
docker compose down
```

## 🚨 常见坑点

| 症状 | 原因 | 解决方法 |
|------|------|----------|
| `Cannot connect to the Docker daemon` | Docker Desktop 没有启动 | 打开 Docker Desktop 应用，等待守护进程启动 |
| `port is already allocated` | 端口已被占用 | 换一个端口映射，如 `-p 3307:3306` |
| `docker: command not found` | Docker 未安装 | macOS 安装：`brew install --cask docker` |
| `Permission denied` | 当前用户不在 docker 组 | 一般 macOS 下不需要，Linux 需要 `sudo usermod -aG docker $USER` |
| 容器退出后数据丢失 | 容器内的数据默认不持久化 | 使用 `-v` 或 `--mount` 挂载宿主机目录或 named volume |
| `docker pull` 很慢 | Docker Hub 在国内访问慢 | 配置国内镜像加速器（阿里云、中科大、七牛云） |
| Apple Silicon 兼容问题 | Intel 镜像在 M 芯片上可能运行有问题 | 加 `--platform linux/amd64` 或使用 arm64 镜像 |

## 🔗 参考链接

- [Docker 官方文档](https://docs.docker.com/)
- [Docker Hub（镜像市场）](https://hub.docker.com/)
- [Docker Compose 文档](https://docs.docker.com/compose/)
- [Docker Cheat Sheet](https://docs.docker.com/get-started/docker_cheatsheet.pdf)
