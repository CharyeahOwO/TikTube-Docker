<div align="center">
    <img src="img/logo.png" alt="TikTube Logo" width="200">
    <h1>TikTube</h1>
    <p>视频弹幕网站 | Docker 一键部署</p>
    <p>
        <a href="README_EN.md">🇺🇸 English</a>
    </p>
</div>

---

## 快速开始

1. 克隆项目：
```bash
git clone https://github.com/CharyeahOwO/TikTube-Docker.git
```

2. 进入 docker 目录：
```bash
cd TikTube-Docker/docker
```

3. 复制环境变量配置文件：

**Linux/Mac OS:**
```bash
cp .env.example .env
```

**Windows:**
```bash
copy .env.example .env
```

4. 编辑 .env 修改密码后，开始构建：
```bash
docker compose up -d --build
```

5. 访问 `http://localhost:8080`，注册用户名 `admin` 成为管理员

---

## 功能特性

- 🎬 视频上传与自动封面生成
- 💬 实时弹幕系统
- 👥 用户注册/登录/TOTP 两步验证
- ❤️ 点赞、收藏、评论、订阅
- 🤖 AI 内容审核（可选）
- 🗄️ S3 兼容对象存储支持

---

## 技术栈

| 后端 | 前端 | 数据库 | 其他 |
|------|------|--------|------|
| Spring Boot 3.4 | Vue 3.5 + Vuetify | MySQL 8 | Docker |
| MyBatis-Plus | Vite + ArtPlayer | Redis 7 | FFmpeg |

---

## 项目结构

```
├── docker/
│   ├── Dockerfile          # 多阶段构建
│   ├── docker-compose.yml  # 服务编排
│   └── .env.example        # 环境变量模板
├── TikTube/                # 后端 Spring Boot
├── TikTubeWeb/             # 前端 Vue
└── tik_tube.sql            # 数据库脚本
```

---

## 常见问题

### Docker 拉取镜像失败

配置镜像加速（Linux）：
```bash
sudo tee /etc/docker/daemon.json <<EOF
{"registry-mirrors": ["https://docker.1panel.live"]}
EOF
```

重启 Docker 服务：
```bash
sudo systemctl restart docker
```

**Windows:** Docker Desktop → Settings → Docker Engine → 添加 `registry-mirrors`

### 大文件上传失败

- 超过 2GB 的视频建议先压缩
- MKV 格式建议转换为 MP4

---

## 常用命令

查看状态：
```bash
docker compose ps
```

查看日志：
```bash
docker compose logs -f
```

停止服务：
```bash
docker compose down
```

重启服务：
```bash
docker compose restart
```

---

## 致谢

基于 [PuZhiweizuishuai/TikTube](https://github.com/PuZhiweizuishuai/TikTube) 进行 Docker 化改造。

---

## 许可证

[MIT License](LICENSE)
