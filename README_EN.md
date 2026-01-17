<div align="center">
    <img src="img/logo.png" alt="TikTube Logo" width="200">
    <h1>TikTube - Video Danmaku Website</h1>
    <p>A simple video website with bullet comments (danmaku), supports Docker one-click deployment</p>
    <p>
        <a href="#-features">Features</a> •
        <a href="#-quick-start">Quick Start</a> •
        <a href="#-docker-deployment">Docker Deployment</a> •
        <a href="#-changelog">Changelog</a>
    </p>
    <p>
        <a href="README.md">🇨🇳 中文文档</a>
    </p>
</div>

---

## 📖 Introduction

TikTube is a YouTube-style video website with danmaku (bullet comments) support. The backend is built with Spring Boot, and the frontend is built with Vue 3 + Vuetify.

This repository is a **Docker-optimized version** based on [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube), featuring:
- 🐳 Complete Docker multi-stage build support
- 🔧 Production environment optimizations
- 📝 Detailed deployment documentation

> **Acknowledgement**: Special thanks to **[PuZhiweizuishuai](https://github.com/PuZhiweizuishuai)** for creating this excellent open-source project! All modifications in this repository are based on the original project.

---

## ✨ Features

- 🎬 **Video Upload** - Upload videos with automatic thumbnail generation
- 💬 **Danmaku System** - Real-time bullet comments display
- 📺 **Video Player** - High-performance player based on ArtPlayer
- 👥 **User System** - Registration, login, TOTP two-factor authentication
- ❤️ **Interactions** - Like, favorite, comment, subscribe
- 📊 **Data Management** - Watch history, personal homepage
- 🤖 **AI Moderation** - Support for LLM-based content moderation
- 🗄️ **Object Storage** - Support MinIO, Cloudflare R2, and S3-compatible storage

---

## 🏗️ Tech Stack

| Module | Technology |
|--------|------------|
| Backend | Spring Boot 3.4.4, MyBatis-Plus, JWT |
| Frontend | Vue 3.5, Vuetify 3, Vite, ArtPlayer |
| Database | MySQL 8.0 |
| Cache | Redis 7 |
| Video Processing | JavaCV, FFmpeg |
| Containerization | Docker, Docker Compose |

---

## 🚀 Quick Start

### Option 1: Docker One-Click Deployment (Recommended)

```bash
# Clone repository
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
cd TikTube-Docker/docker

# Configure environment variables
cp .env.example .env
nano .env  # Modify passwords and settings

# Build and start
docker compose up -d --build

# Access
open http://localhost:8080
```

For detailed instructions, see [Docker Deployment Guide](docker/DOCKER_DEPLOY.md)

### Option 2: Local Development

**Requirements**: Java 17+, Node.js 20+, Maven 3.9+, MySQL 8.0+

```bash
# Import database
mysql -u root -p < tik_tube.sql

# Start backend
cd TikTube
mvn spring-boot:run

# Start frontend
cd TikTubeWeb
npm install && npm run dev

# Access
open http://localhost:5173
```

---

## 🐳 Docker Deployment

### Directory Structure

```
docker/
├── Dockerfile              # Multi-stage build file
├── docker-compose.yml      # Service orchestration
├── .env.example            # Environment variables template
└── DOCKER_DEPLOY.md        # Detailed deployment guide
```

### Quick Commands

```bash
# Build image
docker compose build

# Start services
docker compose up -d

# View logs
docker compose logs -f tiktube

# Stop services
docker compose down
```

### Services

| Service | Port | Description |
|---------|------|-------------|
| tiktube | 8080 | Main application |
| mysql | 3306 | Database |
| redis | 6379 | Cache |

---

## 📝 Changelog

### v1.3.0-docker (2026-01-18)

#### 🐳 Docker Optimization

**1. Multi-stage Dockerfile Build**
- Stage 1: Node.js 20 compiles Vue frontend
- Stage 2: Maven 3.9 compiles Spring Boot backend
- Stage 3: JRE 17 runtime (**Changed from Alpine to Debian** for JavaCV compatibility)

**2. JVM Memory Optimization**
```diff
- JAVA_OPTS: "-Xms512m -Xmx1024m"
+ JAVA_OPTS: "-Xms2048m -Xmx5120m"
```
- Initial heap: 512MB → 2GB
- Max heap: 1GB → 5GB
- Fixes `OutOfMemoryError` when processing large video files (1GB+)

**3. Runtime Image Change**
```diff
- FROM eclipse-temurin:17-jre-alpine  # musl libc
+ FROM eclipse-temurin:17-jre         # glibc
```
- Fixes `SIGSEGV` crash during video processing with JavaCV
- Reason: Alpine uses musl libc, which is incompatible with JavaCV native libraries

**4. Frontend Enhancement**
- Added [VideoTogether](https://videotogether.github.io/) watch-together plugin

**5. Database Script Fix**
- Fixed syntax errors in `tik_tube.sql` (missing semicolons, trailing commas)

#### 📁 New Files

| File | Description |
|------|-------------|
| `docker/Dockerfile` | Multi-stage build file |
| `docker/docker-compose.yml` | Service orchestration |
| `docker/.env.example` | Environment variables template |
| `docker/DOCKER_DEPLOY.md` | Detailed deployment guide |

---

## ⚠️ Known Limitations

1. **Large File Upload**: Videos larger than 2-3GB may fail to process. Consider compressing first.
2. **MKV Format**: Some MKV files (especially with FLAC audio) may fail to parse. Convert to MP4 recommended.

---

## 🙏 Acknowledgements

- **Original Author**: [PuZhiweizuishuai](https://github.com/PuZhiweizuishuai) - Created the excellent TikTube video website project
- **Original Repository**: [https://github.com/PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube)

This repository is a Docker-optimized version based on v1.3.0. All core functionality credits go to the original author.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE)

---

<div align="center">
    <p>If this project helps you, please give the original project a ⭐ Star!</p>
    <a href="https://github.com/PuZhiweizuishuai/TikTube">👉 Original Project</a>
</div>
