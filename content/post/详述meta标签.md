---
# 常用定义
title: "详述meta标签"          # 标题
date: 2020-07-04   # 创建时间
draft: false                       # 是否是草稿？
tags: ["HTML"]  # 标签
categories: ["HTML"]              # 分类
author: "Carri"                  # 作者
keywords: ["HTML"]
description: "详述meta标签"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

之前没怎么在意过meta，结果连charset都不知道，属实丢人。
于是查阅了很多资料，大体列举了一些常见的meta标签.

# meta标签是什么
meta常用于定义**页面的说明，关键字，最后修改日期**，和其它的元数据。这些元数据将服务于浏览器（如何布局或重载页面），搜索引擎和其它网络服务。

## meta标签的属性分类
直接参考MDN：

* 如果设置了 charset 属性，meta 元素是一个字符集声明，告诉文档使用哪种字符编码。
* 如果设置了 name 属性，meta 元素提供的是文档级别（document-level）的元数据，应用于整个页面。
* 如果设置了 http-equiv 属性，meta 元素则是编译指令，提供的信息与类似命名的HTTP头部相同。
* 如果设置了 itemprop 属性，meta 元素提供用户定义的元数据。

这里主要讨论前三种

# charset
```
<meta charset="UTF-8">
```

UTF-8是编码规则，这个标签告诉文档使用UTF-8编码。具体UTF-8是什么不在讨论范畴

# name属性
name属性主要用于描述网页，比如网页的关键词，叙述等。与之对应的属性值为content，content中的内容是对name填入类型的具体描述，便于搜索引擎抓取。

具体的语法格式
```
<meta name="参数" content="具体的描述">。
```

具体常用属性列举以下几种：

## 1.keywords(关键字)

说明：用于告诉搜索引擎，你网页的关键字。
举例：
```
<meta name="keywords" content="gaocarri,前端">
```
## 2.description(描述)

说明：用于告诉搜索引擎，你网站的主要内容。
举例：
```
<meta name="description" content="Gacarri的前端博客">
```
## 3.viewport

说明：这个概念比较复杂，有空再写一下
举例:
```
<meta name="viewport" content="width=device-width, initial-scale=1">
```
## 4.其他

author(作者) copyright(版权) renderer(双核浏览器渲染方式)...
```
<meta name="renderer" content="webkit"> //默认webkit内核
<meta name="renderer" content="ie-comp"> //默认IE兼容模式
<meta name="renderer" content="ie-stand"> //默认IE标准模式
```

# http-equiv

这个主要是用来定义一些http参数的

具体的语法格式
```
<meta http-equiv="参数" content="具体的描述">
```

主要有以下几个参数：

## 1.content-Type(设定网页字符集)(推荐使用HTML5的方式)

说明：用于设定网页字符集，便于浏览器解析与渲染页面
举例：

```
<meta http-equiv="content-Type" content="text/html;charset=utf-8">  //旧的HTML，不推荐

<meta charset="utf-8"> //HTML5设定网页字符集的方式，推荐使用UTF-8
```

## 2.X-UA-Compatible(浏览器采取何种版本渲染当前页面)

说明：用于告知浏览器以何种版本来渲染页面。（一般都设置为最新模式，在各大框架中这个设置也很常见。）
举例：

```
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"/> //指定IE和Chrome使用最新版本渲染当前页面
```

## 3.cache-control(指定请求和响应遵循的缓存机制)
用法1.
说明：指导浏览器如何缓存某个响应以及缓存多长时间。

举例:
```
<meta http-equiv="cache-control" content="no-cache">
```
共有以下几种用法：

no-cache: 先发送请求，与服务器确认该资源是否被更改，如果未被更改，则使用缓存。

no-store: 不允许缓存，每次都要去服务器上，下载完整的响应。（安全措施）

public : 缓存所有响应，但并非必须。因为max-age也可以做到相同效果

private : 只为单个用户缓存，因此不允许任何中继进行缓存。（比如说CDN就不允许缓存private的响应）

maxage : 表示当前请求开始，该响应在多久内能被缓存和重用，而不去服务器重新请求。例如：max-age=60表示响应可以再缓存和重用 60 秒。


用法2.(禁止百度自动转码)
说明：用于禁止当前页面在移动端浏览时，被百度自动转码。虽然百度的本意是好的，但是转码效果很多时候却不尽人意。所以可以在head中加入例子中的那句话，就可以避免百度自动转码了。
举例：
```
<meta http-equiv="Cache-Control" content="no-siteapp" />
```
## 4.expires(网页到期时间)
说明:用于设定网页的到期时间(格林尼治时间)，过期后网页必须到服务器上重新传输。
举例：
```
<meta http-equiv="expires" content="Sunday 26 May 2020 01:00 GMT" />
```
## 5.refresh(自动刷新并指向某页面)
说明：网页将在设定的时间内，自动刷新并调向设定的网址。
举例:
```
<meta http-equiv="refresh" content="2；URL=http://gaocarr.top/"> //意思是2秒后跳转向我的博客
```
## 6.Set-Cookie(cookie设定)
说明：如果网页过期。那么这个网页存在本地的cookies也会被自动删除。
```
<meta http-equiv="Set-Cookie" content="name, date"> //格式

<meta http-equiv="Set-Cookie" content="User=Lxxyx; path=/; expires=Sunday, 10-Jan-16 10:00:00 GMT"> //具体范例
```

总之常见的就这些了