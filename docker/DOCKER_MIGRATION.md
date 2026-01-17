# Docker 与 containerd 数据目录迁移记录

> 记录于 2026-01-17，将 Docker 和 containerd 数据从系统盘迁移到 /home 分区

## 迁移概述

| 项目 | 迁移前路径 | 迁移后路径 | 释放空间 |
|------|-----------|-----------|---------|
| Docker 数据 | `/var/lib/docker` | `/home/docker` | ~2.4G |
| containerd 数据 | `/var/lib/containerd` | `/home/containerd` | ~16G |

**系统盘使用率：83% → 47%**

---

## 一、Docker 数据迁移

### 1. 停止 Docker 服务
```bash
sudo systemctl stop docker docker.socket
```

### 2. 复制数据到新位置
```bash
sudo cp -a /var/lib/docker /home/docker
```

### 3. 配置 Docker 使用新路径
```bash
sudo mkdir -p /etc/docker
echo '{"data-root": "/home/docker"}' | sudo tee /etc/docker/daemon.json
```

### 4. 启动 Docker 并验证
```bash
sudo systemctl start docker
docker info | grep "Docker Root Dir"
# 应显示: Docker Root Dir: /home/docker
```

### 5. 删除旧数据（可选，确认无误后）
```bash
sudo rm -rf /var/lib/docker
```

---

## 二、containerd 数据迁移

### 1. 停止服务
```bash
sudo systemctl stop docker docker.socket containerd
```

### 2. 复制数据到新位置
```bash
sudo cp -a /var/lib/containerd /home/containerd
```

### 3. 生成并修改配置文件
```bash
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml > /dev/null

# 修改 root 路径
sudo sed -i "s|root = '/var/lib/containerd'|root = '/home/containerd'|" /etc/containerd/config.toml

# 验证修改
grep "root = " /etc/containerd/config.toml
# 应显示: root = '/home/containerd'
```

### 4. 启动服务
```bash
sudo systemctl start containerd docker
```

### 5. 删除旧数据（确认无误后）
```bash
sudo rm -rf /var/lib/containerd
```

---

## 三、恢复容器服务

如果容器没有自动启动，手动启动：

```bash
cd /home/muling/Downloads/tik/TikTube-1.3.0/docker
sudo docker compose up -d

# 樱花穿透（如果有）
sudo docker start sakura-frp
```

---

## 四、配置文件备份

### /etc/docker/daemon.json
```json
{"data-root": "/home/docker"}
```

### /etc/containerd/config.toml 关键行
```toml
root = '/home/containerd'
```

---

## 五、故障排除

### 问题1：Docker 启动失败
```bash
# 检查日志
sudo journalctl -u docker -n 50

# 检查配置文件语法
cat /etc/docker/daemon.json | jq .
```

### 问题2：containerd 启动失败
```bash
# 检查日志
sudo journalctl -u containerd -n 50

# 验证配置文件
containerd config validate /etc/containerd/config.toml
```

### 问题3：容器丢失
迁移后容器应该保留。如果丢失，检查数据是否正确复制：
```bash
ls -la /home/docker/containers/
ls -la /home/containerd/
```

### 问题4：系统更新后配置被重置
Arch Linux 更新 docker/containerd 包时可能重置配置，需重新添加：
```bash
# Docker
echo '{"data-root": "/home/docker"}' | sudo tee /etc/docker/daemon.json

# containerd - 编辑 /etc/containerd/config.toml
# 将 root = '/var/lib/containerd' 改为 root = '/home/containerd'
```

---

## 六、当前系统状态

```
Docker Root Dir: /home/docker
containerd Root: /home/containerd

运行中的容器：
- tiktube-app (TikTube 应用)
- tiktube-mysql (MySQL 8.0)
- tiktube-redis (Redis 7)
- sakura-frp (樱花内网穿透)

公网访问地址：http://frp-six.com:50894
```
