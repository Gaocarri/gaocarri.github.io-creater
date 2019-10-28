---
# 常用定义
title: "HTML常用标签"           # 标题
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
# 一.a标签的用法
```
<a href="" target="_blank" download>
```
## 1.1属性
1. href
2. target
3. download
4. rel=noopener


## 1.2 href
### 1.2.1 href的取值
1.网址

  * https:// google.com
  * http:// google.com
  * //google.com

一般情况下用//google.com就可以了，浏览器会自动将其补全为https://www.google.com

2.路径

  * /a/b/c以及a/b/c
  * index.html以及./index.html

其中/a/b/c中的第一个/指的是http服务的根目录，即使用终端打开得到的结果即是HTML文件相同目录下的a/b/c。

index.html和./index.html指的都是HTML文件相同目录下的index.html


  3.伪协议

  * javascript:这里写入Js代码;
  * mailto:邮箱;
  * tel:电话;

点击即会运行写入的Js代码，如果不写，点击页面不会有任何变化

注意：如果取值为#会回到页面最上部，取值为“”会刷新页面（根据Chrome的调试工具Network得到）


4.id

* href=“#XXX”即跳到当前页面中id为XXX的标签

### 1.2.2 href的作用
* 跳转外部页面
* 跳转内部锚点
* 跳转到邮箱或者电话

## 1.3 target
### 1.3.1 target的取值
1.self

* 在当前页面跳转

2.blank

* 在新的空白页面跳转

3.top

* 如果top存在于iframe里，就会在引用iframe的主页面中跳转

4.parent

* 如果parent存在于最里面被引用的iframe里，则会在最里层的iframe的上一层iframe/页面中跳转

## 1.4 download
* 许多浏览器不支持，理论上使用这个属性会下载此页面
### 1.5 rel=noopener
* 这个属性能解决Js的一个bug





# 二.img标签的用法
```
<img src="XXX.png" alt="这是一张图片" width="100px" height="100px">
```
## 2.1作用
* 发出get请求，展示一张图片
  
## 2.2 属性
1. src
2. alt
3. height/width

## 2.3 src
* source的简称，路径用法与a标签相同，加载选择的图片

## 2.4 alt
* 当图片加载失败时，会显示alt表示的文字

## 2.5 height/width
* 图片的高和宽，当只指定其中一个属性时，图片的另一个属性会自适应
* 注意：不要同时指定它的宽和高，会导致图片变形

## 2.6 事件
* onload
* onerror
```
  id.onerror = {
    console.log('图片加载失败')
    id.src = '404.png'
  }
  id.onload = {
    console.log('图片加载成功)
  }
```
可以用来在图片加载失败时添加一个404的图片作为替代

## 2.7 响应式
* 使其自适应手机屏幕确保图片的内容全部显示
```
  img {
    max-width:100%
  }
```

# 三. table标签的用法
```
    <table>
        <thead>
            <tr>
                <th>1</th>
                <th>2</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>我</td>
                <td>你</td>
            </tr>
            <tr>
                <td>她</td>
                <td>他</td>
            </tr>
        </tbody>
        <tfoot>
            <tr>
                <td>我她</td>
                <td>你他</td>
            </tr>
        </tfoot>
    </table>
```
## 3.1 table包含的标签
* table
* thead
* tbody
* tfoot
* th
* td
* tr

## 3.2 table的属性
* table-layout:具有auto和fixed两种值，分别对应根据浏览器划定的(根据字符宽度)的和平均的
* border-collapse:为collapse就是合并边框的意思 默认不合并
* border-spacing:border之间的距离
