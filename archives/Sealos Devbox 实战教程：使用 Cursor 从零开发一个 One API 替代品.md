# Sealos Devbox 实战教程：使用 Cursor 从零开发一个 One API 替代品

随着技术的不断发展与 AI 的崛起，许多原本需要团队协作才能完成的任务，如今可以通过自动化与智能化的方式轻松实现。**单个开发者的能力得到了极大提升**，借助各种工具，一个人便可以完成开发、测试、运维等整条链路的工作，真正成为“全栈工程师”。

此前，我们分享过一些入门级的 Hello World 教程。今天，我们将通过一个实际业务案例，展示 Devbox 不仅仅是一个玩具工具，而是一个真正的生产力工具。

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)

---

## **Sealos Devbox 开发环境简介**

Sealos 平台拥有众多应用，其中很多管控层面的应用都是使用 **Cursor + Go + Next.js** 开发的。我们的开发环境直接使用 **Sealos Devbox**，上线也是通过 Devbox 一键完成。这种开发模式让团队具备了高效的作战能力：**大部分重复性工作通过自动化或 AI 完成，开发者可以专注于核心业务逻辑**。

### **示例应用：AI Proxy**

以 Sealos 中的 AI Proxy 应用为例，这是一个典型的前后端分离架构的应用，主要由以下两部分组成：

- **基于 Next.js 开发的前端应用**：负责用户界面与 BFF 层，BFF 层用于用户鉴权，并将经过验证的请求转发给后端服务。
- **使用 Golang 开发的后端服务**：负责核心业务逻辑，包括 token 存储、日志记录和请求转发等功能。

---

## **Golang 后端开发实战**

### **1. 创建开发环境**

首先，在 **Sealos Cloud** 中打开 Devbox 应用，创建一个新项目，选择 Go 作为运行环境，版本选择 1.23。

Devbox 提供了以下实用功能：

- **灵活的资源配置**：根据项目需求自由调整 CPU 和内存，确保性能同时控制成本。
- **一键启用 HTTPS**：系统自动分配安全域名，无需手动配置 SSL 证书。
- **全自动域名管理**：从开发到测试环境，域名配置全程由系统处理。

创建完成后，几秒钟即可启动开发环境。接下来，使用 Cursor 连接开发环境，首次打开会提示安装 **Devbox 插件**，安装后即可自动连接。

### **2. 导入项目到 Cursor**

Fork **Sealos 源码**到自己的仓库，然后将仓库克隆到 Devbox 开发环境。

### **3. 测试环境开发**

在 Cursor 的面板中切换到 “Database” 标签页，创建 PostgreSQL 和 Redis 实例。复制数据库连接信息并在终端中启动服务：

bash
export ADMIN_KEY=sealos-admin
export SQL_DSN=<复制的pgsql连接串>/postgres
export REDIS_CONN_STRING=<复制的redis连接串>
go run . --port 8080


运行成功后，使用 curl 进行测试：

bash
curl https://mmznjndvzdrv.sealoshzh.site/api/status -H "Authorization: sealos-admin"


### **4. 优化数据库设计**

为了简化开发，将数据库中的外键约束改为程序层面的显式调用。通过 Cursor 的 Chat 功能，让 AI 自动生成优化代码。

### **5. 上线到生产环境**

在 Cursor 目录顶层的 `endpoint.sh` 中设置启动命令，然后在 Devbox 发布页面发布版本。部署完成后，使用生产环境的域名进行测试。

---

## **Next.js 前端开发实战**

### **1. 前端项目搭建**

在 Devbox 中创建一个 Node.js 环境，版本选择 20，端口改为 3000。建议选择 `4c 16G` 的配置。克隆 Fork 的 Sealos 仓库，并安装依赖：

bash
pnpm i
pnpm -r --filter ./packages/client-sdk run build


### **2. 对接后端环境**

在项目根目录创建 `.env` 文件，配置以下关键变量：

bash
NEXT_PUBLIC_MOCK_USER=""
AI_PROXY_BACKEND_KEY=""
APP_TOKEN_JWT_KEY="test123"
AI_PROXY_BACKEND=""
AI_PROXY_BACKEND_INTERNAL=""
ADMIN_NAMESPACES=""


配置完成后，运行 `pnpm dev` 启动开发服务器。

---

## **总结**

Sealos AI Proxy 项目采用了经典的 **Next.js App Router** 架构，其中 `app/[lng]` 用于页面路由，`app/api` 用于 API 路由。这种分层设计让 Golang 后端能够专注于核心业务逻辑，无需关心认证等基础设施，从而提高了代码的灵活性与可移植性。

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)