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

```bash
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
```

```bash
cd TikTube-Docker/docker
```

```bash
cp .env.example .env    # Edit .env to change passwords
```

```bash
docker compose up -d --build
```

Visit http://localhost:8080
> Register with username `admin` to become administrator

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

<details>
<summary><b>Docker image pull failed</b></summary>

Configure registry mirror:

```bash
# Linux
sudo tee /etc/docker/daemon.json <<EOF
{"registry-mirrors": ["https://docker.1panel.live"]}
EOF
```

```bash
sudo systemctl restart docker
```

Windows: Docker Desktop → Settings → Docker Engine → Add `registry-mirrors`

</details>

<details>
<summary><b>Large file upload failed</b></summary>

- Videos over 2GB: compress first
- MKV format: convert to MP4

</details>

---

## Common Commands

```bash
docker compose ps          # Check status
```

```bash
docker compose logs -f     # View logs
```

```bash
docker compose down        # Stop services
```

```bash
docker compose restart     # Restart services
```

---

## Acknowledgements

Based on [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube) with Docker optimization.

---

## License

[MIT License](LICENSE)
