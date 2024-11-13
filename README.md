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

## 第五步：申请SSL证书

1. 登入WEB界面后，找到SSL进入，点击添加SSL证书按钮，选择添加Let's Encrypt证书

2. 域名 如：*.lsdong.com lsdong.com 两个都填上（填入实际的域名），概括所有后缀为lsdong.com的二级域名
  当然在这一步之前需要在自己申请的服务商里设置域名解析，IP指向此安装了NPM服务器的主机IP。

3. 选择 使用DNS认证 
   如：aliyun ,选择后填写秘钥
   dns_aliyun_access_key = （这里填写ID）
   dns_aliyun_access_key_secret = （这里填写Secret）
   保存等待，SSL证书申请完成！

## 第六步：添加代理服务
1.  仪表盘里点击代理服务按钮，进入找到添加代理服务，点击

2.  域名填写自己解析了的域名，二级部分根据自己需求填写
    如：nihao.lsdong.com，
        haode.lsdong.com  (nihao和haode这些可以随意更改，记得住就行)

3.  转发主机填写要去往的域名或者ip，端口要正确，勾选阻止常见漏洞

4.  点击SSL页面，选择刚刚申请了的SSL证书，下面4个选项全勾上
   点击保存，代理设置完成

