<div align="center">
    <img src="img/logo.png" alt="TikTube Logo" width="200">
    <h1>🎬 TikTube - Video Danmaku Website</h1>
    <p>A video website with bullet comments, Docker one-click deployment</p>
    <p>
        <a href="README.md">🇨🇳 中文文档</a>
    </p>
</div>

---

## 🚀 Quick Deployment (3 Steps)

### 1️⃣ Clone Repository

```bash
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
cd TikTube-Docker/docker
```

### 2️⃣ Configure Environment

```bash
# Linux/macOS
cp .env.example .env
vim .env  # Edit passwords

# Windows PowerShell
copy .env.example .env
notepad .env  # Edit passwords with Notepad
```

⚠️ **Must modify** passwords in `.env`:
```
MYSQL_ROOT_PASSWORD=your_mysql_password
REDIS_PASSWORD=your_redis_password
```

### 3️⃣ Start Services

```bash
docker compose up -d --build
```

> First build takes about 5-10 minutes

### ✅ Access Website

Open browser: **http://localhost:8080**

> 📌 **Tip**: Register with username `admin` to become administrator

---

## ❓ Troubleshooting

### Docker Image Pull Failed

If you see `failed to fetch anonymous token` error, it's a Docker Hub network issue.

**Solution**: Configure Docker registry mirror

<details>
<summary>📖 Click to expand configuration</summary>

#### Windows Docker Desktop
1. Right-click Docker icon in system tray → Settings
2. Select Docker Engine
3. Add configuration:
```json
{
  "registry-mirrors": [
    "https://docker.1panel.live",
    "https://dockerproxy.cn"
  ]
}
```
4. Click Apply & restart

#### Linux
```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": [
    "https://docker.1panel.live",
    "https://dockerproxy.cn"
  ]
}
EOF
sudo systemctl restart docker
```

</details>

### Video Upload Failed

- **Large files (2GB+)**: Compress video first
- **MKV format**: Convert to MP4 recommended

---

## 📁 Project Structure

```
TikTube-Docker/
├── docker/
│   ├── Dockerfile           # Multi-stage build
│   ├── docker-compose.yml   # Service orchestration
│   ├── .env.example         # Environment template
│   └── DOCKER_DEPLOY.md     # Detailed guide
├── TikTube/                  # Backend (Spring Boot)
├── TikTubeWeb/               # Frontend (Vue 3)
└── tik_tube.sql              # Database script
```

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|------------|
| Backend | Spring Boot 3.4, MyBatis-Plus |
| Frontend | Vue 3.5, Vuetify 3, Vite |
| Database | MySQL 8.0, Redis 7 |
| Video | JavaCV, FFmpeg |

---

## 📝 Changelog

### v1.3.0-docker

- 🐳 Docker multi-stage build
- ⚙️ JVM memory: 1GB → 5GB
- 🔧 JavaCV compatibility (Alpine → Debian)
- ✨ VideoTogether plugin

---

## 🙏 Acknowledgements

Based on [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube).

Thanks to **[PuZhiweizuishuai](https://github.com/PuZhiweizuishuai)** for creating this project!

---

## 📄 License

[MIT License](LICENSE)
