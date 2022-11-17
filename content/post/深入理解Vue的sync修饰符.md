---
# 常用定义
title: "深入理解Vue的sync修饰符"          # 标题
date: 2020-04-15   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["Vue"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "深入理解Vue的sync修饰符"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

## 说明

官方文档对.sync修饰符的解释：[https://cn.vuejs.org/v2/guide/components-custom-events.html#sync-%E4%BF%AE%E9%A5%B0%E7%AC%A6](https://cn.vuejs.org/v2/guide/components-custom-events.html#sync-修饰符)

首先，一句话来概括.sync修饰符的作用：它会被扩展成一个自动更新父组件属性的v-on监听器（它作为一个编译时的语法糖存在）

来看以下示例,父组件当中：

```vue
<Child :money.sync = "total">
```

它会被扩展成($event储存子组件传入事件的参数)

```vue
<Child :money="total" @update:money="total = $event">
```

## 示例

上面的解释可能太过浅显，我们来看一个例子



这个例子是这样的

- 父亲（父组件）答应给儿子（子组件）1000块钱，但是这1000块钱是放在父亲那里的
- 儿每花掉100，父亲那里存放的钱就会-100



代码如下：

父组件

```vue
<template>
  <div id="app">
    <span>{{total}}</span>
    <Child :money.sync="total"/>
  </div>
</template>

<script>
import Child from "./components/Child";

export default {
  name: "App",
  components: {
    Child
  },
  data() {
    return {
      total: 1000
    };
  }
};
</script>
```

子组件

```vue
<template>
  <div>
    <span>儿子的零花钱是{{money}}</span>
    <button @click="$emit('update:money',money-100)">花掉100元</button>
  </div>
</template>

<script>
export default {
  name: "Child",
  props: ["money"]
};
</script>
```



初始状态

![](/images/花钱初始.png)

花钱结束（点击花掉100元后）

![](/images/花钱结束.png)

## 总结

vue修饰符sync的作用就是当一个子组件改变了一个prop的值时，这个变化也会同步到父组件上。如果不用.sync，想要实现上面示例的功能，我们也可以用v-on监听子组件传过来的事件，但是毫无疑问使用.sync会更加简洁

但是官方文档中提到有两点需要注意，这里直接展示了

- 注意带有 `.sync` 修饰符的 `v-bind` **不能**和表达式一起使用 (例如 `v-bind:title.sync=”doc.title + ‘!’”` 是无效的)。取而代之的是，你只能提供你想要绑定的属性名，类似 `v-model`。

- 将 `v-bind.sync` 用在一个字面量的对象上，例如 `v-bind.sync=”{ title: doc.title }”`，是无法正常工作的，因为在解析一个像这样的复杂表达式的时候，有很多边缘情况需要考虑。