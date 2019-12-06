---
# 常用定义
title: "对于JS继承的理解"           # 标题
date: 2019-12-10   # 创建时间
draft: false                       # 是否是草稿？
tags: ["JavaScript","JS","原型","继承"]  # 标签
categories: ["JavaScript"]              # 分类
author: "Carri"                 # 作者
keywords: ["JavaScript","JS","原型","继承"]
description : "对于JS继承的理解"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---
同样又是一个查缺补漏，这几天疯狂看博客，把不清楚的知识给弄清楚

首先，谈JS的继承就得提到原型以及原型链，`对象_proto_===其构造函数.prototype`,他们共同指向了一个地址，这个地址是这个对象的原型，它里面存着各种属性和方法，`Object.prototype`是所有对象的直接或间接原型，而所有函数的原型都是`Function.prototype`。举个例子：
```
let obj = [1, 2, 3]
obj.toString()
```
当调用obj.toString时，js 首先在obj对象自身上查找toString方法；未找到，继续沿着原型链查找Array.prototype上有没有toString；未找到，继续沿着原型链在Object.prototype上查找。最终在Object.prototype上找到了toString方法，这就是原型链和它的作用。

好了正式讲讲及继承吧
ES5和ES6分别提供了不同继承的方法，基于原型的继承和基于class的继承,我查了博客找出了他们之间的区别
## 基于原型的继承
```
function Person(name){
    this.name = name
}
Person.prototype.sayHi = function(){
        console.log('你好，我是'+this.name)
    }//这个方法是拿出来特地说明与class继承的不同

function Child(name,age){
    Person.call(this,name)
    this.age = age
}

let xw = new Child('小王',10)
xw.name  //小王
xw.sayHi() //报错
```

在ES5中通过call,bind方法指定this是this(即`Child`)，使得`Child`继承了`Person`的属性，但是它并没有继承`Person`原型上的`sayHi`方法,这是由于这种方法对原型链上的继承没有实现。此时`Child.__proto__===Function.prototype`，而`Child.__proto__！==Person`

要想实现对原型链上的继承可以在声明`xw`前加上`Child.prototype = Object.create(Person.prototype)`,它的意思是把`Child.prototype`上的`_proto_`属性指向了`Person.prototype`.


上面这样很麻烦，所以ES6有了class，上面这段函数用class写是这样的

## 基于class的继承
```
class Person{
    constructor(name){
        this.name=name
    }
    sayHi(){
         console.log('你好，我是'+this.name)
    }
}

class Child extends Person{
    constructor(name,age){
        super(name)
        this.age=age
    }
}

let xw = new Child('小王',10)
xw.name//'小王'
xw.sayHi()//'你好，我是小王'
```

此时`Child.__proto__===Person`，自然它就有sayHi方法了

class其实是一种语法糖，它也是利用原型实现继承的，怎么实现就不讲了。。。

## 补充 关于new做了什么

直接照搬MDN啦

1. 创建一个空的简单JavaScript对象（即{}）；
2. 链接该对象（即设置该对象的构造函数）到另一个对象 ；
3. 将步骤1新创建的对象作为this的上下文 ；
4. 如果该函数没有返回对象，则返回this。

