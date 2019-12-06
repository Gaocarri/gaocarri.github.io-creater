---
# 常用定义
title: "使用Promise"           # 标题
date: 2019-12-02    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JavaScript","ES6","异步"]  # 标签
categories: ["JavaScript"]              # 分类
author: "Carri"                  # 作者
keywords: ["JavaScript","ES6","异步"]
description : "使用Promise"     

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

虽说最近学到Vue了,但是突然发现自己对Promise的用法掌握的不怎么样,遂写一篇博客,也可以当作自己平常使用的参考。

内容包括以下几个部分：

* Promise是什么
* Promise解决了什么问题
* Promise的api
* Promise习题实例

# Promise是什么？

一句话概括：Promise是一种写异步代码的方式

它的基本用法：
```
let example =new Promise(resolve,reject){
    //在这里写你想写的代码
    //然后在某些条件下解决了什么问题
    if(/*你想写的条件*/){
        resolve()
    }else{
        reject()
    }
}

example.then(()=>{
    //这里写example被resolve时执行的
},()=>{
    //这里写example被reject时执行的
})
```

上面这段代码代表什么意思呢？
1. 构造了一个Promise实例
   * 构造函数接受一个函数作为参数
   * 调用构造函数得到实例p的同时，作为参数的函数会立即执行
   * 参数函数接受两个回调函数参数resolve和reject
   * 在参数函数被执行的过程中，如果在其内部调用resolve，会将p的状态变成fulfilled，或者调用reject，会将p的状态变成rejected

2. 调用.then()

   * 调用.then可以为实例p注册两种状态回调函数
   * 当实例p的状态为fulfilled，会触发第一个函数执行
   * 当实例p的状态为rejected，则触发第二个函数执行

Promise的基本模式是（一种异步编程模式）：

* 将异步过程转化为Promise对象
* 对象有三个状态
* 通过.then注册状态的回调
* 已完成状态能触发回调

# Promise解决了什么问题

之前就听说过回调地狱（也有人叫回调金字塔），它的代码是这样子的
```
do1(done1 => {
  do2(done1, done2 => {
    do3(done2, done3 => {
      do4(done3, done4 => {
        do5(done4, done5 => {
          // 终于取到done5了
        })
      })
    })
  })
})
```

这段代码看起来很乱很难懂，如果使用Promise改写的话就变得简单明了了:
```
do1()
    .then(do2)
    .then(do3)
    .then(do4)
    .then(do5)
    .then(done=>{
        //得到done啦
    })
```

如果让我选，傻子才选第一种写法。

# Promise的api

首先说说Promise怎么使用?

1. Promise有三种状态
   * pending(待定)
   * fulfilled(已执行)
   * rejected(已拒绝)

resolve()使得Promise实例的状态变成fulfilled,reject()使得Promise实例的状态变成rejected。这两种都是已完成的状态，只有已完成的状态下，才能触发回调。

Promise的api?

* new Promise：可以将一个异步过程转化为Promise对象，先有Promise对象，后面才有Promise编程方式
* then:用于为Promise对象的状态注册回调函数。它会返回一个Promise对象，所以可以进行链式调用，也就是.then后面可以继续.then。在注册的状态回调函数中，可以通过return语句改变.then返回的Promise对象的状态，以及向后面.then注册的状态回调传递数据；也可以不使用return语句，那样默认就是将返回的promise对象resolve。
* .catch：用于注册rejected状态的回调函数，同时该回调也是程序出错的回调，即如果前面的程序运行过程中出错，也会进入执行该回调函数。同.then一样，也会返回新的promise对象。
* Promise.resolve():调用Promise.resolve会返回一个状态为fulfilled状态的promise对象，参数会作为数据传递给后面的状态回调函数
* Promise.reject():同理，只是返回状态变成了rejected
* Promise.all():是Promise对象上的静态方法，该方法的作用是将多个Promise对象实例包装，生成并返回一个新的Promise实例。注意：在下面的方法中，promise数组中所有的promise实例都变为resolve的时候，该方法才会返回，并将所有结果传递results数组中。promise数组中任何一个promise为reject的话，则整个Promise.all调用会立即终止，并返回一个reject的新的promise对象。
```
let p1 = Promise.resolve(1),
    p2 = Promise.resolve(2),
    p3 = Promise.resolve(3);
Promise.all([p1, p2, p3]).then(function (results) {
    console.log(results);  // [1, 2, 3]
});
```
* Promse.race：顾名思义赛跑，意思就是说，Promise.race([p1, p2, p3])里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。（感觉用的很少）

# Promise的习题实例
如何交替实现红绿灯？
在网上看到的一个面试题，据说会问两种写法，先把Promise写法搞定吧
```
        let red = () => {
            console.log('red');
        }

        let green = () => {
            console.log('green');
        }

        let yellow = () => {
            console.log('yellow');
        }

        let light = (fn, timer) =>
            new Promise(resolve => {
                setTimeout(function() {
                    fn()
                    resolve()
                }, timer)
            })


        // times为交替次数
        function start(times) {
            if (!times) {
                return
            }

            times--
            light(red, 3000)
                .then(() => light(green, 1000))
                .then(() => light(yellow, 2000))
                .then(() => start(times))

        }

        start(4)
    ```
好了以上就是我学到的Promise总结，后面还有Generator，async,await等，都是对Promise的优化(存疑，还没学习到，用到了再深入吧)