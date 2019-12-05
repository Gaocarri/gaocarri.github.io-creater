---
# 常用定义
title: "call,apply和bind的使用"           # 标题
date: 2019-12-07    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JavaScript","this"]  # 标签
categories: ["JavaScript","this"]              # 分类
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

同样是对之前知识的查缺补漏

call,apply,bind都可以用来指定this的值。
举例
```
const xw = {
    name:'小王',
    gender:'男',
    age:18,
    say:function(city,university){
        console.log(`${this.gender}生${this.name},${this.age}岁,在${city}市${university}上学`)
    }
}
const xh = {
    name:'小红',
    gender:'女',
    age:17
}
```
`xw.say(‘南京’,'南京大学')` 得到“男生小王，18岁，在南京市南京大学上学”,这里的this就指向xw。

要把`this`变为`xh`，得到“女生小红，17岁，在北京市北京大学上学”就可以用call,apply,bind了，以下是用法和区别
# call
`xw.say.call(xh,'北京','北京大学')`
call后面跟着的第一个参数就是要指定的this,后面的参数与`xw.say`方法是一一对应的

# apply
`xw.say.apply(xh,['北京','北京大学'])`
say后面跟着的第一个参数也是要指定的this,与call的区别在于后面的第二个参数是一个数组，数组中的元素与`xw.say`方法一一对应

# bind
`xw.say.bind(xh,'北京','北京大学')()`
bind的传参方式与call一致，区别在于它返回的是一个函数，所以要再加`()`来调用
它也可以写成`xw.say.bind(xh)('北京','北京大学')`（这是因为bind返回的是一个函数，我们仍可以在调用的时候传参）


