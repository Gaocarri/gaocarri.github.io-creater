---
# 常用定义
title: "记一次由yarn.lock引起的dayjs问题" # 标题
date: 2023-09-11 # 创建时间
draft: false # 是否是草稿？
tags: ["npm", "yarn.lock"] # 标签
categories: ["黑曜石博客"] # 分类
author: "Carri" # 作者
keywords: ["npm", "yarn.lock"]
description: "记一次由yarn.lock引起的dayjs问题.md"

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true # 关闭评论
toc: true # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false # 关闭打赏
mathjax: true # 打开 mathjax
---

## 背景

基于antd5搭建了一套我们内部使用的ui组件库，同时项目中仍然存在antd4，两者共存，在引入时发现了国际化配置失效的问题

## antd5国际化配置失效

antd的日期中文国家化配置失效了，看起来组件非常奇怪，年为中文，月和星期又为英文。

![图片1](/images/dayjsQ/图片1.png)

查询github issues，发现有人提到了可能是dayjs版本不一致的问题

我们通过在项目中输入以下命令，可以看到确实装了好几个版本的依赖

```bash
npm ls dayjs
```

![图片2](/images/dayjsQ/图片2.png)

可以看到项目中一共存在了三个版本的dayjs，这时候就开始疑惑了

1. 在antd5中，dayjs的依赖是这么写的

![图片3](/images/dayjsQ/图片3.png)表示支持1.11.1 - 2.0.0之前的版本，优先安装最新

2. 在我们基于antd5封装的组件库中，后来的某个组件也用到了dayjs

![图片4](/images/dayjsQ/图片4.png)表示支持1.11.9 - 2.0.0之前的版本

3. 在我们的项目，以及几个我们自己的常用库中也用到了dayjs，它们都是这样的

![图片5](/images/dayjsQ/图片5.png)表示支持1.8.25到2.0.0的所有版本。



所以这就很奇怪了，按照^的作用，他们理论上会安装同一个版本的依赖，然而事实却相悖，这时候才想到会不会是yarn.lock锁住了

## yarn.lock在项目中的变迁

![图片6](/images/dayjsQ/图片6.png)

经过了一系列的复现及思考，理清了这个过程

在这个项目刚刚建立的时候，我们执行yarn.lock安装了1.9.6的依赖包

后来引入antd5，由于antd5的dayjs依赖必须大于1.11.1，因此安装了1.11.8的依赖包

再后来在组件库中用到dayjs，这个dayjs的依赖必须大于1.11.9，因此又安装了1.11.9的依赖包

最后造成了这个局面，对于一般的项目来说，共存问题不大，但是dayjs就会导致国际化配置失效

## 解决

手动删除两个lock信息，然后重新执行 yarn

![图片7](/images/dayjsQ/图片7.png)

最终效果也ok了

![图片8](/images/dayjsQ/图片8.png)
