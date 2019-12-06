---
# 常用定义
title: "如何优化DOM操作来优化性能"           # 标题
date: 2019-11-11    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JavaScript","JS","DOM"]  # 标签
categories: ["JavaScript"]              # 分类
author: "Carri"                  # 作者
keywords: ["JavaScript","JS","DOM"]
description : "如何优化DOM操作来优化性能"

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# 为什么DOM操作很慢

* 一句话概括：DOM对象本身也是一个js对象，所以严格来说，并不是操作这个对象慢，而是说操作了这个对象后，会触发一些浏览器行为，比如布局（layout）和绘制（paint）。


* 用文字描述浏览器呈现一张页面的过程：
  1. 解析HTML，并生成一棵DOM tree
  2. 解析各种样式并结合DOM tree生成一棵Render tree
  3. 对Render tree的各个节点计算布局信息，比如box的位置与尺寸
  4. 根据Render tree并利用浏览器的UI层进行绘制


* 浏览器触发layout的情况
    1. 通过js获取需要计算的DOM属性
    2. 添加或删除DOM元素
    3. resize浏览器窗口大小
    4. 改变字体
    5. css伪类的激活，比如:hover
    6. 通过js修改DOM元素样式且该样式涉及到尺寸的改变


# 几种优化方案

## 批量读写方案
对于一段这样的代码

```
// Read
var h1 = element1.clientHeight;

// Write (invalidates layout)
element1.style.height = (h1 * 2) + 'px';

// Read (triggers layout)
var h2 = element2.clientHeight;

// Write (invalidates layout)
element2.style.height = (h2 * 2) + 'px';

// Read (triggers layout)
var h3 = element3.clientHeight;

// Write (invalidates layout)
element3.style.height = (h3 * 2) + 'px';
```


其中`clientHeight`这个属性是计算得到的，每计算一次，浏览器就会进行一次layout

下面给出一种优化这段代码的方法，只需要预先读取所需要的属性，在一起修改即可：

```
// Read
var h1 = element1.clientHeight;
var h2 = element2.clientHeight;
var h3 = element3.clientHeight;

// Write (invalidates layout)
element1.style.height = (h1 * 2) + 'px';
element2.style.height = (h2 * 2) + 'px';
element3.style.height = (h3 * 2) + 'px';
```


## 先操作属性，再添加回Render Tree

以下列代码为例

```
var fragment = document.createDocumentFragment();
for (var i=0; i < items.length; i++){
  var item = document.createElement("li");
  item.appendChild(document.createTextNode("Option " + i);
  fragment.appendChild(item);
}
list.appendChild(fragment);
```

核心思想就是先对一个不在Render tree上的节点进行一系列操作，再把这个节点添加回Render tree，这样无论多么复杂的DOM操作，最终都只会触发一次layout。


## 面对样式修改
* 针对样式的改变，我们首先需要知道并不是所有样式的修改都会触发layout，因为我们知道layout的工作是计算RenderObject的尺寸和大小信息，那么我如果只是改变一个颜色，是不会触发layout的。

特别提一下JS动画,比如以下这段代码
```
function animate (from, to) {
  if (from === to) return

  requestAnimationFrame(function () {
    from += 5
    element1.style.height = from + "px"
    animate(from, to)
  })
}

animate(100, 500)
```

动画的每一帧都会导致layout，这是无法避免的，但是为了减少动画带来的layout的性能损失，可以将动画元素绝对定位，这样动画元素脱离文本流，layout的计算量会减少很多。


## 使用requestAnimationFrame

* window.requestAnimationFrame() 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行(MDN)
* 任何可能导致重绘的操作都应该放入requestAnimationFrame
* 在现实项目中，代码按模块划分，很难像上例那样组织批量读写。那么这时可以把写操作放在


requestAnimationFrame的callback中，统一让写操作在下一次paint之前执行。

```
// Read
var h1 = element1.clientHeight;

// Write
requestAnimationFrame(function() {
  element1.style.height = (h1 * 2) + 'px';
});

// Read
var h2 = element2.clientHeight;

// Write
requestAnimationFrame(function() {
  element2.style.height = (h2 * 2) + 'px';
});
```

## 一些个人感想

或许这就是更深层次点的东西，写出代码容易，写好难。



