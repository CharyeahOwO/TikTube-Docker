<div align="center">
    <img src="img/logo.png" alt="TikTube Logo" width="200">
    <h1>TikTube</h1>
    <p>Video Danmaku Website | Docker One-Click Deploy</p>
    <p>
        <a href="README.md">🇨🇳 中文文档</a>
    </p>
</div>

---

## Quick Start

1. Clone the project:
```bash
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
```

2. Enter docker directory:
```bash
cd TikTube-Docker/docker
```

3. Copy environment config file:

**Linux/Mac OS:**
```bash
cp .env.example .env
```

**Windows:**
```bash
copy .env.example .env
```

4. Edit .env to change passwords, then build:
```bash
docker compose up -d --build
```

5. Visit `http://localhost:8080`, register with username `admin` to become administrator

---

## Features

- 🎬 Video upload with auto thumbnail generation
- 💬 Real-time danmaku (bullet comments) system
- 👥 User registration/login/TOTP 2FA
- ❤️ Like, favorite, comment, subscribe
- 🤖 AI content moderation (optional)
- 🗄️ S3-compatible object storage support

---

## Tech Stack

| Backend | Frontend | Database | Others |
|---------|----------|----------|--------|
| Spring Boot 3.4 | Vue 3.5 + Vuetify | MySQL 8 | Docker |
| MyBatis-Plus | Vite + ArtPlayer | Redis 7 | FFmpeg |

---

## Project Structure

```
├── docker/
│   ├── Dockerfile          # Multi-stage build
│   ├── docker-compose.yml  # Service orchestration
│   └── .env.example        # Environment template
├── TikTube/                # Backend Spring Boot
├── TikTubeWeb/             # Frontend Vue
└── tik_tube.sql            # Database script
```

---

## Troubleshooting

### Docker image pull failed

Configure registry mirror (Linux):
```bash
sudo tee /etc/docker/daemon.json <<EOF
{"registry-mirrors": ["https://docker.1panel.live"]}
EOF
```

Restart Docker service:
```bash
sudo systemctl restart docker
```

**Windows:** Docker Desktop → Settings → Docker Engine → Add `registry-mirrors`

### Large file upload failed

- Videos over 2GB: compress first
- MKV format: convert to MP4

---

## Common Commands

Check status:
```bash
docker compose ps
```

View logs:
```bash
docker compose logs -f
```

Stop services:
```bash
docker compose down
```

Restart services:
```bash
docker compose restart
```

---

## Acknowledgements

Based on [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube) with Docker optimization.

---

## License

[MIT License](LICENSE)
