---
# 常用定义
title: "CSS关于文本换行与不换行的那些事"          # 标题
date: 2020-07-07   # 创建时间
draft: false                       # 是否是草稿？
tags: ["CSS"]  # 标签
categories: ["CSS"]              # 分类
author: "Carri"                  # 作者
keywords: ["CSS"]
description: "CSS关于文本换行与不换行的那些事"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# 关于换行的样式属性

标粗的是常用属性，会在后面的场景里用到

#### 1.white-space: normal | pre | nowrap | pre-wrap | pre-line | inherit;

white-space 属性设置如何处理元素内的空白

设值说明：
 normal 默认。空白会被浏览器忽略。
 pre 空白会被浏览器保留。其行为方式类似 HTML 中的 pre 标签。
 **nowrap文本不会换行，文本会在在同一行上继续，直到遇到 br 标签为止。**
 pre-wrap 保留空白符序列，但是正常地进行换行。
 pre-line 合并空白符序列，但是保留换行符。
 inherit 规定应该从父元素继承 white-space 属性的值。

#### 2.word-wrap: normal|break-word;

word-wrap 属性用来标明是否允许浏览器在单词内进行断句，这是为了防止当一个字符串太长而找不到它的自然断句点时产生溢出现象。

设置说明：
 normal: 只在允许的断字点换行(浏览器保持默认处理)
 **break-word:在长单词或URL地址内部进行换行**

#### 3.word-break: normal|break-all|keep-all;

word-break 属性用来标明怎么样进行单词内的断句。

设值说明：
 normal：使用浏览器默认的换行规则。
 **break-all:允许在单词内换行**
 keep-all:只能在半角空格或连字符处换行

# 常见的场景

1. 强制文本一行展示，超出一行范围的文本用省略号表示；

2. 设置文本一行或多行（规定行）展示，超出规定范围的文本直接隐藏；

3. 自动换行，过长英文单词进行强制断行；

# 场景1

强制文本一行展示，超出一行范围的文本用省略号表示

```
// HTML
<p id="txt">爱是一盏灯，黑暗中照亮前行的远方；爱是一首诗，冰冷中温暖渴求的心房；爱是夏日的风，是冬日的阳，是春日的雨，是秋日的果。</p>

// CSS
#txt{
  border:1px solid red;
  
  width:300px;
  white-space:nowrap;
  overflow:hidden;
  text-overflow:ellipsis
}
```

[点击查看效果](https://jsbin.com/magalubive/edit?html,css,output)

# 场景2

设置文本一行或多行（规定行）展示，超出规定范围的文本直接隐藏；



场景2相对而言比较复杂，下面提供两种解决方式：

### 使用**WebKit的CSS扩展属性**，该方法**适用于WebKit浏览器及移动端**

[点击查看效果](https://jsbin.com/zufozuroba/edit?html,css,output)



注明：CSS3 text-overflow 属性主要有：clip，ellipsis，string。（不在本文讨论范围内）

- clip: 修剪文本
- ellipsis：显示省略号来代表被修剪的文本
- string:  显示给定的字符串来代表修剪的文本

### 利用**绝对定位和padding**;(**跨浏览器**解决方案)

[点击查看效果](https://jsbin.com/ceyebadacu/edit?html,css,output)

原理：首先在包含文字的元素里，**嵌入一个…**，然后包含文字的元素右侧留出…的位置(**padding-right**),最后利用**绝对定位**将…定位至右侧的padding-right区域。

# 场景3

自动换行，过长英文单词进行强制断行；

主要就是两个:

- word-wrap: 允许单词内断句，首先会尝试挪到下一行，看看下一行的宽度够不够，不够的话就进行单词内的断句 

```
word{
	word-wrap:break-word
}
```

- word-break: 断句时，不会把长单词挪到下一行，而是直接进行单词内的断句

```
.word{
	word-break:break-all;
}
```



[点击案例预览](https://jsbin.com/lemidifuqo/1/edit?html,css,output)



# 结语

其实这篇写这篇博客的原因是因为字节三面的时候问到了场景2的问题，当时实在憋不出来，思考得出后说应该用JavaScript处理字符串。。。虽然挂的原因方方面面，但是在这种问题上还是不应该出错。