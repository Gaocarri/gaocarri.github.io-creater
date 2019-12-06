---
# 常用定义
title: "搞懂JS闭包"           # 标题
date: 2019-12-06    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JavaScript","闭包"]  # 标签
categories: ["JavaScript"]              # 分类
author: "Carri"                  # 作者
keywords: ["JavaScript","闭包"]
description : "搞懂JS闭包"

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

算是对之前学习的查缺补漏吧

本文包括：

* 闭包是什么
* 闭包的用途
* 闭包的缺点

# 闭包是什么

一句话概括：`函数`和`函数内部能访问到的变量（也叫环境）`的总和，就是一个闭包。这是由JavaScript的作用域决定的

以以下一段代码为例
```
let saySomething = () => {
    let foo = 'hi' //声明一个变量
    let bar = () => {
        console.log(foo) //使用这个变量
    }
    return bar
}

let sayHi = saySomething()
sayHi() //hi
```

从第一行开始我们做了这样的事情

* 声明了一个函数`saySomething`
* 在这个函数内部声明了一个变量`foo`
* 声明了一个函数`bar`
* 返回了`bar`
* 将`saySomething`的返回值赋值给了`sayHi`
* 调用`sayHi`就可以访问到`foo`这个变量

只是为了方便理解，这是一个强行凑出来的例子（- -！），总而言之，我们可以间接访问到foo这个变量啦，那么这就是一个闭包。

关于return:如果不return的话，我们就无法使用这个闭包了，return bar和window.bar = bar是一个意思 

# 闭包的用途

一句话概括：闭包用来间接访问一个变量(即隐藏一个变量)。为什么要这样呢，我来举一个例子

如果我是一个超人
```
    window.me = superMan
```
这样很不妥啊，我不想让所有人都知道我是superMan,而且这样他们能乱改我的身份啊！

所以我不要上面这个代码了，我要把自己是超人这件事给隐藏起来（用局部变量），但我还是希望有一些相关人士能知道我的身份和帮我改身份（让别人可以间接访问）

一般闭包和立即执行函数一起用

```
!function () {
    var me = 'superMan'
        window.getSecret = function () {
            console.log(me)
        },
        window.createSecret = function (newSecret) {
            me = newSecret
    }
}()

getSecret() //知道了我是superMan
me //没有用，你不知道我是什么
createSecret('spiderMan') //我把自己变成了蜘蛛侠
getSecret() //知道我现在是蜘蛛侠了

```

好了，现在我就可以把这个函数告诉其他人（其他的JS文件），其他人就可以通过`getSecret()`和`w、createSecret()`方法来得知或者更改我现在的身份了

我把它的用处总结为以下几点

1. 函数外部可以访问到函数内部的变量或者对象
2. 避免了垃圾回收(可以一直能访问这个变量)

# 闭包的缺点
那么闭包有没有缺点呢？有

1. 常驻内存，增加内存使用量
2. 在IE中（IE有Bug），使用不当造成内存泄漏（乱用闭包，造成很多多余无用的变量占据内存空间）