---
# 常用定义
title: "Vue冷门api之provide"          # 标题
date: 2021-06-11   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["Vue"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "Vue冷门api之provide"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# 常见的组件通信方式
* props
* eventBus
* ref与$children,$parent访问组件实例
* Vuex
* 以上不做赘述了

# provide/inject作用
我们先来看看官网的例子
```javascript
// 父级组件提供 'foo'
var Provider = {
  provide: {
    foo: 'bar'
  },
  // ...
}

// 子组件注入 'foo'
var Child = {
  inject: ['foo'],
  created () {
    console.log(this.foo) // => "bar"
  }
  // ...
}
```

我们顾名思义，provide(提供)就是指定我们想要**提供给**后代组件的数据或方法，而inject是用来**接受**provide提供的数据或方法。

其实这种方式很像React Hooks里的useContext，关于hooks的内容可以参见我之前的博客。

# 使用provide作全局状态管理
一般全局状态管理都用Vuex，其实provide也可以实现全局状态管理。

如何做全局状态管理呢？很多人可能想到在根组件中传入变量，然后在后代组件中使用。我们来通过这个例子看一看它的利弊
```
// 根组件提供一个非响应式变量给后代组件
export default {
  provide () {
    return {
      text: 'bar'
    }
  }
}

// 后代组件注入 'app'
<template>
	<div>{{this.text}}</div>
</template>
<script>
  export default {
    inject: ['text'],
    created() {
      this.text = 'baz' // 在模板中，依然显示 'bar'
    }
  }
</script>
```

首先，官方文档中有这样一句提示
```
提示：provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。
```


结合上面的例子，我们发现传入的text并不是响应式的（也就是说无法对provide提供的数据进行响应式处理，除非提供一个响应式的数据）


那么如何解决响应式的问题呢？很简单，我们可以将组件本身全部注入provide,此时我们就可以在后代中任意访问根组件中的所有状态。


来看下面这个例子
```javascript
// 根组件提供将自身提供给后代组件
export default {
  provide () {
    return {
      app: this
    }
  },
  data () {
    return {
      text: 'bar'
    }
  }
}

// 后代组件注入 'app'
<template>
	<div>{{this.app.text}}</div>
</template>
<script>
  export default {
    inject: ['app'],
    created() {
      this.app.text = 'baz' // 在模板中，显示 'baz'
    }
  }
</script>	
```

那么问题来了，我们可以直接通过this.$root来获取根组件为什么还要使用provide

答：使用provide可以在不同模块的入口组件传数据给各自的模块，如果放在根模块，很容易造成冲突。

# 慎用provide

既然 provide/inject 如此好用，那么，为什么 Vue 官方还要推荐我们使用 Vuex，而不是用原生的 API 呢？


我在前面提到过，Vuex 和 provide/inject 最大的区别在于，Vuex 中的全局状态的每次修改是可以追踪回溯的（使用Vue Devtools），而 provide/inject 中变量的修改是无法控制的，换句话说，你不知道是哪个组件修改了这个全局状态。


Vue 的设计理念借鉴了 React 中的单向数据流原则，而 provide/inject 明显破坏了单向数据流原则。试想，如果有多个后代组件同时依赖于一个祖先组件提供的状态，那么只要有一个组件修改了该状态，那么所有组件都会受到影响。这会导致数据的不可控。

# 总结
尽管provide/inject是一个冷门api，但是在特殊的情况下它仍有其作用。花了点时间了解了一下，感觉看到了不一样的风景

