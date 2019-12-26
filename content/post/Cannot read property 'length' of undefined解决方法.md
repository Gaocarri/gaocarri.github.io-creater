---
# 常用定义
title: "[Vue warn]: Error in render: TypeError: Cannot read property 'length' of undefined解决方法"           # 标题
date: 2019-12-26    # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["Vue"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "[Vue warn]: Error in render: TypeError: Cannot read property 'length' of undefined解决方法"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax

---

今天在使用vue做项目的时候遇到了一个错误，不断尝试后终于找到了解决方法，顺便举一反三一波

![错误信息](/images/191226.png)

## 错误及其解决方法

我给一个组件的子组件传递了一个对象,它的结构大概是这种形式(goods里有一个shopInfo对象，shopInfo对象里有一个数组)

```
goods：{
    ItemInfo：{...},
    shopInfo:{
            services:[obj1,obj2,obj3,obj4]
        },
    ...
}
```

`obj4`是一个对象，里面有一个属性`name`，现在，我在子组件中想使用这个`name`属性,就出现了上面的报错提示，但是奇怪的是页面中成功显示了这个`name`

```
<span>{{goods.shopInfo.services[goods.shopInfo.services.length-1].name}}</span>
```

先说解决报错的方法,给span加上v-if判断`goods.shopInfo.services`的存在

```
<span v-if="goods.shopInfo.services">{{goods.shopInfo.services[goods.shopInfo.services.length-1].name}}</span>
```

## 举一反三
### 子组件接受到父组件传入值的时间

为了探寻报错的原因，我在子组件的生命周期函数`created`,`mounted`,`updated`分别`console.log(goods.shopInfo.services)`,结果只有`updated`函数中打印出了这个对象，其余两个都是undefined,同样的`console.log(goods)`只有`updated`里访问到了这个对象。

**以上log说明子组件接受到父组件`props`来的对象发生在`mounted`之后！！**

### Vue的mustache语法里的特殊符号[]

为什么html中`{{goods.shopinfo.services}}`没有问题而`{{goods.shopinfo.services[goods.shopInfo.services.length-1].name}}`就报错呢？

原因就出在这个`[]`里面，页面在数据更新的时候会替换掉`{{}}`，但是更新之前`{{}}`含有`[]`符号，浏览器会直接进行解析，而`goods.shopInfo.service`还不存在，所以根本没有`length`，故发出警告,页面更新之后`length`存在，于是数据正常更新出来

由于现在也是刚刚开始熟悉vue，对底层原理了解还不是很深入，如果这段写错了，还请批评指正，在下方给我留言，学习学习