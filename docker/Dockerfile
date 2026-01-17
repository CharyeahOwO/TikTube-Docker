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
# 注意：使用 Debian 而非 Alpine，因为 JavaCV 需要 glibc
FROM eclipse-temurin:17-jre AS runtime

LABEL maintainer="TikTube Docker"
LABEL description="TikTube 视频网站"

WORKDIR /app

# 安装必要工具（用于视频处理的 FFmpeg）
RUN apt-get update && apt-get install -y --no-install-recommends \
  ffmpeg \
  && rm -rf /var/lib/apt/lists/*

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
