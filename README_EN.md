<div align="center">
    <img src="img/logo.png" alt="TikTube Logo" width="200">
    <h1>🎬 TikTube - Video Danmaku Website</h1>
    <p>A video website with bullet comments, Docker one-click deployment</p>
    <p>
        <a href="README.md">🇨🇳 中文文档</a>
    </p>
</div>

---

# 🚀 Deployment Tutorial (Beginner Friendly)

Follow these steps and deploy in 5 minutes.

---

## Step 1: Install Docker

If you haven't installed Docker yet:

- **Windows**: Download [Docker Desktop](https://www.docker.com/products/docker-desktop/), install and start it
- **macOS**: Download [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- **Linux**:
```bash
curl -fsSL https://get.docker.com | sh
sudo systemctl start docker
```

Verify installation:
```bash
docker --version
```

---

## Step 2: Download This Project

**Option A: Using Git**
```bash
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
cd TikTube-Docker/docker
```

**Option B: Download ZIP**
1. Click the green **Code** button above
2. Select **Download ZIP**
3. Extract and navigate to `TikTube-Docker-main/docker` folder

---

## Step 3: Set Passwords

Open the `docker` folder, you'll find a `.env.example` file.

### Windows Users:
```powershell
copy .env.example .env
notepad .env
```

### macOS/Linux Users:
```bash
cp .env.example .env
nano .env   # or vim .env
```

### Edit .env File

You'll see these contents:

```bash
# MySQL database password - change to your own password!
MYSQL_ROOT_PASSWORD=TikTube@2024!

# Redis cache password - change to your own password!
REDIS_PASSWORD=TikTube@Redis2024!
```

**Simply change these two passwords**, for example:
```bash
MYSQL_ROOT_PASSWORD=MySecurePass123
REDIS_PASSWORD=MyRedisPass456
```

> 💡 **Note**: These passwords are set by YOU. Docker will automatically use these passwords to create MySQL and Redis. No need to install databases separately - Docker handles everything!

**Save the file** after editing.

---

## Step 4: Start Services

Open terminal in the `docker` folder and run:

```bash
docker compose up -d --build
```

**First run takes 5-10 minutes** (downloading images + compiling code)

You'll see output like this when successful:
```
✔ Container tiktube-mysql   Started
✔ Container tiktube-redis   Started
✔ Container tiktube-app     Started
```

---

## Step 5: Access Website

Open your browser and visit:

👉 **http://localhost:8080**

### Register Admin Account

On first visit:
1. Click **Register**
2. **Username: `admin`** (This is important!)
3. Complete registration

> ⚠️ The first account with username `admin` automatically becomes administrator!

---

# ❓ Troubleshooting

## Issue 1: Docker Image Pull Failed

Error: `failed to fetch anonymous token` or `timeout`

**Cause**: Cannot access Docker Hub (common in China)

**Solution**: Configure registry mirror

### Windows Docker Desktop
1. Right-click Docker icon in system tray → **Settings**
2. Select **Docker Engine** on the left
3. Add to JSON config:
```json
{
  "registry-mirrors": [
    "https://docker.1panel.live",
    "https://dockerproxy.cn"
  ]
}
```
4. Click **Apply & restart**
5. Run `docker compose up -d --build` again

### Linux
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
# Run again
docker compose up -d --build
```

---

## Issue 2: Port Already in Use

Error: `port 8080 already in use`

**Solution**: Change port in `docker-compose.yml`:
```yaml
ports:
  - "9000:8080"  # Change 8080 to 9000 or another port
```

Then visit http://localhost:9000

---

## Issue 3: Video Upload Failed

- **Large files (over 2GB)**: Compress video first
- **MKV format**: Convert to MP4

---

# 📁 Project Structure

| Directory/File | Description |
|----------------|-------------|
| `docker/` | Docker configuration files |
| `TikTube/` | Backend code (Spring Boot) |
| `TikTubeWeb/` | Frontend code (Vue 3) |
| `tik_tube.sql` | Database script (auto-executed) |

---

# 🛠️ Common Commands

```bash
# Check status
docker compose ps

# View logs
docker compose logs -f tiktube

# Stop services
docker compose down

# Restart services
docker compose restart
```

---

# 🙏 Acknowledgements

This project is based on [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube) with Docker optimization.

Thanks to **[PuZhiweizuishuai](https://github.com/PuZhiweizuishuai)** for creating this excellent open-source project!

---

# 📄 License

[MIT License](LICENSE)
