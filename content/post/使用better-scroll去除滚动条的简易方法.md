---
# 常用定义
title: "使用better-scroll去除滚动条的简易方法"          # 标题
date: 2020-03-06   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架","JS库"]  # 标签
categories: ["Vue"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架","JS库"]
description: "使用better-scroll去除滚动条的简易方法"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---
# 前言
这段时间在做一个记账webApp；发现原生滚动条既难看也难用，本来打算将其隐藏了事，无奈试过很多方法都无法满足要求，于是想到了之前用过的一款插件，即better-scroll，下面本文将简单介绍在typeScript的vue应用下使用better-scorll的基本方法

# BetterScroll简介

这里直接截取官网：better-scroll 是一款重点解决移动端（已支持 PC）各种滚动场景需求的插件。它的核心是借鉴的 iscroll 的实现，它的 API 设计基本兼容 iscroll，在 iscroll 的基础上又扩展了一些 feature 以及做了一些性能优化。

better-scroll 是基于原生 JS 实现的，不依赖任何框架。它编译后的代码大小是 63kb，压缩后是 35kb，gzip 后仅有 9kb，是一款非常轻量的 JS lib。

# BetterScroll 去除滚动条的简单使用方法

1. 安装betterscroll

```
yarn add better-scroll
```

2. 由于typescript的原因，需要安装

```
@types/better-scroll
```

3. 封装一个通用的Scroll.vue文件

```vue
<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script lang='ts'>
import Vue from "vue";
import { Component } from "vue-property-decorator";

import BScroll from "better-scroll";

@Component
export default class Scroll extends Vue {
  scroll: any;
  mounted() {
    {
      // 创建scroll对象 
      this.scroll = new BScroll(this.$refs.wrapper as any, {
        click: true // betterscroll默认不监听浏览器的原生click，加上true即可监听
      });
    }
  }
}
</script>
```

4. 如在Add.vue中使用Better-scroll（后满会封装Add.vue组件）

```
<template>
	<Scroll class="content">
		<!-此处省略内容-->
	</Scroll>
</template>

<script lang='ts'>
import Vue from "vue";
import { Component } from "vue-property-decorator";

import Scroll from "@/components/common/Scroll.vue";

@Component({
  components: {
    Scroll
  }
})
export default class Add extends Vue {

}
</script>

<style>
	.content{
  	position: absolute;
  	top: 50px;
  	bottom: 270px;
  	left:0
  	right:0
  	overflow: hidden;
	}
</style>

```

5. ok 可以愉快地使用better-scroll了

- 注意：必须给better-scroll指定的高度才可以滚动（无高度不可以滚动）