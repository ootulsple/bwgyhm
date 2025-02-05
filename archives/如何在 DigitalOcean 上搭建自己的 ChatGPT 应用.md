# 如何在 DigitalOcean 上搭建自己的 ChatGPT 应用

本教程参考了多个开源项目，使用了 [ChatGPT-web](https://github.com/Chanzhaoyu/chatgpt-web)，并通过 DigitalOcean 服务器实现了部署。全程无需翻墙，适合个人使用。

## 主要费用概览
- **DigitalOcean 服务器**：4 美元/月，新用户注册可获得 200 美元，有效期 2 个月。
- **WildCard 开卡费用**：15 美元。
- **OpenAI Token 费用**：每 10 万个 Token 收费 4 美分，约相当于 5 万个汉字。

## 先决条件
1. **DigitalOcean 账号**：用于创建和管理服务器。
2. **OpenAI 账号**：用于获取 API Key。
   
👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)

OpenAI 仅支持信用卡支付，且不接受中国信用卡。此外，创建 API Key 时需验证手机号，也不支持中国手机号。通过 WildCard，可以一站式完成账号注册、手机验证和开卡服务，开卡费 15 美元，充值费率 3%。按照 WildCard 的指引完成操作，并保存生成的 OpenAI API Key。

## 详细步骤

### 1. 创建 DigitalOcean 服务器
选择新加坡数据中心，操作系统选择 CentOS 8。

![创建服务器](https://bbtdd.com/img/7576094436.webp)

**CPU 选项**：个人使用选择最低配置（4 美元/月）即可。

![CPU 选项](https://bbtdd.com/img/662360551704279.webp)

在 **Authentication Method** 步骤中，选择 SSH Key。DigitalOcean 提供详细的教程帮助创建 SSH Key。

![SSH Key](https://bbtdd.com/img/4329165364114511.webp)

点击 **Create Droplet**，等待服务器创建完成。成功后，复制服务器 IP 备用。

![服务器 IP](https://bbtdd.com/img/918083208949.webp)

### 2. 安装 Docker
点击 **Access Console**，打开服务器的终端。

![终端](https://bbtdd.com/img/86889712587.webp)

在终端中，按照以下步骤安装 Docker 和 Docker-Compose：

bash
# 更新 yum
yum update

# 下载 docker-ce 的 repo
curl https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo

# 安装依赖
yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm

# 安装 docker-ce
yum install docker-ce

# 启动 docker
systemctl start docker

# 设置开机启动
systemctl enable docker

# 安装 wget（如果报错）
yum -y install wget

# 下载 docker-compose
sudo wget https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m) -O /usr/local/bin/docker-compose

# 添加操作权限
sudo chmod +x /usr/local/bin/docker-compose

# 设置快捷方式
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# 查看版本
docker-compose --version


### 3. 部署 ChatGPT 应用
按照以下步骤部署 ChatGPT 应用：

bash
# 创建项目目录
mkdir chatgpt_web && cd chatgpt_web

# 安装 vim（如果没有）
yum -y install vim*

# 创建 docker-compose.yml 文件
vim docker-compose.yml


将以下内容填入 `docker-compose.yml` 文件并保存：

yaml
version: '3'
services:
  app:
    image: chenzhaoyu94/chatgpt-web:latest
    ports:
      - 3002:3002
    environment:
      OPENAI_API_KEY: sk-xxx（替换为自己的 API Key）
      TIMEOUT_MS: 60000


保存后，启动服务：

bash
docker-compose up -d


在浏览器中访问 `http://服务器IP:3002`，即可使用 ChatGPT 应用。

![ChatGPT 页面](https://bbtdd.com/img/665337701319805.webp)

### 4. 常见问题解决
如果遇到 **fetch failed** 错误，可以尝试刷新页面或重启 Docker 服务。

![错误处理](https://bbtdd.com/img/524010129963.webp)



👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)