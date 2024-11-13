# 搭建 Nginx Proxy Manager （申请证书反向代理）

本教程将指导你在 VPS 上搭建 Nginx Proxy Manager。

## 前提条件

- 一台运行 Linux 的 VPS
- 已安装 SSH 客户端

## 第一步：安装 Docker

1. 使用 SSH 连接到你的 VPS。
2. 进入 [Docker 官网](https://docs.docker.com/compose/install/linux/) 选择适合你系统的 Docker Engine 版本。
3. 这里以debian系统示例，按以下步骤安装（逐行复制回车） Docker：

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

4. 安装 Docker：

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 第二步：安装 Portainer 面板

1. 拉取 Portainer 镜像：

```bash
docker pull portainer/portainer
```

2. 启动 Portainer 容器：

```bash
docker run -d --restart=always --name portainerUI -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer
```

3. 访问 Portainer 面板：在浏览器中输入 `http://<VPS_IP>:9000`。

## 第三步：安装 Nginx Proxy Manager

1. 访问 Nginx Proxy Manager 的 [官网](https://nginxproxymanager.com/guide/) 或 [中文版](https://github.com/xiaoxinpro/nginx-proxy-manager-zh)。
2. 获取安装代码（这里已经把源端口80:80改成880:80）：

```yaml
version: '3'
services:
  app:
    image: 'chishin/nginx-proxy-manager-zh:release'
    restart: always
    ports:
      - '880:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```

3. 在 Portainer 中新建容器，粘贴上述代码并启动。

## 第四步：访问 Nginx Proxy Manager

1. 访问 Nginx Proxy Manager 管理页面：在浏览器中输入 `http://<VPS_IP>:81`。

默认管理员信息:

Email:    admin@example.com
Password: changeme
使用这个默认用户登录后，系统会立即要求您修改详细信息和密码。
详情请查考GitHub官方 [中文版](https://github.com/xiaoxinpro/nginx-proxy-manager-zh)。

至此，你已经成功在 VPS 上搭建了 Nginx Proxy Manager。
