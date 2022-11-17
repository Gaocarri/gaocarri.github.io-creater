---
# 常用定义
title: "Vue完整版与非完整版区别"          # 标题
date: 2020-03-30    # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["Vue"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "Vue基础（二）"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

## 官方文档的解释

![](/images/vue两个版本官方.png)

## 总结

![](/images/Vue两个版本.png)

## 两个版本对应的文件名

- 完整版
  - 文件名：vue.js和vue.min.js
  - 含min的是压缩版，表示是在生产环境下给用户使用的版本，取消了注释和警告

- 运行时版本
  - 文件名：vue.runtime.js或者vue.runtime.min.js
  - 同样的，含min的是压缩版，表示是在生产环境下给用户使用的版本，取消了注释和警告

## template和render怎么用

**template(完整版使用)**

- 代码

```javascript
// 需要编译器
new Vue({
  template: '<div>{{ hi }}</div>'
})
```

- 完整版拥有更多的功能，我们可以在template里写模板代码，编译器会自动进行转换

**render的使用（运行时版使用）**

- 代码

```javascript
// 不需要编译器
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})
```

- 运行时版本缺少了编译器，所以无法直接在template下写html
- 此时需要通过render提供的一个h函数，编写h函数下的代码实现转换
- 如果我们把html文件写在`.vue`文件下，使用h函数来render里面的内容，，vue会通过内部的vue-loader实现编译器的效果

**使用建议**

   运行时版比完整版的体积小了约30%,所以尽可能使用运行时版以减小代码体积，提升用户体验。