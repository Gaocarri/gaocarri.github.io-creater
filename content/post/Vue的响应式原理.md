---
# 常用定义
title: "Vue的响应式原理"          # 标题
date: 2020-01-01   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["Vue"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "Vue的响应式原理"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---
Vue的响应式原理主要分为两大部分

1. 修改数据，Vue内部监听数据的改变（Object.defineProperty 监听对象属性改变）
2. 发布者订阅者模式

## 监听数据的改变

声明一个对象，然后对该对象的key进行遍历

```
const obj = {
  message:'我是data里的属性1',
  name:'我是data里的属性2'
}

Object.keys(obj).forEach(key=>{
  let value = obj[key]

  Object.defineProperty(obj,key,{
    set(newValue){
      // key改变时会调用set方法
      value = newValue
    },
    get(){
      // 获取key对应的值时会调用get方法，如控制台输入obj.name
      return value
    }
  })
})
```

Object.defineProperty里的：

  * set方法根据解析Html，获取哪些地方用到了这个属性
  * get方法将值返回给用到该属性的每个地方


## 发布者订阅者模式

发布者：
```
  class Dep {
    constructor() {
      this.subs = []
    }
    addSub(watcher) {
      this.subs.push(watcher)
    }
    notify() {
      this.subs.forEach(item => {
        item.update()
      })
    }
  }

  const dep = new Dep()
```

订阅者
```
  class Watcher {
    constructor(name) {
      this.name = name
    }
    update() {
      console.log(this.name + 'update了');
    }
  }

  const w1 = new Watcher('张三')
  dep.addSub(w1)
  const w2 = new Watcher('李四')
  dep.addSub(w2)
  const w3 = new Watcher('王五')
  dep.addSub(w3)

  // 发布
  dep.notify()
```

说下这个过程吧：

* new了一个发布者`dep`,一个`data`属性会对应一个不同的`dep`
* new了订阅者（相当于Vue实例中的每一个用到data其中一个属性的地方，给它们每个人都起个名字），例如new了`w1`之后，会调用发布者`dep.addSub()`方法(在`Object.defineProperty`的`get`方法中调用)，让其真正成为发布者的订阅者（加入到订阅者数组中）
* 当某个订阅者值发生改变时，会调用发布者`dep.notify()`方法，通知所有订阅者它们的值发生改变了，执行订阅者的`update()`。
* 于是乎,当值发生改变时,在`Object.defineProperty`的`set`方法中,就会调用`dep.notify()`来更新所有订阅者的值


图解响应式原理
![响应式原理](/images/200101.jpg)
```
new Vue({
  el:'#app',
  data:{

  }
})
```

* el会走Compile过程，data会走Observer过程
* Observer即`Object.defineProperty`

言语都在图中(我这字简直不像研究生写出来的- -！)