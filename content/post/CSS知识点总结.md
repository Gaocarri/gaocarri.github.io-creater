---
# 常用定义
title: "CSS知识总结"           # 标题
date: 2019-10-31    # 创建时间
draft: false                       # 是否是草稿？
tags: ["CSS基础","浏览器渲染原理","盒模型"]  # 标签
categories: ["CSS"]              # 分类
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


# 一、浏览器渲染原理

## 参考文章
* Google团队写的文章


1. [渲染树构建、布局及绘制](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction)
2. [渲染性能](https://developers.google.com/web/fundamentals/performance/rendering/)
3. [使用transform来实现动画](https://developers.google.com/web/fundamentals/performance/rendering/stick-to-compositor-only-properties-and-manage-layer-count)


* 查看CSS各属性触发什么


1. [CSSTriggers.com](https://csstriggers.com/)
   

## 浏览器渲染过程

1. 根据HTML构建HTML树(DOM)
2. 更具CSS构建CSS树(CSSOM)
3. 将两棵树合并成一棵渲染树(render tree)
4. Layout布局(文档流、盒模型、计算大小和位置)
5. Paint绘制(把边框颜色、文字颜色、阴影等画出来)
6. Compose合成(更具层叠关系展示画面)


## 三种样式的更新方式

1. JS/CSS > 样式 > 布局 > 绘制 > 合成
2. JS/CSS > 样式 > 绘制 > 合成
3. JS/CSS > 样式 > 合成

* 如何知道如何更新？ 使用开发者工具自己尝试或者访问[CSSTriggers.com](https://csstriggers.com/)

* 流程越少，性能越好。

* 资料来源：饥人谷



# 二、CSS动画的两种做法(transition和animation)

* 它们的优点：没有repaint过程，比使用left(改变属性)性能好

## transition（过渡）

```
#xxx {
    transition:all 1s ease-in-out 3s;
}
```
* 配合`transform`属性使用，它的用法[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform)
* `all`表示需要需要过渡的属性，可以是`width` `background`等，不是所有元素都能过渡。特别注意: `display:block>none`无法过渡，但是`visibility:visible>hidden`可以过渡
* `1s`表示过渡完整的时间
* `ease-in-out`表示动画的过渡方式，还有`linear` `ease`等
* `3s`是指3s后才开始动画
* 其他属性用法参考MDN
  

## animation(动画)
```
@keyframes xxx {
    from {
        transform:translateX(0%);
    }
    to {
        transform:tanslateX(100%);
    }
}
```
或者
```
@keyframes xxx{
    0% {top:0;left:0;}
    30%{top:50px;}
    100%{top:100px;left:100%}
}
```

* `keyframes`关键帧，使用animation即设定关键帧。


```
#xxx {
    animation:1.5s xxx linear infinite alternate-reverse;
}
```

* `1.5s`指过渡的时间
* `xxx`进行动画的id名
* `linear`过渡效果，同transition
* `infinite`表示过渡的次数无限大,也可以是`10`次等具体次数
* `alternate-reverse`变换相反进行动画，如果是`forwards`会停在动画的最后一帧，具体参考MDN


# 三、CSS其他知识点

## CSS盒模型
* CSS盒模型分为两种，一种是content-box,一种是border-box,它们的区别在于content-box的高和宽只包含content,而 border-box的高和宽包含border、padding、content
* 一般使用border-box，因为border-box更好用

## 外边距合并
* 同一父亲的两个孩子的外边距会合并(float或者变为inline-block就不会合并)
* 父子外边距合并，只有上下合并，左右不合并。margin和margin合并的前提是parent和children之间parent没有border,padding或者加overflow：hidden

## position定位
* static 默认值
* relative 相对定位，升起来，但不脱离文档流（占的位子不变，但是显示的位子变了）
* absolute 绝对定位，定位基准是祖先中最近的定位非static的元素 left:50%加负margin可实现居中
* fixed 固定定位，定位基准是视口viewport(有诈) 加了transform就变了，手机上不要用(bug太多)
* sticky 粘滞定位（会让它粘在屏幕的最上边），兼容性极差。

## 如何创建层叠上下文，z-index
* 可以使z-index不为auto,并加上定位relative或者absolute
* 只要定位position：fixed 就会创建
* opacity 不为1时
* 有transform
* flex,grid里index不是auto
