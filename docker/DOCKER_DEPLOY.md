# TikTube Docker 一键部署教程

> 本教程将指导你使用 Docker 从零开始部署 TikTube 视频网站

---

## 📋 目录

1. [前置环境检查](#1-前置环境检查)
2. [获取项目代码](#2-获取项目代码)
3. [配置环境变量](#3-配置环境变量)
4. [构建镜像](#4-构建镜像)
5. [启动容器](#5-启动容器)
6. [验证部署](#6-验证部署)
7. [常见问题排查](#7-常见问题排查)
8. [运维命令参考](#8-运维命令参考)

---

## 1. 前置环境检查

### 1.1 系统要求

| 项目 | 最低要求 | 推荐配置 |
|------|---------|---------|
| 操作系统 | Linux / macOS / Windows | Ubuntu 22.04+ |
| CPU | 2 核 | 4 核+ |
| 内存 | 4 GB | 8 GB+ |
| 磁盘 | 20 GB | 50 GB+ |

### 1.2 检查 Docker

```bash
# 检查 Docker 版本
docker --version
# 期望输出: Docker version 20.10.0+

# 检查 Docker Compose
docker compose version
# 期望输出: Docker Compose version v2.0.0+
```

若未安装 Docker，请参考官方文档：
- [Docker 官方安装指南](https://docs.docker.com/engine/install/)

### 1.3 检查端口

确保以下端口未被占用：

```bash
# Linux/macOS
sudo lsof -i :8080 -i :3306 -i :6379

# 如果有输出，说明端口被占用，需要先停止占用的服务
```

| 端口 | 用途 |
|------|------|
| 8080 | TikTube 主应用 |
| 3306 | MySQL 数据库 |
| 6379 | Redis 缓存 |

---

## 2. 获取项目代码

```bash
# 克隆仓库
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
cd TikTube-Docker

# 项目结构
.
├── TikTube/           # 后端 Spring Boot 源码
├── TikTubeWeb/        # 前端 Vue 源码
├── docker/            # Docker 配置目录
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── .env.example
│   └── DOCKER_DEPLOY.md
├── tik_tube.sql       # 数据库初始化脚本
└── README.md
```

---

## 3. 配置环境变量

```bash
cd docker

# 复制环境变量模板
cp .env.example .env

# 编辑配置（必须修改密码！）
nano .env
```

### 环境变量说明

```bash
# =========== MySQL 配置 ===========
MYSQL_ROOT_PASSWORD=你的MySQL密码    # ⚠️ 必须修改！
MYSQL_DATABASE=tik_tube

# =========== Redis 配置 ===========
REDIS_PASSWORD=你的Redis密码         # ⚠️ 必须修改！

# =========== TikTube 配置 ===========
TIKTUBE_OPEN_REDIS=true              # 启用 Redis 缓存
TIKTUBE_PROXY_CONFIGURED=false       # 如使用 Nginx/CDN 改为 true
```

> ⚠️ **安全提示**：请务必修改默认密码，切勿在生产环境使用示例密码！

---

## 4. 构建镜像

```bash
# 在 docker 目录下执行
docker compose build

# 构建过程约需 5-10 分钟，取决于网络速度
```

### 构建过程说明

Docker 会执行多阶段构建：

| 阶段 | 说明 | 镜像 |
|------|------|------|
| 1 | 编译前端 Vue 项目 | node:20-alpine |
| 2 | 编译后端 Spring Boot | maven:3.9-eclipse-temurin-17 |
| 3 | 打包运行环境 | eclipse-temurin:17-jre |

### 加速构建（可选）

如果网络较慢，已在 Dockerfile 中配置了国内镜像：
- npm 使用 `npmmirror.com`
- Maven 使用阿里云镜像

---

## 5. 启动容器

```bash
# 后台启动所有服务
docker compose up -d

# 查看启动状态
docker compose ps
```

### 期望输出

```
NAME            STATUS                   PORTS
tiktube-app     Up About a minute        0.0.0.0:8080->8080/tcp
tiktube-mysql   Up About a minute        0.0.0.0:3306->3306/tcp
tiktube-redis   Up About a minute        0.0.0.0:6379->6379/tcp
```

### 启动顺序

1. **MySQL** 先启动，健康检查通过后
2. **Redis** 启动，健康检查通过后
3. **TikTube** 应用最后启动

---

## 6. 验证部署

### 6.1 检查服务状态

```bash
# 查看容器状态
docker compose ps

# 查看应用日志
docker compose logs -f tiktube
```

### 6.2 访问网站

打开浏览器访问：

```
http://localhost:8080
http://你的服务器IP:8080
```

### 6.3 注册管理员

> 📌 **重要**：首次注册用户名为 `admin` 的账号将自动成为管理员！

1. 点击「注册」
2. 用户名填写 `admin`
3. 完成注册后即为管理员

---

## 7. 常见问题排查

### 7.1 数据库连接失败

**错误信息**：`Communications link failure`

**解决方案**：

```bash
# 检查 MySQL 容器状态
docker compose ps mysql

# 查看 MySQL 日志
docker compose logs mysql

# 如果数据库未初始化，重建容器
docker compose down -v
docker compose up -d
```

### 7.2 视频上传失败（SIGSEGV 错误）

**错误信息**：`A fatal error has been detected by the Java Runtime Environment: SIGSEGV`

**原因**：使用了 Alpine 镜像，与 JavaCV 不兼容

**解决方案**：确保 Dockerfile 使用 Debian 基础镜像：

```dockerfile
# ✅ 正确
FROM eclipse-temurin:17-jre

# ❌ 错误
FROM eclipse-temurin:17-jre-alpine
```

### 7.3 大文件上传内存溢出

**错误信息**：`OutOfMemoryError: Java heap space`

**解决方案**：增加 JVM 内存，修改 `docker-compose.yml`：

```yaml
environment:
  JAVA_OPTS: "-Xms2048m -Xmx5120m"
```

然后重启：

```bash
docker compose up -d tiktube
```

### 7.4 验证码一直错误

**可能原因**：通过多层代理访问，Cookie/Session 未正确传递

**解决方案**：

1. 清除浏览器缓存和 Cookie
2. 使用无痕模式访问
3. 尝试直接访问 `http://localhost:8080`（跳过代理）

### 7.5 容器启动后立即退出

```bash
# 查看退出原因
docker compose logs tiktube

# 常见原因：
# 1. MySQL 未就绪 → 等待几秒后重试
# 2. 配置错误 → 检查 .env 文件
# 3. 端口被占用 → 检查端口冲突
```

---

## 8. 运维命令参考

### 日常操作

```bash
# 启动服务
docker compose up -d

# 停止服务
docker compose down

# 重启单个服务
docker compose restart tiktube

# 查看日志
docker compose logs -f tiktube

# 查看所有日志（最近 100 行）
docker compose logs --tail 100
```

### 数据备份

```bash
# 备份 MySQL 数据
docker exec tiktube-mysql mysqldump -uroot -p'密码' tik_tube > backup.sql

# 备份上传文件
cp -r docker/data/uploads ./uploads_backup
```

### 更新部署

```bash
# 拉取最新代码
git pull

# 重新构建镜像
docker compose build

# 更新容器
docker compose up -d
```

### 清理资源

```bash
# 停止并删除容器（保留数据）
docker compose down

# 停止并删除容器和数据卷（⚠️ 会删除数据！）
docker compose down -v

# 清理未使用的镜像
docker image prune -a
```

---

## 📁 数据持久化

所有数据存储在 `docker/data/` 目录：

```
docker/data/
├── mysql/      # MySQL 数据库文件
├── redis/      # Redis 持久化数据
├── uploads/    # 用户上传的文件
└── logs/       # 应用日志
```

> 📌 备份时请确保备份整个 `data` 目录！

---

## 🔗 相关链接

- [原项目 GitHub](https://github.com/PuZhiweizuishuai/TikTube)
- [Docker 官方文档](https://docs.docker.com/)
- [Docker Compose 文档](https://docs.docker.com/compose/)

---

**感谢使用 TikTube！如有问题，请提交 Issue。**
