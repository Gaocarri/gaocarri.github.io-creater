---
# 常用定义
title: "WebRTC浅析及在直播竞品中的使用"          # 标题
date: 2022-05-27  # 创建时间
draft: false                       # 是否是草稿？
tags: ["Web音视频"]  # 标签
categories: ["Web音视频"]              # 分类
author: "Carri"                  # 作者
keywords: ["Web音视频"]
description: "WebRTC浅析及在直播竞品中的使用"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

最近mentor让调研了竞品中的新技术应用，总结内部分享后，这篇文章同样搬运到自己的博客中。
# WebRTC简介
*WebRTC（Web Real-Time Communications*是由谷歌开源并推进纳入W3C标准的一项音视频技术，旨在通过点对点（Peer-to-Peer）的方式，在不借助中间媒介的情况下，实现浏览器之间的实时音视频通信

WebRTC能够应用在音视频会议、在线教育、直播甚至文件传输的多个领域

### 兼容性
![rtc1](/images/rtc1.png)

### 结构图
![rtc2](/images/rtc2.png)

# WebRTC功能/特性
1. 平台和设备无关性

* 实现：W3C提供的标准API、IETF提供的标准协议
* 重要性：开发人员可以编写跨操作系统、设备的WebRTC代码

2. 安全语音和视频

* 实现：安全RTP协议（SRTP）加密和身份验证
* 重要性：加密意味着他人无法监听和录制

3. 非同一般的语音和视频质量

* 实现：Opus音频编解码器、VP8视频编解码器等
* 重要性：内置的编解码器，避免下载编解码器，防止安装恶意程序和病毒。新的编解码器可以在检测到拥塞时自适应调整

4. 建立可靠对话

* 实现：网络地址转换（NAT）穿透技术
* 重要性：WebRTC可不需要通过服务器中继，直接在浏览器间传送的某提更加可靠和高质量，对服务器负荷也更小

5. 通过单个传输会话发送多个媒体流和多种媒体类型

* 实现：RTP（实时传输协议）和SDP（会话描述协议）的扩展协议
* 重要性：使用单个会话发送所有媒体更为高效和可靠

6. 网络状态自适应

* 多路复用RTP控制协议（RTCP）、带反馈机制的音频/视频配置（SAVPF）
* 网络状态的反馈是视频传输的关键

7. 支持多种媒体类型和多种媒体来源

*  实现：用于逐一协商每种来源的 
*  重要性：由于能逐一协商每种来源，因此带宽及其他资源可以得到最高效的利用 

8. 能够使用SIP、Jingle和PSTN与VoIP和视频通信系统进行互操作

* 实现：标准安全RTP（SRTP）媒体、标砖SDP和扩展协议
* 重要性：现有的VoIP和视频系统可以使用标砖协议与新的WebRTC系统协同发挥作用

**如何使用WebRTC首先介绍WebRTC中的专有名词，方便后续理解**

1. RTCPeerConnection：核心对象，每一个连接对象都需要传一个该对象
2. SDP（Session Description Protocol，会话描述协议）：包含建立连接的一些必要信息，比如IP地址等，SDP由RTCPeerConnection对象方法创建，目前我们不需要知道该对象中的具体内容，使用黑盒传输即可
3. ICE（Interactive Connectivity Establishment，交互式连接建立技术）：用户之间建立连接的方式，用来选取用户之间最佳的连接方式，在实际的网络当中，有很多原因能导致简单的从A端到B端直连不能如愿完成。这需要绕过阻止建立连接的防火墙，给你的设备分配一个唯一可见的地址（通常情况下我们的大部分设备没有一个固定的公网地址），如果路由器不允许主机直连，还得通过一台服务器转发数据，参照MDN

作为应用开发者，建立WebRTC会话需要4个步骤

1. 获取本地媒体

* 例如 getUserMedia()等

2. 在浏览器和对等端（其他浏览器或终端）之间建立连接

* 使用RTCPeerConnection，应用开发人员需要为每一对需要连接的浏览器（或其他终端）建立一个对等连接，例如有三个浏览器进行互相通信则需要建立三个连接。
* RTCPeerConnection构造函数方法的唯一输入项是一个配置对象，该对象包含ICE"打洞"通过各种网络地址转换设备和防火墙所使用的信息。

3. 将媒体和数据通道关联至该连接

* 建立连接后，可将任意数量的本地媒体流关联到对等连接，以通过该连接发送至远端服务器。同样也可以将任意数量的远端媒体流发送至对等连接的本地段，这样本地端就有了新的媒体流，而且可以像操作其他媒体流那样处理它们。

4. 交换会话描述

* RTCSessionDescription对象会存放会话描述，即有关如何建立媒体会话的信息，用于表示通过对等练级传输的所有媒体集合。当两个端之间交换完RTCSessionDescription对象后，即可建立媒体或数据会话。此时两个浏览器之间开始打洞，然后协商密钥，最后可以开始媒体和数据会话。注意，交互RTCSessionDescription对象后的所有一切活动通过浏览器执行JavaScript代码完成。

**《WebRTC权威指南》上给出的流程图**
![rtc3](/images/rtc3.png)

**信令服务器**
两个设备之间建立WebRTC连接需要一个信令服务器（上图中的web服务器充当了信令服务器，也可使用单独的信令服务器）来实现双方通过网络进行连接。信令服务器的作用是作为一个中间人帮助双方在尽可能少的暴露隐私的情况下建立连接。

WebRTC中并没有建立信令标准，可以使用WebSocket、XMLHttpRequest等等

通过信令（signaling）服务器转发媒体协商信息（sdp），以及网络协商信息（NAT及ICE协议）。
![rtc4](/images/rtc4.png)

# 2022年WebRTC发展趋势
原文地址：https://bloggeek.me/webrtc-trends-for-2022/

### 规模与性能
![rtc5](/images/rtc5.jpeg)
在2021年11月的Kranky Geek活动中，Google分享了他们过去一年中所做的工作，上图是围绕性能优化展示的ppt图片。其中很多任务已经完成，但还有更多的工作要去做。

这些改进的目的都是为了向加入单一对话的更多参与者提供更好的可扩展性。

### 新技术
大量的新技术发展和成熟，使用者可以利用这些技术充分发挥WebRTC的价值。

1. WebAssembly

WebAssembly 可提高Web 代码性能并支持跨语言编译。对于WebRTC来说，主要好处是将 WebAssembly 用于媒体操纵的机器学习任务。从噪声抑制，到背景替换和视频特效，再到视频灯光效果。 这些都可以用WebAssembly实现。

2. AV1

AV1是2018年推出的新的编解码器，自诞生以来一直有开发者致力于推动AV1成为WebRTC的解决方案。在2022年，AV1即将进入WebRTC。

3. WebTransport&WebCodecs

WebTransport和WebCodecs理论上可以实现媒体的编解码以及从服务器发送或接收媒体。目前越来越多的开发者试验这些技术并将他们与WebRTC一起使用。

### 应用领域
1. 直播

使用WebRTC的直播可以达到次秒级别的延迟，虽然现在的 WebRTC 开源软件还不能很好支持直播。对于很多场景（例如电商带货）而言，低延迟相当重要。而对于目前使用其他直播技术的产品而言，要想达到更低的延迟，可以考虑WebRTC的使用。

2. 从2D到元宇宙
元宇宙无疑是是当下产业和技术的热词，成为近期全球科技领域炙手可热的新概念，或许在未来可以看到WebRTC在元宇宙领域的应用。参考[从RTC走向元宇宙](https://cloud.tencent.com/developer/article/1928000)

# 国内竞品中WebRTC的落地场景
写在前面，直播领域一对多的方案，大多数都使用rtmp，在使用场景方面，基于成本及部署难度考虑，WebRTC更适用于一对一，或人数不多的场景（如会议）。但在国内的直播领域，已有一些厂商根据自身的业务场景，将WebRTC应用到了直播当中，下面就部分竞品中的落地场景，方案选型等进行一个总结，不对技术本身做更深入的探讨。

### 优酷播放黑科技 | 基于WebRTC实现的直播"云多视角"技术解析
[原文地址](https://mp.weixin.qq.com/s?__biz=Mzg4MDY0ODk0Ng==&mid=2247484951&idx=1&sn=26ccf34446b75a26c361d308009faea2&chksm=cf70b18af807389c770b6033fea54dfefa03835b19a50cfcf14141b0ded26017c0490a7c9ec0&token=2053335004&lang=zh_CN#rd)

**背景**

在中大型的直播活动中（例如街舞、欧冠等），粉丝向比较明显（明星、球星），如果能提供多个观看视角或者提供某个聚焦视角供切换选择，在用户体验上无疑能有很大的提升。

**方案选型**

优酷的方案选型围绕三个重点进行，低延迟、高清晰度、全机型覆盖。

针对拉流过程进行分析，可选的实现方案，经过对比，其采用了基于RTP的单路流多视角直播（基于RTP协议，端上同一时间仅拉取一路流，切视角不需要重新切流地址起播。）。
![rtc6](/images/rtc6.png)

**系统设计**

![rtc7](/images/rtc7.png)

**详细设计**
端侧为了尽可能的复用播控主链路，减少对主播放链路的侵入， 增加了多视角播放器。多视角播放器与其他播放器保持相同的调用接口，在播控处根据业务类型决定是否创建多视角播放器。在业务层增加多视角插件，与其他业务解耦，可以很方便的挂载与移除。
![rtc8](/images/rtc8.png)

**核心流程**
用户进入多视角模式主要起播流程如下：
![rtc9](/images/rtc9.png)
目前支持3种展示形态，分别为mix模式、cover模式及source模式， 下图为具体形态及切换过程。
![rtc10](/images/rtc10.png)

** 边端指令
通过RTP协议进行数据传输

** 项目落地
优酷在**播放能力调整、接入已有播放体系、端侧问题解决、服务并发优化**等问题和方面给出了解决方案。

# 参考文献

1. 《WebRTC权威指南（第三版中文版）》
2. 稀土掘金：[WebRTC学习总结](https://juejin.cn/post/6844903624561147918)
3. [MDN WebRTC API](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API)
4. [浅聊WebRTC视频通话](https://mp.weixin.qq.com/s?__biz=Mzg4MTYwMzY1Mw==&mid=2247499638&idx=1&sn=900a2826910d9ab9b0d630b642468693&chksm=cf61da5cf816534aa40a26a7402a97e3ce6a40bd72e9ee1a6afdda99141cf6a12af285c628ac&mpshare=1&srcid=1013SrIj6xAf0k6sSk8UZSLo&sharer_sharetime=1634089414456&sharer_shareid=0ad68442f04d4ffde89d3fb9b9e30d46&scene=21#wechat_redirect)
5. [网页端实时音视频服务架构与实践](https://juejin.cn/post/6844903516876570631)
6. [WebRTC Trends for 2022: Proprietary & differentiation are back](https://bloggeek.me/webrtc-trends-for-2022/)
7. [从RTC走向元宇宙](https://cloud.tencent.com/developer/article/1928000)
8. [优酷播放黑科技 | 基于WebRTC实现的直播"云多视角"技术解析](https://mp.weixin.qq.com/s?__biz=Mzg4MDY0ODk0Ng==&mid=2247484951&idx=1&sn=26ccf34446b75a26c361d308009faea2&chksm=cf70b18af807389c770b6033fea54dfefa03835b19a50cfcf14141b0ded26017c0490a7c9ec0&token=2053335004&lang=zh_CN#rd)
