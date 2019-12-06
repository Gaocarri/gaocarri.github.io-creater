---
# 常用定义
title: "jQuery的使用"           # 标题
date: 2019-11-13    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JavaScript","JS","DOM","jQuery"]  # 标签
categories: ["jQuery"]              # 分类
author: "Carri"                  # 作者
keywords: ["JavaScript","JS","DOM","jQuery"]
description : "jQuery的使用"   

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

这两天自己尝试了封装DOM，并且熟悉了jQuery，记一篇博客概述jQuery的基本使用方法。

# jQuery如何获取元素


首先概括jQuery的基本思想---选择某个网页元素，对其进行一系列操作。

具体步骤就是使用jQuery.()(简写为$.())来选中某个元素，选取元素的方法如下列所示。

1. 选择表达式可以是CSS选择器

```
　　$(document) //选择整个文档对象

　　$('#myId') //选择ID为myId的网页元素

　　$('div.myClass') // 选择class为myClass的div元素

　　$('input[name=first]') // 选择name属性等于first的input元素
```

2. 选择表达式也可以是jQuery的特有表达式

```
　　$('a:first') //选择网页中第一个a元素

　　$('tr:odd') //选择表格的奇数行

　　$('#myForm :input') // 选择表单中的input元素

　　$('div:visible') //选择可见的div元素

　　$('div:gt(2)') // 选择所有的div元素，除了前三个

　　$('div:animated') // 选择当前处于动画状态的div元素
```
3. jQuery还具有选取特定元素的能力

```
　　$('div').has('p'); // 选择包含p元素的div元素

　　$('div').not('.myClass'); //选择class不等于myClass的div元素

　　$('div').filter('.myClass'); //选择class等于myClass的div元素

　　$('div').first(); //选择第1个div元素

　　$('div').eq(5); //选择第6个div元素

　　$('div').next('p'); //选择div元素后面的第一个p元素

　　$('div').parent(); //选择div元素的父元素

　　$('div').closest('form'); //选择离div最近的那个form父元素

　　$('div').children(); //选择div的所有子元素

　　$('div').siblings(); //选择div的同级（兄弟）元素
```
# jQuery的链式操作是怎样的

举个例子
```
　　$('div').find('h3').eq(2).html('Hello');
//找到div,选取其中的h3,选取h3里的第3个元素，将它的内容改为('Hello')
```
* jQuery链式操作实现的原理在于，jQuery操作返回的都是一个jQuery对象，我们可以紧接着对这个对象进行一系列操作


另外jQuery还提供了.end()操作方法,使得选取的对象后退一步。如下：

```
　　$('div')

　　　.find('h3')

　　　.eq(2)

　　　.html('Hello')

　　　.end() //退回到选中所有的h3元素的那一步

　　　.eq(0) //选中第一个h3元素

　　　.html('World'); //将它的内容改为World
```


# jQuery如何移动元素

jQuery提供了两组方法来操作元素在网页中的位置移动。一组方法是直接移动该元素，另一组方法是移动其他元素，使得目标元素达到我们想要的位置。

* 首先是这种操作模式的四种方法，共计四对

```
　　.insertAfter()和.after()：在现存元素的外部，从后面插入元素

　　.insertBefore()和.before()：在现存元素的外部，从前面插入元素

　　.appendTo()和.append()：在现存元素的内部，从后面插入元素

　　.prependTo()和.prepend()：在现存元素的内部，从前面插入元素
```
* 以第一组为例

```
　　$('div').insertAfter($('p')); 
    //第一种方法是使用.insertAfter()，把div元素移动p元素后面
　　$('p').after($('div')); 
    //第二种方法是使用.after()，把p元素加到div元素前面
```
值得一提的是（需要注意的是），这两种方法返回的不是同一个元素：第一种返回的是`div`元素而第二种返回的是`p`元素

# jQuery如何创建元素

jQuery创建元素很简单，以下为例
```
　　$('<p>Hello</p>');  // 创建<p>Hello</p>元素

　　$('<li class="new">new li</li>');  // 创建<li class="new">new li</li>元素

　　$('ul').append('<li>list item</li>'); //创建<li>list item</li>元素并添加到ul里面
```

# jQuery如何修改元素的属性

```
　　.html() 取出或设置html内容

　　.text() 取出或设置text内容

　　.attr() 取出或设置某个属性的值

　　.width() 取出或设置某个元素的宽度

　　.height() 取出或设置某个元素的高度

　　.val() 取出某个表单元素的值
```

jQuery很厉害的一点在于它可以使用同一个函数，来完成取值（getter）和赋值（setter），即"取值器"与"赋值器"合一，到底是取值还是赋值，由传入的参数决定

举例：
```
　　$('h1').html(); //html()没有参数，表示取出h1的值

　　$('h1').html('Hello'); //html()有参数Hello，表示对h1进行赋值
```





* 以上就是对现阶段jQuery学习的总结