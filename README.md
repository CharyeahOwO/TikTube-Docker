<div align="center">
    <img src="img/logo.png" alt="TikTube Logo" width="200">
    <h1>🎬 TikTube - 视频弹幕网站</h1>
    <p>一个支持弹幕的视频网站，Docker 一键部署</p>
    <p>
        <a href="README_EN.md">🇺🇸 English</a>
    </p>
</div>

---

# 🚀 部署教程

按照以下步骤操作，5 分钟内完成部署。

---

## 第 1 步：安装 Docker

如果你还没有安装 Docker，请先安装：

- **Windows**：下载 [Docker Desktop](https://www.docker.com/products/docker-desktop/)，安装后启动
- **macOS**：下载 [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- **Linux**：
```bash
curl -fsSL https://get.docker.com | sh
sudo systemctl start docker
```

验证安装成功：
```bash
docker --version
```

---

## 第 2 步：下载本项目

**方式 A：使用 Git**
```bash
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
cd TikTube-Docker/docker
```

**方式 B：直接下载 ZIP**
1. 点击页面上方绿色的 **Code** 按钮
2. 选择 **Download ZIP**
3. 解压后进入 `TikTube-Docker-main/docker` 文件夹

---

## 第 3 步：设置密码

打开 `docker` 文件夹，你会看到一个 `.env.example` 文件。

### Windows 用户：
```powershell
copy .env.example .env
notepad .env
```

### macOS/Linux 用户：
```bash
cp .env.example .env
nano .env   # 或 vim .env
```

### 修改 .env 文件

打开 `.env` 文件后，你会看到这些内容：

```bash
# MySQL 数据库密码 - 改成你自己的密码！
MYSQL_ROOT_PASSWORD=TikTube@2024!

# Redis 缓存密码 - 改成你自己的密码！
REDIS_PASSWORD=TikTube@Redis2024!
```

**直接修改这两个密码即可**，比如改成：
```bash
MYSQL_ROOT_PASSWORD=MySecurePass123
REDIS_PASSWORD=MyRedisPass456
```

> 💡 **说明**：这些密码是你自己设置的，Docker 启动时会自动使用这些密码创建 MySQL 和 Redis。不需要单独安装数据库，Docker 会帮你搞定！

修改完成后**保存文件**。

---

## 第 4 步：启动服务

在 `docker` 文件夹中打开终端/命令行，运行：

```bash
docker compose up -d --build
```

**第一次运行需要等待 5-10 分钟**（下载镜像 + 编译代码）

看到类似输出表示成功：
```
✔ Container tiktube-mysql   Started
✔ Container tiktube-redis   Started
✔ Container tiktube-app     Started
```

---

## 第 5 步：访问网站

打开浏览器，访问：

👉 **http://localhost:8080**

### 注册管理员账号

首次访问时：
1. 点击 **注册**
2. **用户名填写 `admin`**（这是关键！）
3. 完成注册

> ⚠️ 第一个用户名为 `admin` 的账号会自动成为管理员！

---

# ❓ 遇到问题？

## 问题 1：Docker 拉取镜像失败

错误信息：`failed to fetch anonymous token` 或 `timeout`

**原因**：国内无法访问 Docker Hub

**解决方法**：配置镜像加速

### Windows Docker Desktop
1. 右键系统托盘 Docker 图标 → **Settings**
2. 左侧选择 **Docker Engine**
3. 在 JSON 配置中添加（注意保留原有内容）：
```json
{
  "registry-mirrors": [
    "https://docker.1panel.live",
    "https://dockerproxy.cn"
  ]
}
```
4. 点击 **Apply & restart**
5. 重新运行 `docker compose up -d --build`

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
# 重新运行
docker compose up -d --build
```

---

## 问题 2：端口被占用

错误信息：`port 8080 already in use`

**解决方法**：修改 `docker-compose.yml` 中的端口：
```yaml
ports:
  - "9000:8080"  # 将 8080 改成 9000 或其他端口
```

然后访问 http://localhost:9000

---

## 问题 3：视频上传失败

- **大文件（超过 2GB）**：建议先压缩视频
- **MKV 格式**：建议转换为 MP4

---

# 📁 项目说明

| 目录/文件 | 说明 |
|-----------|------|
| `docker/` | Docker 配置文件 |
| `TikTube/` | 后端代码 (Spring Boot) |
| `TikTubeWeb/` | 前端代码 (Vue 3) |
| `tik_tube.sql` | 数据库脚本（自动执行） |

---

# 🛠️ 常用命令

```bash
# 查看运行状态
docker compose ps

# 查看日志
docker compose logs -f tiktube

# 停止服务
docker compose down

# 重启服务
docker compose restart
```

---

# 🙏 致谢

本项目基于 [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube) 进行 Docker 化改造。

感谢原作者 **[PuZhiweizuishuai](https://github.com/PuZhiweizuishuai)** 创建了这个优秀的开源项目！

---

# 📄 许可证

[MIT License](LICENSE)
