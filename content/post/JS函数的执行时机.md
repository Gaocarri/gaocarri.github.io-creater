---
# 常用定义
title: "JS函数的执行时机"           # 标题
date: 2019-11-04    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JS","Javascript","javascript入门","javascript语法","函数"]  # 标签
categories: ["JavaScript"]              # 分类
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

今天学习到了关于JS函数执行时机方面的知识。


* 以下两个for循环各自独立
```
// 第一个：
let i = 0
for(i = 0; i<6; i++){
  setTimeout(()=>{
    console.log(i)
  },0)
}
// 第二个：
for(let i = 0; i<6; i++){
  setTimeout(()=>{
    console.log(i)
  },0)
}
```
* 运行代码我们可以知道第一个for循环会在后台打印出6个6，第二个for循环会在后台打印出0，1，2，3，4，5


* 下面分析造成这种情况的原因
  
  1. setTimeout()的用法
        * setTimeout()方法设置一个定时器，该定时器在定时器到期后执行一个函数或指定的一段代码
        * delay：延迟的毫秒数 (一秒等于1000毫秒)，函数的调用会在该延迟之后发生。如果省略该参数，delay取默认值0，意味着“马上”执行，或者尽快执行。不管是哪种情况，实际的延迟时间可能会比期待的(delay毫秒数) 值长，原因请查看[Reasons for delays longer than specified](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout#Reasons_for_delays_longer_than_specified)
  2. 第一段代码因为setTimeout()这个函数,`console.log(i)`会在for循环全部执行后才执行，此时i变为了6，因此会打印出6个6。举个例子，老板让你搬100个箱子，在你搬箱子的时候老板说去打印一个文件，你就会在搬完100个箱子的时候再去打印这个文件，通俗来说就是等一会，同理setTimeout.
  3. 那么第二段代码为什么不一样了呢，这就是JS的坑爹之处：
        * 因为JS在for和let一起用的时候会加东西
        * 每次for循环会多创建一个i（坑爹），所以打印出了6个不同的i

