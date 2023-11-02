---
# 常用定义
title: "dom-to-image库无法在electron应用中复制图片的问题" # 标题
date: 2023-03-29 # 创建时间
draft: false # 是否是草稿？
tags: ["JavaScript", "库"] # 标签
categories: ["JavaScript"] # 分类
author: "Carri" # 作者
keywords: ["JavaScript", "库"]
description: "dom-to-image库无法在electron应用中复制图片的问题"

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true # 关闭评论
toc: true # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false # 关闭打赏
mathjax: true # 打开 mathjax
---

昨天遇到了一个神奇的问题，我们某个依赖 [dom-to-image](https://github.com/tsayen/dom-to-image) 的组件交付给其他部门使用时，出现了复制结果缺少背景图片的问题，其他部门使用electron，排查后发现一个electron独有的坑。

## 1. 问题原因

首次复制时，控制台报错，大概可以猜测和图片的加载有关

![图片1](/images/domToImage/图片1.png)

进入sources，可以看到错误出现在dom-to-image库打包后的代码中。考虑到打包后产物不方便debug，我们将库的源码直接复制到控制台当中执行，进行断点调试

![图片2](/images/domToImage/图片2.png)

根据调用堆栈，不难发现dom-to-image使用了ajax的方式去请求图片资源，而原本完整的路径在被库进行处理后变为了 file:///C:/Program%20Files%20(x86 

这里的string是css属性

![图片3](/images/domToImage/图片3.png)

URL_REGEX = /url\(['"]?([^'"]+?)['"]?\)/g;

URL_REGEX这个正则的意思是期望去匹配url后第一个括号里的内容，而因为应用的安装路径带有括号，因此到x86时就被截断了，匹配出了不正确的路径

那么js是如何拿到这段绝对路径的呢，根据调用栈，dom-to-image会拿到节点信息进行获取

![图片4](/images/domToImage/图片4.png)

## 2. 解决方式

最终发现问题出现在了正则匹配上，总体上来说有两种解决方式

1. 更改库源码，修改正则匹配的方式，比如用栈的方式记录括号开头和结尾，进行匹配

- 实际上这段正则还用在了其他类型的string匹配，实际的输入类型非常多样，改动结果未知，可靠性较差。对于electron应用带括号的安装路径，也是一种比较特殊的情况。
- 图片采用cdn，采用cdn以避免此类问题

- 使用cdn，把路径保证在开发人员可控的范围，对于这个特殊问题的解决更为合理。

采用解决方式2
