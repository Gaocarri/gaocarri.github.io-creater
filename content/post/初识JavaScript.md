---
# 常用定义
title: "初识JavaScript"           # 标题
date: 2019-11-01   # 创建时间
draft: false                       # 是否是草稿？
tags: ["JS","Javascript","javascript入门"]  # 标签
categories: ["JavaScript"]              # 分类
author: "Carri"                 # 作者

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---


## JavaScript的诞生(参考[阮一峰的博客](http://www.ruanyifeng.com/blog/2011/06/birth_of_javascript.html))
1. 诞生: JavaScript由Brendan Eich于1995年发明（只花了十天）。
2. 目的: 网景公司需要一种网页脚本语言，使得浏览器可以与网页互动。
3. 为什么叫JavaScript: 网景公司与发明Java的Sun公司合作，为了蹭上Java的热度，给这种脚本语言命名为JavaScript。
4. JavaScript的设计思想：
    * 借鉴C语言的基本语法；
    * 借鉴Java语言的数据类型和内存管理；
    * 借鉴Scheme语言，将函数提升到"第一等公民"（first class）的地位；
    * 借鉴Self语言，使用基于原型（prototype）的继承机制。

这导致JavaScript的语言风格 = （简化的）函数式编程 + （简化的）面向对象编程

## JavaScript的发展

### JS的十大设计缺陷（参考[阮一峰的博客](http://www.ruanyifeng.com/blog/2011/06/10_design_defects_in_javascript.html)）
1. 不适合开发大型程序
2. 非常小的标准库
3. null和undefined
4. 全局变量难以控制
5. 自动插入行尾分号
6. 加号运算符
7. NaN
8. 数组和对象的区分
9. == 和 ===
10. 基本类型的包装对象

* 如果遵守良好的编程规范，加上第三方函数库的帮助，Javascript的这些缺陷大部分可以回避

### ECMAScript标准
* JS和ES的关系
    1. JS是浏览器的实现，ES是纸上的标准
    2. 纸上的标准往往落后于浏览器的实现，先实现，再写入标准
* 1999年发布的ES3使用最为广泛
* 2015年ES6发布
* 之后每年发布一版，版本号以年份命名。
