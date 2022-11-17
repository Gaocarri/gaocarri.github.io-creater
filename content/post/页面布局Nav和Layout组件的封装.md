---
# 常用定义
title: "页面布局Nav和Layout组件的封装"          # 标题
date: 2020-07-12   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架","TypeScript"]  # 标签
categories: ["黑曜石记账项目笔记"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架","JS库"]
description: "页面布局Nav和Layout组件的封装"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# Nav.vue 底部导航栏的封装

1.svg的使用（使用svg-sprite-loader）

- iconfont.con下载svg
- shims-tsx.d.ts中添加

```
declare module '*.svg' {
  const content: string;
  export default content
}
```

- 安装 svg-sprite-loader(命令行输入)

```javascript
yarn add svg-sprite-loader -D
yarn add svgo-loader
```

- 在vue.config.js里面添加，启用两个loder

```
   module.exports = {
     lintOnSave: false,
     chainWebpack: config => {
       const dir = path.resolve(__dirname, 'src/assets/icons')
   
       config.module
         .rule('svg-sprite')
         .test(/\.svg$/)
         .include.add(dir).end() //包含icons目录
         .use('svg-sprite-loader').loader('svg-sprite-loader').options({ extract: false }).end()
       config.plugin('svg-sprite').use(require('svg-sprite-loader/plugin'), [{ plainSprite: true }])
       config.module.rule('svg').exclude.add(dir) //其他svg loader排除icons
     }
   }
```

   

- 封装Icon.vue

```typescript
   <template>
     <svg class="icon">
       <use :xlink:href="'#'+name" />
     </svg>
   </template>
   
   <script lang="ts">
   // 引入整个icons
   const importAll = (requireContext: __WebpackModuleApi.RequireContext) =>
     requireContext.keys().forEach(requireContext);
   try {
     importAll(require.context("../../assets/icons", true, /\.svg$/));
   } catch (error) {
     console.log(error);
   }
   
   export default {
     name: "Icon",
     props: ["name"]
   };
   </script>
   
   <style lang="scss" scoped>
   .icon {
     width: 1em;
     height: 1em;
     vertical-align: -0.15em;
     fill: currentColor;
     overflow: hidden;
   }
   </style>
```

- main.ts将Icon变为全局组件

```
   import Icon from '@/components/Icon.vue'
   Vue.component('Icon', Icon)
```

   2.封装nav.vue,通过url判断路由是否活跃以显示不同的icon(使用v-show显示)

```
   <script lang='ts'>
   import Vue from "vue";
   import { Component } from "vue-property-decorator";
   
   @Component
   export default class Nav extends Vue {
     get moneyIsActive() {
       return this.$route.path.indexOf("money") !== -1;
     }
     get statisticsIsActive() {
       return this.$route.path.indexOf("statistics") !== -1;
     }
   }
   </script>
```

# Layout.vue的封装

1. 使Nav居于页面下方的方式

```
<template>
     <div class="layout-wrapper">
       <div class="content">
         <slot />
       </div>
       <Nav class="nav" />
     </div>
   </template>
   
   <style lang='scss' scoped>
   .layout-wrapper {
     display: flex;
     flex-direction: column;
     min-height: 100vh;
     > .content {
       overflow: auto;
       flex: 1;
     }
   }
   </style>
```

# TabBar的封装

1. 获得选中状态(添加 class selected)

```vue
<template>
  <div class="tab-bar">
    <div class="icon">
      <slot name="icon" />
    </div>
    <span class="expend" :class="{'selected':type==='-'}" @click="selectType('-')">支出</span>
    <span class="income" :class="{'selected':type==='+'}" @click="selectType('+')">收入</span>
    <div class="delete">
      <slot name="delete" />
    </div>
  </div>
</template>

<script lang='ts'>
import Vue from "vue";
import { Component } from "vue-property-decorator";

@Component
export default class extends Vue {
  type: string = "-";

  selectType(type: string) {
    this.type = type;
    this.$emit("selectType", type);
  }
}
</script>
```

2. slot插槽添加可能的Icon,并使它绝对定位

```
<div class="icon">
    <slot />
 </div>
 
   .icon {
    position: absolute;
    top: 50%;
    margin-top: -9px;
  }
```

