---
# 常用定义
title: "Vue关于data为什么是函数这件事"          # 标题
date: 2021-03-01   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["Vue"]               # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "Vue关于data为什么是函数这件事"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

Vue组件data为什么是函数，面试的时候被问到了，感觉解释的不太清楚，借博客整理一下

# 什么是Vue组件
这里先借用官方的解释:

组件是可复用的 Vue 实例，且带有一个名字：在这个例子中是 `<button-counter>`，如下：
```vue
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```
# data为啥么是函数不是对象
官方已经解释了，一个vue组件就是一个vue实例。

在JS当中实例是通过构造函数来创建的，每个构造函数可以`new`出很多个实例，那么每个实例都会继承原型上的方法或属性。

vue的data数据其实是vue组件原型上的属性，数据存在于内存当中

vue组件为了保证每个实例上的data数据的独立性，规定了必须使用函数，而不是对象。

因为使用对象的话，每个实例（组件）上使用的data数据是相互影响的，这当然就不是我们想要的了。对象是对于内存地址的引用，直接定义个对象的话组件之间都会使用这个对象，这样会造成组件之间数据相互影响。

# 不使用函数和使用函数示例
## 不使用函数
```javascript
// 创建一个简单的构建函数
var MyComponent = function() {
    
}
// 原型链对象上设置data数据，为里为Object
MyComponent.prototype.data = {
  name: 'abc',
  age: 20,
}
// 创建两个实例:小明，小红
var xiaoming = new MyComponent()
var xiaohong = new MyComponent()
// 默认状态下小明和小红的年龄一样
console.log(xiaoming.data.age === xiaohong.data.age) // true
// 改变一下小明的年龄
xiaoming.data.age = 30;
// 打印下小红的年龄，发现因为改变了小明的年龄，结果造成小红的年龄也变了
console.log(xiaoming.data.age)// 30
console.log(xiaohong.data.age) // 30
```
这是因为小红和小明的`data`都引用的是同一个内存地址

## 使用函数
```javascript
// 创建一个简单的构建函数
var MyComponent = function() {
    // ... code here
    this.data = this.data();
}
// 原型链对象上设置data数据，为里为Object
MyComponent.prototype.data = function(){
    return {
      name: 'abc',
      age: 20,
    }
};
// 创建两个实例:小明，小红
var xiaoming = new MyComponent()
var xiaohong = new MyComponent()
// 默认状态下小明和小红的年龄一样
console.log(xiaoming.data.age === xiaohong.data.age) // true
// 改变一下小明的年龄
xiaoming.data.age = 30;
// 打印下小红的年龄，发现因为必变了小明的年龄，结果造成小红的年龄也变了
console.log(xiaoming.data.age)//30
console.log(xiaohong.data.age) // 20
```
使用函数后，使用的是data()函数，data()函数中的this指向的是当前实例本身

# 总结
当一个组件被定义， data 必须声明为返回一个初始数据对象的函数，因为组件可能被用来创建多个实例。如果 data 仍然是一个纯粹的对象，则所有的实例将共享引用同一个数据对象！通过提供 data 函数，每次创建一个新实例后，我们能够调用 data 函数，从而返回初始数据的一个全新副本数据对象。