---
# 常用定义
title: "自己画出JS世界"           # 标题
date: 2019-11-07    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JS","Javascript","原型"]  # 标签
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

# 关于一个JS世界是如何形成的？

1. 创建根对象#101(它有toString等属性)、根对象无名字（它的__proto__:null）
[![Mk9Qtf.png](https://s2.ax1x.com/2019/11/07/Mk9Qtf.png)](https://imgchr.com/i/Mk9Qtf)


2. 创建函数的原型#208(它有call,apply等属性),原型__proto__的地址为#101
[![Mk9mnA.png](https://s2.ax1x.com/2019/11/07/Mk9mnA.png)](https://imgchr.com/i/Mk9mnA)


3. 创建数组的原型#404(它有push,pop等属性),原型__proto__的地址为#101
[![Mk9n0I.png](https://s2.ax1x.com/2019/11/07/Mk9n0I.png)](https://imgchr.com/i/Mk9n0I)


4. 创建Function（实际没有名字）#342，原型__proto__的地址为#208
5. 让Function的prototype地址为#208
6. 此时发现Function的__proto__和prototype都是#208
[![Mk9u7t.png](https://s2.ax1x.com/2019/11/07/Mk9u7t.png)](https://imgchr.com/i/Mk9u7t)


7. 用Function创建Object（实际没有名字）
8. 让Object.prototype的地址为#101
[![Mk9MAP.png](https://s2.ax1x.com/2019/11/07/Mk9MAP.png)](https://imgchr.com/i/Mk9MAP)


9. 用Function创建Array（实际没有名字）
10. 让Array.prototype的地址为#101
[![Mk9lh8.png](https://s2.ax1x.com/2019/11/07/Mk9lh8.png)](https://imgchr.com/i/Mk9lh8)


11. 创建window对象（它不属于JS世界）
12. 用window的"Function""Object""Array"属性将他们命名
[![Mk939S.png](https://s2.ax1x.com/2019/11/07/Mk939S.png)](https://imgchr.com/i/Mk939S)


13. 记住一点JS创建一个对象时，不会给它名字

# 完整的图示
![MkiUHA.png](https://s2.ax1x.com/2019/11/07/MkiUHA.png)

# JS三大公理
1. 对象.__proto__===其构造函数.prototype
2. 根公理：Object.prototype是所有对象（直接或间接）的原型
3. 函数公理：所有函数都是由Function构造的，任何函数__proto__===Function.prototype(任何函数有Object/Array/Function)

* Object.prototype是所有对象的原型，Object是Function构造出来的，所以Function构造了Object.prototype,推论，Function才是万物之源啊（错）
* 错的理由:Function构造了Object.prototype这个地址，但没有构造它对应的根对象，根对象是原本就存在的！