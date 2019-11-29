---
# 常用定义
title: "认识MVC"           # 标题
date: 2019-11-29    # 创建时间
draft: false                       # 是否是草稿？
tags: ["javascript","框架"]  # 标签
categories: ["框架"]              # 分类
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

花了几天的时间熟悉了MVC的思想，回头发现自己以前写的代码弱（太）爆（多）了，记一篇博客回顾所学。

以下内容代表自己的思考，结论并非一定正确。

# 什么是MVC

一句话概括：MVC是一种架构设计模式，它通过关注点分离鼓励改进应用程序组织。

* M：Model(数据类型)负责操作所有数据
* V：View(视图)负责所有UI界面
* C：Controller(控制器)负责其他

# MVC的M,V,C分别做什么
一图流:

![Qk9As0.png](https://s2.ax1x.com/2019/11/29/Qk9As0.png)

给出以下示例

* M:构建一个M，M保存了数据，当M改变的时候，M会通知V。

例如保存缓存，保存方法
```
const m = {
  data: {
    n: parseInt(localStorage.getItem('n'))
  },
  create() {}
  delete() {},
  update(data) {
    Object.assign(m.data, data)
    eventBus.trigger('m:updated')
    localStorage.setItem('n', m.data.n)
  },
  get() {}
}
```

* V：构建一个V，V包含关于视图的部分，当V变化的时候，视图会重新渲染，呈现给用户不一样的界面

包含HTML，以及渲染的方法等

```
const v = {
  el: null,
  html: ...,
  init(container) {
    v.el = $(container)
  },
  render(n) {
    if (v.el.children.length !== 0) v.el.empty()
    $(v.html.replace('{{n}}', n))
      .appendTo(v.el)
  }
}

```

* C：构建一个C，C包含各类绑定事件，用户可以操作C,通过C可以改变M，改变V。

示例
```
const c = {
  init(container) {
    v.init(container)
    v.render(m.data.n) // view = render(data)
    c.autoBindEvents()
    eventBus.on('m:updated', () => {
      console.log('here')
      v.render(m.data.n)
    })
  },
  events: {
    'click #add1': 'add',
    'click #minus1': 'minus',
    'click #mul2': 'mul',
    'click #divide2': 'div',
  },
  add() {
    m.update({n: m.data.n + 1})
  },
  minus() {
    m.update({n: m.data.n - 1})
  },
  mul() {
    m.update({n: m.data.n * 2})
  },
  div() {
    m.update({n: m.data.n / 2})
  },
  autoBindEvents() {
    for (let key in c.events) {
      const value = c[c.events[key]]
      const spaceIndex = key.indexOf(' ')
      const part1 = key.slice(0, spaceIndex)
      const part2 = key.slice(spaceIndex + 1)
      v.el.on(part1, part2, value)
    }
  }
}
```

# EventBus是做什么用的

EventBus用来进行对象间通信的，在上述的代码M中和C中存在EventBus，这些代码表示一个过程，举个例子，当我调用了C中的`add()`方法，会触发M中的`update()`,它会把更新过的n分发给所有包含n的数据，更新它们，并且使用`EventBus.trigger()`,C中的`init()`通过`EventBus.on()`监听了`trigger`,当触发时，它就会调用一个渲染的方法`render()`更新页面。这样的好处就是自动更新，我每次不必在使用`add()`的时候在手动写一次`render()`渲染页面.


EventBus的api:

1. `EventBus.trigger()`,用于触发()中的内容。
2. `EventBus.on()`，用于监听()中的内容，当监听到时，会触发一个事件
3. `EventBus.off()`,用于监听()中的内容，当监听到时，会结束一个事件

# 表驱动编程是什么

* 将所有的数据方法放在表中，直接在表中查找，而不必使用逻辑语句，好处是简单直接。
* 它的本质是，从表里查询信息来代替逻辑语句

以下一段代码，通过if,else来判断月份，给出当月的天数。
```
if (month === 1) {
    return 31;
} else if (month == 2) {
    return 28;
}else if (month == 3) {
    return 31;
}
...
else if (month == 12) {
    return 31;
} else {
    return 0;
}
```

上述代码冗长复杂，我们可以把它改写为
```
array daysPerMonth = [31,28,31,30,31,30,31,31,30,31,30,31];
days = daysPerMonth[month - 1];
```

这样就简洁明了了。

它的优点：

  1. 更加易读和直白；
  2. 用数据代替逻辑，容易维护；
  3. 可以把表中的数据存放在文件中，运行时读取，减少代码体量。数据变更时只需要修改文件；
  4. 降低复杂度。


以上只是一个最简单的例子，MVC中我们把一个个数据和方法放在了表中，同样实现了这些优点。

# 我对模块化的理解

通过对MVC的学习,对模块化有了一定的认识,看着代码从复杂到简易,好像渐渐明白了模块化带来的好处

1. 什么是模块化
    * 模块化就是将一个复杂的程序依据一定的规则(规范)封装成几个块(文件), 并进行组合在一起
    * 块的内部数据与实现是私有的, 只是向外部暴露一些接口(方法)与外部其它模块通信

2. 模块化的优点
    * 避免命名冲突(减少命名空间污染)
    * 更好的分离, 按需加载
    * 更高复用性
    * 高可维护性

马上要学习Vue了,这段时间先回顾回顾,夯实一下自己的基础。



