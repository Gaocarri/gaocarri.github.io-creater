---
# 常用定义
title: "适合前端的15分钟入门Docker" # 标题
date: 2023-08-17 # 创建时间
draft: false # 是否是草稿？
tags: ["Docker"] # 标签
categories: ["Docker"] # 分类
author: "Carri" # 作者
keywords: ["Docker"]
description: "适合前端的15分钟入门Docker"

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true # 关闭评论
toc: true # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false # 关闭打赏
mathjax: true # 打开 mathjax
---

## Docker 介绍及安装

![docker1](/images/docker/docker1.png)

Docker 是一种开源项目，用于将应用程序自动部署为可在云或本地运行的便携式独立容器。 Docker 也是一家公司，它与云、Linux 和 Windows 供应商（包括 Microsoft）协作，致力于推广和发展这项技术。

### Docker 是什么

Docker 是一个应用打包、分发、部署的工具

一个轻量的虚拟机，只包含应用运行所需要的环境

### 打包、分发、部署

打包：将软件运行需要的依赖、库、软件打包到一起，变成一个**镜像**（安装包）。

分发：将打包好的镜像上传到**镜像仓库**。

部署：使用镜像启动**容器**（安装完的应用），自动模拟一个一模一样的运行环境。

### Docker 能做什么，优势是什么

- 解决“我的电脑上没问题的”问题。
- 快速安装其他人打包好的软件
- 多版本软件共享（比如同一台机子上运行不同 node 版本的应用）
- windows 上随意安装和使用 linux

.....

### 什么是镜像、容器

**镜像**：可以理解为软件安装包，可以方便的进行传播和安装。
**容器**：软件安装后的状态，每个软件运行环境都是独立的、隔离的，称之为容器。

### 安装 Docker（For Windows）

Docker 官网：https://www.docker.com/

安装后无法打开：

![docker2](/images/docker/docker2.png)

windows 安装 Docker 必备：WSL

![docker3](/images/docker/docker3.png)

## 使用 Docker 快速安装 Nginx

### 使用镜像

**获取镜像：**docker pull <name>:<tag>

**列出镜像：**docker image ls

![docker4](/images/docker/docker4.png)

**运行容器：**docker run -d -p <本机端口>:<镜像服务端口> <镜像> <终端>

**查看运行中的容器**：docker ps

**停止容器：**docker container stop <容器 id>

**删除容器：**docker container rm <容器 id>

也可以直接使用 Docker Desktop 操作：

![docker5](/images/docker/docker5.png)

**进入容器终端**：

![docker6](/images/docker/docker6.png)

## 制作自己的 Web 项目镜像

### 编写 Dockerfile

描述我们的镜像，将软件安装完依赖后，构建，然后使用 Nginx 部署静态资源

```dockerfile
# 以node镜像作为基础，推荐使用 Alpine 镜像，因为它被严格控制并保持最小尺寸
FROM node:14-alpine as node
# 指定工作目录 /app
WORKDIR /app
# 将项目文件拷贝到 /app/目录下
COPY . /app/
# 装依赖、打包项目
RUN yarn --registry=https://registry.npmmirror.com \
&& yarn build
# 多阶段构建，以nginx镜像为基础
FROM nginx:alpine
# 拷贝静态文件
COPY --from=node /app/dist /usr/share/nginx/html
# 声明容器运行时提供服务的端口为80
EXPOSE 80
```

### 构建镜像并启动容器

![docker7](/images/docker/docker7.png)

### 减小构建体积

- **多阶段构建**：

- - **阶段 1：**根据源码装依赖，构建项目（项目相关 存在项目源码，node_modules）
  - **阶段 2**：使用 nginx 部署 dist 中的静态资源（项目相关 只存在 dist）

  ![docker8](/images/docker/docker8.png)

### 分析构建速度瓶颈

**npm 装包缓慢**：

![docker9](/images/docker/docker9.png)

**改良:**

```dockerfile
# 以node镜像作为基础，推荐使用 Alpine 镜像，因为它被严格控制并保持最小尺寸
FROM node:14-alpine as node
# 指定工作目录 /app
WORKDIR /app

# ======================================改进
# 拷贝yarn.lock,pakcage.json装包
COPY ./package.json ./yarn.lock /app/
# 装依赖 利用缓存
RUN yarn --registry=https://registry.npmmirror.com
# 将整个项目拷贝到 /app/目录下
COPY . /app/
# 打包项目
RUN yarn build
# ======================================改进

# 多阶段构建，以nginx镜像为基础
FROM nginx:alpine
# 拷贝静态文件
COPY --from=node /app/dist /usr/share/nginx/html
# 声明容器运行时提供服务的端口为80
EXPOSE 80
```

**第二次构建 缓存生效：**

![docker10](/images/docker/docker10.png)

### 理解联合文件系统、分层构建

**联合文件系统（UnionFS）:** 一种分层、轻量级并且高性能的文件系统，它支持**对文件系统的修改作为一次提交来一层层的叠加**，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。

联合文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

另外，不同 Docker 容器就可以**共享一些基础的文件系统层**，同时再**加上自己独有的改动层**，大大提高了存储的效率。

**每一层镜像是下一层的\*\***父\***\*镜像。**

**分层构建：**在镜像的构建过程中 docker 会遍历 Dockerfile 文件中的所有指令，顺序执行。对于每一条指令，docker 都会在缓存中**查找是否已存在可重用的镜像**，

对于前端项目，package.json 是较为稳定，不易变动的，可以先拷贝 package.json 和 lock 文件进行依赖安装，做到充分利用缓存的目的

### 理解 COPY 和 ADD

ADD 指令和 COPY 的格式和性质基本一致，均可以复制文件。但是在 COPY 基础上增加了一些功能。

比如 ADD 可以解压缩

```dockerfile
FROM scratch
ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /
...
```

尽可能使用 COPY，语义更加明确

### 目录挂载

**现存问题**：容器中产生的数据，比如 log 文件等，容器删除后就丢失了

![docker11](/images/docker/docker11.png)

**理解容器保持无状态**：镜像使用的是**分层存储**，**容器也是如此**。每一个容器运行时，是**以镜像为基础层**，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为 **容器存储层**。

容器存储层的生存周期和容器一样，**容器消亡时，容器存储层也随之消亡**。因此，任何保存于容器存储层的信息都会随容器删除而丢失。

按照 Docker 最佳实践要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用**数据卷**、或者**绑定宿主目录**，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。

**3 种挂载方式：**

- **bind mount:** 直接把宿主机目录映射到容器内，适合挂代码目录和配置文件。可挂到多个容器上
- **volume:** 由容器创建和管理，创建在宿主机，所以删除容器不会丢失，官方推荐，更高效，Linux 文件系统，适合存储数据库数据。可挂到多个容器上
- **tmpfs mount:** 适合存储临时文件，存宿主机内存中。不可多容器共享。

文档参考：https://docs.docker.com/storage/

![docker12](/images/docker/docker12.png)

**挂载演示：**

容器中 nginx 日志存储位置：/var/log/nginx

将容器日志挂载到宿主机：D:/code/log

```dockerfile
docker run -d -p 9090:80 -v D:/code/log:/var/log/nginx vite-demo-app:v3.0.0
```

查看挂载到宿主机上的日志：

![docker13](/images/docker/docker13.png)

## 扩展阅读

Docker Compose： https://yeasy.gitbook.io/docker_practice/compose

Kubernetes - 开源容器编排引擎：https://yeasy.gitbook.io/docker_practice/kubernetes

## 参考资料

Docker 官方文档：https://docs.docker.com/

Docker - 从入门到实践： https://yeasy.gitbook.io/docker_practice
