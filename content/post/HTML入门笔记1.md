---
# 常用定义
title: "我的第一篇博客"           # 标题
date: 2019-10-28    # 创建时间
draft: false                       # 是否是草稿？
tags: ["HTML","笔记"]  # 标签
categories: ["HTML"]              # 分类
author: "Carri"                  # 作者

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: false       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# HTML是谁发明的

Timothy John Berners-Lee爵士，英国计算机科学家，万维网的发明者


# HTML起手应该写什么

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    
</body>
</html>
```
* `DOCTYPE`文档类型
* `html`代表html5
* `lang="en"`代表语言是英文，`zh-CN`是中文
* `UTF-8`文档的字符类型
* `width=device-width`禁用缩放
* `X-UA-Compatible`兼容手机
* `content="ie=edge"`告诉IE使用最新的内核
* ` <title>Document</title>`标题

# 常用的表示文章/书层级的标签

* 标题`h1~h6`
* 章节`section`
* 文章`article`
* 段落`p`
* 头部`header`
* 脚步`footer`
* 主要内容`main`
* 旁支内容`aside`
* 划分`div`


# 全局属性
`class` `contenteditable` `hidden` `id` `style` `tabindex` `title`


# 内容标签
* 无序列表`ol+li`
* 有序列表`ul+li`
* 描述列表、描述对象、描述数据`dl+dt+dd`
* 使代码中的空格和换行等正常显示`pre`
* 水平分界线`hr`
* 换行`br`
* 链接anchor的缩写`a`
* 斜体,表示强调`em`
* 加粗`strong`
* 呈现一段计算机代码. 默认情况下, 它以浏览器的默认等宽字体显示`code`
* quote引用(会加双引号)，blockquote块级引用`q` `blockquote`