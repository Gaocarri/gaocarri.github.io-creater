---
# 常用定义
title: "JS对象基本用法"           # 标题
date: 2019-11-03    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JS","Javascript","javascript入门","javascript语法","对象"]  # 标签
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
# 声明对象的两种语法
```
// 第1种：
let obj = { 'name': 'frank', 'age': 18 }
// 第2种：
let obj = new Object({'name': 'frank'})
```
* 键名必须是字符串，引号大部分时候可以省略（空格和中文时不可省）
* 就算引号省略了，字符串还是字符串
* 第2种是标准的写法，但大多数时候下用第1种即可


# 如何删除对象的属性
```
delete obj.name
//或
delete obj['name']
```
* 可以删除'name'这个属性
* obj.xxx===undefined 不能判断xxx是不是obj的属性，也有可能有这个属性但是未定义

# 如何查看对象的属性

* `Object.keys(obj)`查看obj的属性有哪些
* `console.dir(obj)`查看obj的属性及属性值
* `obj['name']`
* `obj.name`注意这里的name是字符串
* `obj[name]`注意这里的name是变量


# 如何修改或增加对象的属性
* 直接赋值
```
let obj = {name: 'frank'} // name是字符串
obj.name = 'frank' // name是字符串
obj['name'] = 'frank'
obj[name] = 'frank' // name是变量，所以错，无法这么赋值
obj['na'+'me'] = 'frank'
let key = 'name'; obj[key] = 'frank'
let key = 'name'; obj.key = 'frank' //错，因为等价于obj.key等价于obj['key']，但key是变量
```
* 批量赋值

```
Object.assign(obj, {age: 18, gender: 'man'})
```
* 无法通过自身来修改原型，一般不要修改原型(可以obj.__proto__.toString = 'xxx'，但不要这么做，容易混乱)

```
let common = {
    '国':'中国'，
    hairColor:'black'
}
let person = Object.create(common)
```
* 以common为原型创造person
* person的原型就是common，但是common有原型__proto__


# 'name' in obj和obj.hasOwnProperty('name') 的区别
```
'xxx' in obj === false
```
* false说明不含该属性，true说明含该属性
* 可以看共有属性是否含有（区别）


```
obj.hasOwnProperty('toString') === false
```
* 可以查看是否含有自身的属性（不包括共有属性），这里的false说明toString是共有属性