---
# 常用定义
title: "CSS的Float,Flex,Grid布局"           # 标题
date: 2019-10-30    # 创建时间
draft: false                       # 是否是草稿？
tags: ["CSS布局","Float","Flex","Grid"]  # 标签
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

# 如何选取布局
![KIippn.md.png](https://s2.ax1x.com/2019/10/31/KIippn.md.png)

* 图片来自饥人谷

# Float布局
1. 子元素上加float:left和width
2. 父元素classname上加 clearfix
```
clearfix{
    content:'';
    display:block;
    clear:both;
}
```

# Flex布局
* container容器 
* items内容

```
.container{
    display:flex;
}
```

* 改变items流向`flex-direction`
* `flex-wrap:wrap`变为换行的，默认nowrap不换行
* `justify-content`flex-start,flex-end,center,space-between(分散，顶着两边),space-around(分散，两边有空),space-evenly(孔隙一样)
* `align-items`center(让次轴的items居中),flex-end(都往下顶)，stretch(无高度情况下内容会将它们拉伸的一样高)
* `items{order:1}`顺序，默认是0，可以为负
* `flex-grow`,`flex-shrink`变胖变瘦


重点：


*  `display:flex`
*  `flex-direction:row/column`
*  `flex-wrap:wrap`
*  `justify-content:center/space-between`
*  `align-items:center`


# Grid布局
![KIA74g.png](https://s2.ax1x.com/2019/10/31/KIA74g.png)

* `grid-row/column-start/end`从第几根线开始，第几根结束
* `grid-template-columns:1fr 1fr 2fr;`表示几份
* `grid-template-areas:"header header header" "aside main ad" ". footer footer"`可以写成名字，.代表空
```
items{
    grid-area:header
}
```
* `grid-column/row-gap`中间间隔