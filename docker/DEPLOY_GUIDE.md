# TikTube Docker 全自动构建部署教程

> 本教程教你如何在 1Panel 面板中完全使用 Docker 部署 TikTube 视频网站，无需本地安装 JDK、Maven、Node.js。

## 📋 目录

1. [项目分析](#1-项目分析)
2. [Dockerfile 说明](#2-dockerfile-多阶段构建说明)
3. [docker-compose.yml 说明](#3-docker-composeyml-说明)
4. [1Panel 部署步骤](#4-1panel-部署步骤)
5. [数据库初始化](#5-数据库初始化)
6. [常见问题](#6-常见问题)

---

## 1. 项目分析

### 项目结构
```
TikTube-1.3.0/
├── TikTube/          # 后端 Spring Boot 项目
├── TikTubeWeb/       # 前端 Vue 项目
├── tik_tube.sql      # 数据库初始化脚本
└── docker/           # Docker 部署文件（需创建）
    ├── Dockerfile
    ├── docker-compose.yml
    └── .env
```

### 构建版本要求
| 组件 | 版本 |
|------|------|
| Java | 17 |
| Node.js | 20+ |
| Maven | 3.9+ |
| MySQL | 8.0+ |
| Redis | 可选 |

---

## 2. Dockerfile 多阶段构建说明

在 `docker/` 目录下创建 `Dockerfile`：

```dockerfile
# ===========================================
# TikTube Docker 多阶段构建
# 第一阶段: 前端构建 (Node.js)
# 第二阶段: 后端构建 (Maven)
# 第三阶段: 运行环境 (JRE)
# ===========================================

# =========== 阶段1: 前端构建 ===========
FROM node:20-alpine AS frontend-builder

WORKDIR /app/frontend

# 复制前端项目
COPY TikTubeWeb/package*.json ./

# 安装依赖
RUN npm config set registry https://registry.npmmirror.com && \
    npm ci --only=production=false

# 复制源码并构建
COPY TikTubeWeb/ ./
RUN npm run build

# =========== 阶段2: 后端构建 ===========
FROM maven:3.9-eclipse-temurin-17 AS backend-builder

WORKDIR /app/backend

# 配置 Maven 阿里云镜像加速
RUN mkdir -p /root/.m2 && \
    echo '<?xml version="1.0" encoding="UTF-8"?>\n\
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"\n\
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"\n\
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0\n\
  http://maven.apache.org/xsd/settings-1.0.0.xsd">\n\
  <mirrors>\n\
    <mirror>\n\
      <id>aliyun</id>\n\
      <mirrorOf>central</mirrorOf>\n\
      <name>Aliyun Maven</name>\n\
      <url>https://maven.aliyun.com/repository/central</url>\n\
    </mirror>\n\
  </mirrors>\n\
</settings>' > /root/.m2/settings.xml

# 复制 pom.xml 并下载依赖（利用 Docker 缓存）
COPY TikTube/pom.xml ./
RUN mvn dependency:go-offline -B

# 复制后端源码
COPY TikTube/ ./

# 从前端阶段复制构建产物到静态资源目录
COPY --from=frontend-builder /app/frontend/dist/ ./src/main/resources/static/

# 打包（跳过测试）
RUN mvn clean package -DskipTests -B

# =========== 阶段3: 运行环境 ===========
FROM eclipse-temurin:17-jre-alpine AS runtime

LABEL maintainer="TikTube Docker"
LABEL description="TikTube 视频网站"

WORKDIR /app

# 安装必要工具（用于视频处理的 FFmpeg）
RUN apk add --no-cache ffmpeg

# 从构建阶段复制 JAR 包
COPY --from=backend-builder /app/backend/target/tiktube-*.jar app.jar

# 创建上传目录
RUN mkdir -p /app/uploads /app/log

# 暴露端口
EXPOSE 8080

# JVM 参数配置
ENV JAVA_OPTS="-Xms512m -Xmx1024m -XX:+UseG1GC"

# 启动命令
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /app/app.jar"]
```

### 阶段说明

| 阶段 | 基础镜像 | 作用 |
|------|----------|------|
| frontend-builder | node:20-alpine | 编译 Vue 前端，生成 dist |
| backend-builder | maven:3.9-eclipse-temurin-17 | 集成前端并编译 Spring Boot JAR |
| runtime | eclipse-temurin:17-jre-alpine | 仅保留 JAR，运行应用 |

---

## 3. docker-compose.yml 说明

在 `docker/` 目录下创建 `docker-compose.yml`：

```yaml
# ===========================================
# TikTube Docker Compose 编排
# 适用于 1Panel 部署
# ===========================================

services:
  # =========== MySQL 数据库 ===========
  mysql:
    image: mysql:8.0
    container_name: tiktube-mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      TZ: Asia/Shanghai
    command:
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
      - --max_connections=200
    volumes:
      # 数据持久化
      - ./data/mysql:/var/lib/mysql
      # 初始化 SQL 脚本（首次启动自动执行）
      - ../tik_tube.sql:/docker-entrypoint-initdb.d/init.sql:ro
    ports:
      - "3306:3306"
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-uroot", "-p${MYSQL_ROOT_PASSWORD}"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - tiktube-network

  # =========== Redis 缓存 ===========
  redis:
    image: redis:7-alpine
    container_name: tiktube-redis
    restart: unless-stopped
    command: redis-server --requirepass ${REDIS_PASSWORD} --appendonly yes
    volumes:
      - ./data/redis:/data
    ports:
      - "6379:6379"
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "${REDIS_PASSWORD}", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - tiktube-network

  # =========== TikTube 主应用 ===========
  tiktube:
    build:
      context: ..
      dockerfile: docker/Dockerfile
    image: tiktube:1.3.0
    container_name: tiktube-app
    restart: unless-stopped
    depends_on:
      mysql:
        condition: service_healthy
      redis:
        condition: service_healthy
    environment:
      # Spring Boot 数据源配置
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/${MYSQL_DATABASE}?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      # Redis 配置
      SPRING_DATA_REDIS_HOST: redis
      SPRING_DATA_REDIS_PORT: 6379
      SPRING_DATA_REDIS_PASSWORD: ${REDIS_PASSWORD}
      # TikTube 应用配置
      TIKTUBE_OPEN_REDIS: ${TIKTUBE_OPEN_REDIS}
      TIKTUBE_IS_THE_PROXY_CONFIGURED: ${TIKTUBE_PROXY_CONFIGURED}
      # JVM 配置
      JAVA_OPTS: "-Xms512m -Xmx1024m"
      # 时区
      TZ: Asia/Shanghai
    volumes:
      # 上传文件持久化
      - ./data/uploads:/app/uploads
      # 日志持久化
      - ./data/logs:/app/log
    ports:
      - "8080:8080"
    networks:
      - tiktube-network

networks:
  tiktube-network:
    driver: bridge
```

### 环境变量配置 (.env)

创建 `.env` 文件：

```env
# ===========================================
# TikTube 环境变量配置
# 请根据实际情况修改以下配置
# ===========================================

# ===== MySQL 配置 =====
MYSQL_ROOT_PASSWORD=TikTube@2024!
MYSQL_DATABASE=tik_tube

# ===== Redis 配置 =====
REDIS_PASSWORD=TikTube@Redis2024!

# ===== TikTube 应用配置 =====
# 是否启用 Redis 缓存 (true/false)
TIKTUBE_OPEN_REDIS=true

# 是否配置代理 (如果前置有 nginx 等代理，设为 true)
TIKTUBE_PROXY_CONFIGURED=false
```

### 环境变量覆盖原理

Spring Boot 支持通过环境变量覆盖 `application.yml` 配置：

| 原配置 | 环境变量 |
|--------|----------|
| `spring.datasource.url` | `SPRING_DATASOURCE_URL` |
| `spring.datasource.username` | `SPRING_DATASOURCE_USERNAME` |
| `spring.datasource.password` | `SPRING_DATASOURCE_PASSWORD` |
| `spring.data.redis.host` | `SPRING_DATA_REDIS_HOST` |
| `spring.data.redis.password` | `SPRING_DATA_REDIS_PASSWORD` |
| `tiktube.open-redis` | `TIKTUBE_OPEN_REDIS` |

> **规则**：将 `.` 替换为 `_`，小写改大写，`-` 改为 `_`

---

## 4. 1Panel 部署步骤

### 步骤 1：准备文件结构

在 1Panel 文件管理中，进入默认 compose 目录（通常为 `/opt/1panel/docker/compose/`），创建 `tiktube` 目录：

```
/opt/1panel/docker/compose/tiktube/
├── docker-compose.yml
├── .env
├── Dockerfile
├── TikTube/            # 复制后端源码
├── TikTubeWeb/         # 复制前端源码
└── tik_tube.sql        # 复制数据库脚本
```

或者直接使用项目目录结构：

```
/home/muling/Downloads/tik/TikTube-1.3.0/
├── docker/
│   ├── docker-compose.yml
│   ├── .env
│   └── Dockerfile
├── TikTube/
├── TikTubeWeb/
└── tik_tube.sql
```

### 步骤 2：修改环境变量

**⚠️ 重要**：编辑 `.env` 文件，修改默认密码：

```env
MYSQL_ROOT_PASSWORD=你的数据库密码
REDIS_PASSWORD=你的Redis密码
```

### 步骤 3：1Panel 创建编排

1. 登录 1Panel 面板
2. 进入 **容器** → **编排**
3. 点击 **创建编排**
4. 选择 **路径** 模式
5. 填写路径：`/home/muling/Downloads/tik/TikTube-1.3.0/docker`
6. 编排名称：`tiktube`
7. 点击 **确认** 开始构建

### 步骤 4：等待构建完成

首次构建需要：
- 下载 Docker 镜像
- 下载 npm 依赖
- 下载 Maven 依赖（约 1-2GB）
- 编译前后端

**预计时间：5-15 分钟**（取决于网络速度）

### 步骤 5：访问应用

构建完成后，访问：`http://你的服务器IP:8080`

---

## 5. 数据库初始化

### 自动初始化

`docker-compose.yml` 已配置 MySQL 容器启动时自动执行 `tik_tube.sql` 脚本：

```yaml
volumes:
  - ../tik_tube.sql:/docker-entrypoint-initdb.d/init.sql:ro
```

> MySQL 官方镜像会在首次启动时执行 `/docker-entrypoint-initdb.d/` 目录下的所有 `.sql` 文件。

### 手动导入（如果自动失败）

如果需要手动导入：

```bash
# 进入 MySQL 容器
docker exec -it tiktube-mysql bash

# 登录 MySQL
mysql -uroot -p你的密码

# 导入数据库
source /docker-entrypoint-initdb.d/init.sql
```

或在 1Panel 中：

1. 进入 **数据库** → **MySQL**
2. 找到 `tiktube-mysql` 容器对应的数据库
3. 使用 **导入** 功能上传 `tik_tube.sql`

---

## 6. 常见问题

### Q1: 构建失败，Maven 下载超时

**解决方案**：Dockerfile 已配置阿里云 Maven 镜像，如仍失败可能是网络问题，重试即可。

### Q2: 无法连接数据库

**检查**：
```bash
# 查看容器状态
docker compose ps

# 查看 MySQL 日志
docker logs tiktube-mysql
```

### Q3: 如何成为管理员？

**方法**：首次注册时，使用 `admin` 作为用户名，该用户会自动成为管理员。

### Q4: 上传的视频存储在哪里？

**位置**：`./data/uploads/` 目录（已配置持久化挂载）

### Q5: 如何查看应用日志？

```bash
# 方法1：Docker 日志
docker logs -f tiktube-app

# 方法2：文件日志
cat ./data/logs/spring.log
```

### Q6: 如何修改端口号？

编辑 `docker-compose.yml`：

```yaml
tiktube:
  ports:
    - "自定义端口:8080"
```

### Q7: 关闭服务前的注意事项

项目 README 提醒：**关闭服务器之前请先到管理后台同步缓存数据，避免数据丢失！**

---

## 🎉 完成！

部署成功后，你可以：

1. 访问 `http://你的服务器IP:8080`
2. 注册用户名为 `admin` 的账号成为管理员
3. 开始上传和观看视频！

如有问题，可查看项目 GitHub：https://github.com/PuZhiweizuishuai/TikTube
