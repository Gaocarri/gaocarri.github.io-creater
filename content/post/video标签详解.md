---
# 常用定义
title: "video标签问题记录"          # 标题
date: 2021-10-27  # 创建时间
draft: false                       # 是否是草稿？
tags: ["Web音视频"]  # 标签
categories: ["Web音视频"]              # 分类
author: "Carri"                  # 作者
keywords: ["Web音视频"]
description: "video标签问题记录"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

工作之后好久没写博客了，最近1024程序员节部门让我做了一个彩蛋项目，做完当天展示的时候源码被公司各种大佬扒，尴尬到脚抠地抠出三室一厅... 是时候重新写博客了，总结一下video的使用。

该篇文章主要用于记录工作时遇到的safari无法自动播放问题

# video标签的使用
MDN：HTML <video> 元素，用于在 HTML 或者 XHTML 文档中嵌入媒体播放器，从而支持文档内的视频播放

```html
<video controls width="250">
    <source src="/media/cc0-videos/flower.webm"
            type="video/webm">
    <source src="/media/cc0-videos/flower.mp4"
            type="video/mp4">
    Sorry, your browser doesn't support embedded videos.
</video>

```

上面的例子展示了 <video> 元素的用法。和 <img> 元素的使用类似，在 src 属性里加入一个我们需要展示的视频地址，同时也可以用其他属性来定义视频的宽度高度、是否自动或者循环播放、是否展示浏览器默认的视频控件等信息。

注意：浏览器并不是都支持相同的视频格式，所以你可以在 <source> 元素里提供多个视频源，然后浏览器将会使用它所支持的第一个源

# video使用中遇到的问题
## 自动播放

目前主流浏览器加强了对自动播放策略（Autoplay）的限制：浏览器在没有交互操作之前不允许有声音的媒体文件自动播放。 而且各个浏览器关于自动播放策略有不同的实现。
```html
{/* 自动播放 */}
<video ref={ videoRef } controls autoPlay />
```

有两种方案解决AutoPlay限制
1. 播放失败时绕过 Autoplay 限制
2. 直接绕过 Autoplay 限制

**方案一：播放失败绕过Autoplay限制**
在实际使用中，页面并不是完全被 Autoplay 限制，随着用户使用这个页面的次数增加，浏览器会将这个页面加入自己的 Autoplay 白名单列表中。
根据这个原理，可在检测到播放失败时，引导用户点击页面上的某个位置来恢复播放。(Google浏览器下测试均播放失败)
```javascript
// 可播放监听。当浏览器能够开始播放指定的音频/视频时触发
this.videoRef.addEventListener('canplay', () => {
    console.log('视频可以播放了')
    setTimeout(() => {
        // this.videoRef.paused 判断是否暂停，用来判断是视频是否在播放中，如果没有播放就
        console.log(this.videoRef.paused) 
        console.log('视频是否在暂停中', this.videoRef.paused) 
        this.isPlay = !this.videoRef.paused
    }, 500)
})

// 通过promise来判断是否在播放
const videoPromise = this.videoRef.play()
if(!!videoPromise) {
    videoPromise.then(() => {
        this.isPlay = true
        console.log('播放成功')
    }).catch(() => {
        this.isPlay = false
        console.log('播放失败')
    })
} else {
    // 此时可以通过canplay 监听是否在播放
}
```

**方案二：直接绕过 Autoplay 限制**
可以通过如下两种方案实现直接绕过 Autoplay 限制
* 在video标签中关闭静音muted属性设置为true。媒体不包含声音时不会被 Autoplay 限制。(引导用户开启声音)
* 在video标签中关闭静音muted属性设置为true。媒体不包含声音时不会被 Autoplay 限制。(引导用户开启声音)

注意：无论使用哪种方案，在自动播放策略的限制下，没有用户操作之前都不可能自动播放有声媒体。虽然浏览器会在本地维护一个白名单来决定对哪些网站解除自动播放限制，但该白名单无法通过 Javascript 探测到。

```javascript
// 移动端
document.body.addEventListener('touchstart', () => {
    console.log('触发播放')
    this.videoRef.play();
})
// PC端
document.body.addEventListener('mousedown', () => {
    console.log('触发播放')
    this.videoRef.play();
})
// 微信端IOS手机下触发自动播放，大部分IOS能正常自动播放（安卓机只能通过touchstart触发播放）
document.body.addEventListener('WeixinJSBridgeReady', () => {
    console.log('触发播放')
    this.videoRef.play();
})
```

注意： Safari 只允许通过用户交互来触发有声媒体的播放，而不是在用户交互后就打开 Autoplay 限制。(苹果真的是限制多...)


