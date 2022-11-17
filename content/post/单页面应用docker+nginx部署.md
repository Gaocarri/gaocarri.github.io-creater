---
# 常用定义
title: "单页面应用docker+nginx部署"          # 标题
date: 2022-08-27   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Web工具"]  # 标签
categories: ["Web工具"]              # 分类
author: "Carri"                  # 作者
keywords: ["docker","nginx"]
description: "单页面应用docker+nginx部署"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

在公司实践了不少docker项目，总结了一套docker+nginx部署方案，以防忘记，为后续作参考。

# Docker镜像构建
包含nginx配置、Dockerfile编写、Gitlab CI配置

## nginx配置
后台项目不涉及服务端，ssr，不需要node环境，直接使用nginx即可，为了方便更改nginx的配置文件，在项目目录下建立一个nginx.conf文件，这里建立在根目录的config目录下
```bash
touch config/nginx.conf
```

1.nginx.conf编写
由于我们部署的项目并不是直接被用户访问到（会经由公司的服务器代理转发或者负载均衡），所以请按以下配置，否则可能会有奇怪的问题（http2问题）
```nginx
server {
    listen 80;
    listen  [::]:80;
    server_name localhost;

    # 开启gzip----------
    # 打开gzip
    gzip on;
    # 设置用于处理请求压缩的缓冲区数量和大小
    gzip_buffers 4 16k;
    # 当返回内容大于此值时才会使用gzip进行压缩
    gzip_min_length 10k;
    # 设置gzip压缩级别，数字越大级别越高，但会提高服务器的CPU资源消耗
    gzip_comp_level 6;
    # 一定要设置！，因为内外网进行了反向代理到该服务上，server之间默认时使用HTTP/1.0协议通信，gzip默认是1.1！！
    gzip_http_version 1.0;
    # 增加响应头，告诉接收方经过了压缩处理，对于本身不支持gzip压缩的浏览器有用
    gzip_vary on;
    # 设置需要压缩的类型，不要压缩图片!!压缩图片太消耗CPU资源
    gzip_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
	# ------------------------

    # 日志--------------------
    access_log /var/log/nginx/nginx-access.log main;
    error_log /var/log/nginx/nginx-error.log error;
    open_log_file_cache max=1000 inactive=20s valid=1m min_uses=2;
    # ------------------------

    # 后台项目都是单页面应用，只需设置 /
    location / {
        root /usr/share/nginx/html;
        # history路由需要开启，否则刷新页面时nginx无法找到资源
        try_files $uri $uri /index.html;
        index index.html index.htm;

        # 设置浏览器缓存----------
        # 默认情况下nginx的响应头中会带有ETag、Last-modified的协商缓存字段，但无强缓存，所以需要自己设置强缓存cache-control（优先使用cahe-control，不要使用expires，max-age单位s）
        # 不能设置private，否则不能被服务器缓存
        location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|mp4|ogg|ogv|webm|htc)$ {
            add_header Cache-Control "public,max-age=31536000";
        }

        location ~* \.(?:css|js)$ {
            add_header Cache-Control "public,max-age=2592000";
        }
        # ------------------------
    }

    # 如果需要代理其他文件目录（如微前端）可以参考如下配置
    # location /subapp {
    #     alias /usr/share/nginx/subapp;
    #     try_files $uri $uri/ /index.html;
    #     index index.html index.htm;
    # }
}
```

## Dockerfile编写
Dockerfile用于定制镜像，Dockerfile概念可参考[使用Dockerfile定制镜像](https://yeasy.gitbook.io/docker_practice/image/build)

1. 在项目根目录建立文件
``` bash
touch Dockerfile # docker build默认使用的配置
touch Dockerfile.dev # 用于区分dev环境，docker build -f Dockerfile.dev运行
touch dockerignore # 用于docker构建上下文时忽略掉.dockerignore里面的文件列表
```

2. Dockerfile及Dockerfile.dev编写
* Dockerfile
```Dockerfile
FROM node:12-alpine as node

WORKDIR /app

COPY ./package.json ./yarn.lock /app/
# 利用node_modules缓存
RUN yarn

COPY . /app/
# 正式环境打包
RUN yarn build

# alpine是一个精简的docker镜像，具有最少的软件包，但足以运行节点应用程序。
FROM nginx:alpine

ENV TZ="Asia/Shanghai"

# 拷贝dist文件
COPY --from=node /app/dist /usr/share/nginx/html
# 拷贝nginx配置
COPY --from=node /app/config/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
```

* Dockerfile.dev(用于工作时的内网环境)
```Dockerfile
FROM node:12-alpine as node

WORKDIR /app

COPY ./package.json ./yarn.lock /app/
# 利用node_modules缓存
RUN yarn

COPY . /app/
# develop环境打包
RUN yarn build-dev

# alpine是一个精简的docker镜像，具有最少的软件包，但足以运行节点应用程序。
FROM nginx:alpine

ENV TZ="Asia/Shanghai"

# 拷贝dist文件
COPY --from=node /app/dist /usr/share/nginx/html
# 拷贝nginx配置
COPY --from=node /app/config/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
```
* dockerignore
dockerignore的配置也可以和.gitignore保持一致，可以排除掉不需要参与打包的文件
```
logs/
npm-debug.log
yarn-error.log
node_modules/
coverage/
dist/
.idea/
run/
.DS_Store
*.sw*
*.un~
.tsbuildinfo
.tsbuildinfo.*
READEME.m
```

3. 优化点
原先我们的项目拷贝目录的操作
```Dockerfile
COPY . /app

RUN yarn & yarn build
```

优化后
```Dockerfile
COPY ./package.json ./yarn.lock /app/
# 利用node_modules缓存
RUN yarn

COPY . /app/
RUN yarn build
```

为什么这么做？为了利用node_modules的缓存，如果一开始拷贝整个项目，runner就会认为这一步的文件改变了，就不会去用缓存，如果先拷贝的package.json和yarn.lock未改变，则会使用之前构建的镜象的这一层去使用缓存

* 使用缓存时，yarn命令秒结束
![docker+nginx1](/images/docker+nginx1.png)

* 未使用缓存时,yarn命令持续安装
![docker+nginx2](/images/docker+nginx2.png)


改写这一步能节省掉安装依赖的速度，并且不需要在构建时一并放入node_modules，减小了体积

关于打版本号改变了package.json导致无法使用缓存的问题，可以通过脚本拷贝一个不带version的package.json，然后利用缓存

## Gitlab CI配置
git push=>触发 CI=>执行gitlab-ci.yml

Gitlab CI的配置需要在项目中编写.gitlab-ci.yml文件和在gitlab仓库设置CI/CD的变量

1. .gitlab-ci.yml

在项目根目录创建

```bash
touch .gitlab-ci.yml
```

内容

```
image: 这里需要一个基础的系统，如debian

stages:
    - build_develop
    - build_release
    - build_branch

variables:
    DOCKER_REGISTRY_DEV: xxx.dev.com # 推送到内网仓库
    DOCKER_REGISTRY: xxx.dev.com # 推送到外网仓库
    DOCKER_REPO: frontend/xxx # 镜像仓库目录

before_script:
    # 输出当前所有环境变量
    - env

build_develop:
    tags:
        - docker # 使用tag为docker的runner触发构建
    # execute shell command only git tag operation
    only:
        - develop # 只有推送 时才触发该job
    stage: build_develop
    script:
        - image=${DOCKER_REGISTRY_DEV}/$DOCKER_REPO:latest

        # login to dockerhub
        - docker login -u $NCR_DEV_USER -p $NCR_DEV_TOKEN $DOCKER_REGISTRY_DEV

        # build docker image
        - echo "$ docker build -t $image"
        - docker build -f Dockerfile.dev -t $image .

        # push docker image
        - echo "$ docker push $image"
        - docker push $image

        # logout dockerhub
        - docker logout $DOCKER_REGISTRY_DEV

build_release:
    tags:
        - docker # 使用tag为docker的runner触发构建
    # execute shell command only git tag operation
    only:
        - tags # 只有推送tag时才触发该job
    stage: build_release
    script:
        # use git tag name as docker build image name
        - TAG=${CI_BUILD_TAG} # 默认使用当前推送的tag名作为镜像tag，可修改为下面的commit hash的前六位等
        # use current git commit hash as build image name
        # - TAG=${CI_BUILD_REF:0:6}
        - image=${DOCKER_REGISTRY}/$DOCKER_REPO:latest
        - image_with_tag=${DOCKER_REGISTRY}/$DOCKER_REPO:$TAG

        # login to dockerhub
        - docker login -u $NCR_USER -p $NCR_TOKEN $DOCKER_REGISTRY

        # build docker image
        - echo "$ docker build -t $image"
        - docker build -t $image -t $image_with_tag .

        # push docker image
        - echo "$ docker push $image"
        - docker push $image

        # push docker image
        - echo "$ docker push $image_with_tag"
        - docker push $image_with_tag

        # logout dockerhub
        - docker logout $DOCKER_REGISTRY

build_branch:
    tags:
        - docker
    except:
        - develop
        - tags
        - master
    stage: build_branch
    script:
        - TAG=`echo ${CI_BUILD_REF_NAME} | tr "/" "-"`
        - image=${DOCKER_REGISTRY_DEV}/${DOCKER_REPO}:${TAG

        # login to dockerhub
        - docker login -u ${NCR_DEV_USER} -p ${NCR_DEV_TOKEN} ${DOCKER_REGISTRY_DEV}

        # build docker image
        - echo "$ docker build -t ${image}"
        - docker build -f Dockerfile.dev -t ${image} .

        # push docker image
        - echo "$ docker push ${image}"
        - docker push ${image}

        # logout dockerhub
        - docker logout ${DOCKER_REGISTRY_DEV}
```
2. 配置gitlab仓库
需配置变量
![docker+nginx3](/images/docker+nginx3.png)

## 镜像的部署
初次部署要手动启用一个docker镜像
1. 拉取镜像
```bash
docker pull 镜像名
```
2. 查看镜像id
```bash
docker image ls
```

3. 启动容器
```bash
docker run --name 去一个名字 -d -p 82:80 镜像id
```

4. 查看容器启动情况，第一个就是刚才启动的容器
```bash
docker ps
```

# 参考文献
1.  [docker从入门到实践](https://yeasy.gitbook.io/docker_practice/) 
2.  [连前端都看得懂的《Nginx入门指南》](https://juejin.cn/post/6844904129987526663)
3.  [一文读懂前端缓存](https://zhuanlan.zhihu.com/p/44789005) 