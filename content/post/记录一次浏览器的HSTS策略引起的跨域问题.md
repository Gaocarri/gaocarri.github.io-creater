---
# 常用定义
title: "记录一次浏览器的HSTS策略引起的跨域问题"          # 标题
date: 2022-04-11   # 创建时间
draft: false                       # 是否是草稿？
tags: ["javascript","库"]  # 标签
categories: ["javascript"]              # 分类
author: "Carri"                  # 作者
keywords: ["javascript","库"]
description: "记录一次浏览器的HSTS策略引起的跨域问题"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

这周工作发现在chrome和edge上发现访问http页面提示跨域，后端已经设置了CORS。

## 表现
* 第一次是个预检请求（没有预检的话不会报错，简单请求不预检）
![HSTS1](/images/HSTS1.png)
* 可以看到提示了一个Non-Authoritative-Reason：HSTS
![HSTS2](/images/HSTS2.png)

## 问题原因
1. 服务端nginx或其他有设置Strict-Transport-Security(严格传输安全)选项
2. 请求过一次https后，浏览器请求这个接口的会认为需要使用HSTS策略
3. 当请求http的时候，会发生307内部跳转
![HSTS3](/images/HSTS3.png)
4. 307跳转被cors预检请求认为是不合法的，故预检失败

## 解决方法
1. 临时的解决方案
chrome中在浏览器中输入并访问团：chrome://net-internals/#hsts
查询Query HSTS/PKP domain，来查看该接口有没有使用HSTS，
这里发现有，可以Delete domain security policies删除掉缓存
![HSTS4](/images/HSTS4.png)
2. 前端所有配置了https的域名都采用https链接保持一致性
3. nginx端解决
将: add_header Strict-Transport-Security max-age=31622400
改为: add_header Strict-Transport-Security max-age=0;

## 参考
一文解析前端请求307导致CORS跨域失败:https://www.jianshu.com/p/08fefb25ed17