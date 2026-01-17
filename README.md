<div align="center">
    <img src="img/logo.png" alt="TikTube Logo" width="200">
    <h1>🎬 TikTube - 视频弹幕网站</h1>
    <p>一个支持弹幕的视频网站，Docker 一键部署</p>
    <p>
        <a href="README_EN.md">🇺🇸 English</a>
    </p>
</div>

---

## 🚀 快速部署（3 步完成）

### 1️⃣ 克隆仓库

```bash
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
cd TikTube-Docker/docker
```

### 2️⃣ 配置环境变量

```bash
# Linux/macOS
cp .env.example .env
vim .env  # 或使用任意文本编辑器修改密码

# Windows PowerShell
copy .env.example .env
notepad .env  # 用记事本编辑，修改密码
```

⚠️ **必须修改** `.env` 中的密码：
```
MYSQL_ROOT_PASSWORD=你的MySQL密码
REDIS_PASSWORD=你的Redis密码
```

### 3️⃣ 启动服务

```bash
docker compose up -d --build
```

> 首次构建约需 5-10 分钟，请耐心等待

### ✅ 访问网站

打开浏览器访问：**http://localhost:8080**

> 📌 **提示**：首次注册用户名 `admin` 的账号将自动成为管理员

---

## ❓ 常见问题

### Docker 拉取镜像失败

如果出现 `failed to fetch anonymous token` 错误，这是 Docker Hub 网络问题。

**解决方案**：配置 Docker 镜像加速

<details>
<summary>📖 点击展开配置方法</summary>

#### Windows Docker Desktop
1. 右键点击系统托盘 Docker 图标 → Settings
2. 选择 Docker Engine
3. 添加以下配置：
```json
{
  "registry-mirrors": [
    "https://docker.1panel.live",
    "https://dockerproxy.cn"
  ]
}
```
4. 点击 Apply & restart

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

### 视频上传失败

- **大文件（2GB+）**：建议先压缩视频
- **MKV 格式**：建议转换为 MP4

---

## 📁 项目结构

```
TikTube-Docker/
├── docker/
│   ├── Dockerfile           # 多阶段构建
│   ├── docker-compose.yml   # 服务编排
│   ├── .env.example         # 环境变量模板
│   └── DOCKER_DEPLOY.md     # 详细部署教程
├── TikTube/                  # 后端 (Spring Boot)
├── TikTubeWeb/               # 前端 (Vue 3)
└── tik_tube.sql              # 数据库脚本
```

---

## 🛠️ 技术栈

| 组件 | 技术 |
|------|------|
| 后端 | Spring Boot 3.4, MyBatis-Plus |
| 前端 | Vue 3.5, Vuetify 3, Vite |
| 数据库 | MySQL 8.0, Redis 7 |
| 视频处理 | JavaCV, FFmpeg |

---

## 📝 更新日志

### v1.3.0-docker

- 🐳 添加 Docker 多阶段构建
- ⚙️ JVM 内存优化：1GB → 5GB
- 🔧 修复 JavaCV 兼容性（Alpine → Debian）
- ✨ 添加 VideoTogether 一起看插件

---

## 🙏 致谢

本项目基于 [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube) 进行 Docker 化改造。

感谢原作者 **[PuZhiweizuishuai](https://github.com/PuZhiweizuishuai)** 创建了这个优秀的开源项目！

---

## 📄 许可证

[MIT License](LICENSE)
