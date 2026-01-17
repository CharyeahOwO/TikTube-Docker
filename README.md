<div align="center">
    <img src="img/logo.png" alt="TikTube Logo" width="200">
    <h1>TikTube - 视频弹幕网站</h1>
    <p>一个能发弹幕的简单视频网站，支持 Docker 一键部署</p>
    <p>
        <a href="#-功能特性">功能特性</a> •
        <a href="#-快速开始">快速开始</a> •
        <a href="#-docker-部署">Docker 部署</a> •
        <a href="#-更新日志">更新日志</a>
    </p>
    <p>
        <a href="README_EN.md">🇺🇸 English</a>
    </p>
</div>

---

## 📖 项目简介

TikTube 是一个仿 YouTube 风格的视频网站，支持弹幕功能。后端基于 Spring Boot，前端基于 Vue 3 + Vuetify。

本仓库是基于 [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube) 原项目进行的 **Docker 化改造版本**，主要增加了：
- 🐳 完整的 Docker 多阶段构建支持
- 🔧 生产环境优化配置
- 📝 详细的中文部署文档

> **致谢**：感谢原作者 **[PuZhiweizuishuai](https://github.com/PuZhiweizuishuai)** 创建了这个优秀的开源项目！本仓库的所有改动均基于原项目进行。

---

## ✨ 功能特性

- 🎬 **视频投稿** - 支持上传视频、自动生成封面截图
- 💬 **弹幕系统** - 实时弹幕发送与展示
- 📺 **视频播放** - 基于 ArtPlayer 的高性能播放器
- 👥 **用户系统** - 注册、登录、TOTP 两步验证
- ❤️ **互动功能** - 点赞、收藏、评论、订阅
- 📊 **数据管理** - 播放历史、个人主页
- 🤖 **AI 审核** - 支持配置大模型自动内容审核
- 🗄️ **对象存储** - 支持 MinIO、Cloudflare R2 等 S3 兼容存储

---

## 🏗️ 技术栈

| 模块 | 技术 |
|------|------|
| 后端 | Spring Boot 3.4.4, MyBatis-Plus, JWT |
| 前端 | Vue 3.5, Vuetify 3, Vite, ArtPlayer |
| 数据库 | MySQL 8.0 |
| 缓存 | Redis 7 |
| 视频处理 | JavaCV, FFmpeg |
| 容器化 | Docker, Docker Compose |

---

## 🚀 快速开始

### 方式一：Docker 一键部署（推荐）

```bash
# 克隆仓库
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
cd TikTube-Docker/docker

# 修改环境变量
cp .env.example .env
nano .env  # 修改密码等配置

# 构建并启动
docker compose up -d --build

# 访问
open http://localhost:8080
```

详细教程请查看 [Docker 一键部署文档](docker/DOCKER_DEPLOY.md)

### 方式二：本地开发环境

**环境要求**：Java 17+, Node.js 20+, Maven 3.9+, MySQL 8.0+

```bash
# 导入数据库
mysql -u root -p < tik_tube.sql

# 启动后端
cd TikTube
mvn spring-boot:run

# 启动前端
cd TikTubeWeb
npm install && npm run dev

# 访问
open http://localhost:5173
```

---

## 🐳 Docker 部署

### 目录结构

```
docker/
├── Dockerfile              # 多阶段构建文件
├── docker-compose.yml      # 服务编排
├── .env.example            # 环境变量模板
└── DOCKER_DEPLOY.md        # 详细部署教程
```

### 快速命令

```bash
# 构建镜像
docker compose build

# 启动服务
docker compose up -d

# 查看日志
docker compose logs -f tiktube

# 停止服务
docker compose down
```

### 服务说明

| 服务 | 端口 | 说明 |
|------|------|------|
| tiktube | 8080 | 主应用 |
| mysql | 3306 | 数据库 |
| redis | 6379 | 缓存 |

---

## 📝 更新日志

### v1.3.0-docker (2026-01-18)

#### 🐳 Docker 化改造

**1. 多阶段 Dockerfile 构建**
- 阶段 1：Node.js 20 编译前端 Vue 项目
- 阶段 2：Maven 3.9 编译后端 Spring Boot 项目
- 阶段 3：JRE 17 运行环境（**从 Alpine 改为 Debian**，解决 JavaCV 兼容性）

**2. JVM 内存参数优化**
```diff
- JAVA_OPTS: "-Xms512m -Xmx1024m"
+ JAVA_OPTS: "-Xms2048m -Xmx5120m"
```
- 初始堆内存：512MB → 2GB
- 最大堆内存：1GB → 5GB
- 解决大文件（1GB+）视频处理时的 `OutOfMemoryError`

**3. 运行时镜像修改**
```diff
- FROM eclipse-temurin:17-jre-alpine  # musl libc
+ FROM eclipse-temurin:17-jre         # glibc
```
- 修复 JavaCV 视频处理时的 `SIGSEGV` 段错误
- 原因：Alpine 使用 musl libc，与 JavaCV native 库不兼容

**4. 前端增强**
- 添加 [VideoTogether](https://videotogether.github.io/) 一起看插件

**5. 数据库脚本修复**
- 修复 `tik_tube.sql` 中的语法错误（缺少分号、尾部逗号）

#### 📁 新增文件

| 文件 | 说明 |
|------|------|
| `docker/Dockerfile` | 多阶段构建文件 |
| `docker/docker-compose.yml` | 服务编排 |
| `docker/.env.example` | 环境变量模板 |
| `docker/DOCKER_DEPLOY.md` | 详细部署教程 |

---

## ⚠️ 已知限制

1. **大文件上传**：超过 2-3GB 的视频可能无法处理，建议先压缩
2. **MKV 格式**：部分 MKV 文件（特别是 FLAC 音轨）可能无法解析，建议转为 MP4

---

## 🙏 致谢

- **原项目作者**：[PuZhiweizuishuai](https://github.com/PuZhiweizuishuai) - 创建了 TikTube 这个优秀的开源视频网站项目
- **原项目地址**：[https://github.com/PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube)

本仓库基于原项目 v1.3.0 版本进行 Docker 化改造，所有核心功能归功于原作者。

---

## 📄 许可证

本项目遵循 [MIT License](LICENSE)

---

<div align="center">
    <p>如果这个项目对你有帮助，请给原项目一个 ⭐ Star！</p>
    <a href="https://github.com/PuZhiweizuishuai/TikTube">👉 原项目地址</a>
</div>
