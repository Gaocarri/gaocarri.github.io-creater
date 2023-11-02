---
# 常用定义
title: "H5通用app唤端流程" # 标题
date: 2023-04-11 # 创建时间
draft: false # 是否是草稿？
tags: ["JavaScript"] # 标签
categories: ["JavaScript"] # 分类
author: "Carri" # 作者
keywords: ["JavaScript"]
description: "H5通用app唤端流程"

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true # 关闭评论
toc: true # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false # 关闭打赏
mathjax: true # 打开 mathjax
---

目前国内各浏览器（微信、qq）对于 APP 的唤端表现各异，并且安卓/ios 也存在差异，根据实际情况，梳理各状态下的表现。

依赖开源的[callapp-lib](https://github.com/suanmei/callapp-lib)，阅读源码后梳理实际的唤端流程

### 安卓唤端流程

![唤端1](/images/唤端1.png)

### IOS唤端流程

* ios qq 禁止了 universalLink 唤起app，安卓不受影响 - 18年12月23日
* ios qq 浏览器禁止了 universalLink - 19年5月1日
* ios 微信自 7.0.5 版本放开了 Universal Link 的限制
* ios 微博禁止了 universalLink

![唤端2](/images/唤端2.png)

ios用户未安装app，使用universal link跳转app失败的解决方案 :

将universal link的域名作为中转，跳转到appstore

### 参考文献

[H5唤起app指南](https://suanmei.github.io/2018/08/23/h5_call_app/)
