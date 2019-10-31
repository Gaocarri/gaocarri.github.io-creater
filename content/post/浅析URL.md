---
# 常用定义
title: "浅析URL"           # 标题
date: 2019-10-31    # 创建时间
draft: false                       # 是否是草稿？
tags: ["URL","HTTP","网络"]  # 标签
categories: ["HTTP"]              # 分类
author: "Carri"                  # 作者

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---
# IP的作用是什么
* IP：Internet Protocol
* 约定了两件事情（作用）
    1. 如何定位一台设备
    2. 如何封装报文，和其他设备交流
* 几个特殊的IP
    1. 127.0.0.1表示自己
    2. localhost通过hosts指定为自己（hosts文件只能以管理员模式打开）
    3. 0.0.0.0不表示任何设备


# 域名是什么，ping命令怎么用
* 域名是对IP的别称
* 例：qq.com
* 一个域名可以对应不同的IP，叫做负载均衡
* 一个IP可以对应不同的域名，叫做共享主机
* 命令行输入`ping 域名` 可以知道这个域名对应的IP
* 域名级别：
    1. com是顶级域名
    2. baidu.com是二级域名（俗称一级域名）
    3. www.baidu.com是三级域名（俗称二级域名）
  

# DNS的作用是什么，nslookup命令怎么用
* DNS:Domain Name System 域名系统
* 作用：简而言之将域名解析为IP
* 命令行输入`nslookup baidu.com` 会返还baidu.com对应的所有IP


# URL包含哪几部分，每部分分别有什么用
* URL:Uniform Resource Locator 统一资源定位符
* URL=协议+域名或IP+端口号+路径+查询字符串+锚点
* 例：https://www.baidu.com/s?wd=hello&rsv_spt=1#5
* 注意：
   1. 锚点看起来可能有中文，实际不支持中文。
   2. 锚点是无法在开发者工具的Network面板看到的，因为锚点#后面不会传给服务器
* 当你在chrome输入baidu.com 时发生了什么
   1.  chrome浏览器会向电信/联通提供的DNS服务器询问baidu.com对应什么IP
   2.  电信/联通会回答一个IP（具体过程很复杂）
   3.  然后chrome才会向对应的IP的80/443端口发送请求
   4.  请求内容是查看baidu.com的首页


   * 服务器默认用80端口提供http服务
   * 服务器默认用443端口提供https服务
   * 可以在开发者工具中的Network看到具体的端口
   * 其他端口参考维基百科
  
# curl命令
`curl -v http://baidu.com`

* url会被curl工具重写，先请求DNS获得IP
* 先进行TCP连接，TCP连接成功后，开始发送HTTP请求
* 请求内容看一遍
* 响应内容看一遍
* 响应结束后，关闭TCP连接（看不出来）
* 真正结束