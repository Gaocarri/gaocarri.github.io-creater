---
# 常用定义
title: "Vue基础（一）"           # 标题
date: 2019-12-13    # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["框架"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "Vue基础（一）"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

## Vue是一种渐进式框架，什么是渐进式框架
 渐进式框架：即可以在项目中部分使用Vue，不必非要完全使用

## Vue的MVVM

Vue是一种MVVM框架。（也有人说现在是一种MV*框架）

* View:DOM
* ViewModel:Vue(处理Vue和Model，Vue在这里起到了这样一个作用)
* Model:Plan JavaScript,Objects

## Vue的模板
```

<div id="app">
    <h2>{{message}}</h2>
</div>

<script>
    const app = new Vue({
        el:'#app', // 控制的元素
        data:{
            message:'Hello Vuejs'
        } ,//含有的数据
        methods:{

        }, //含有的方法，后面还有一系列生命周期函数
    })
</script>
```

好了，进入正题

## 1. 列表展示和计数器案例
列表展示案例：
```
    <div id="app">
        <ul>
            <li v-for="movie in movies">{{movie}}</li>
        </ul>

    </div>

    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                movies: ['海王', '蜘蛛侠', '蝙蝠侠', '肖申克的救赎']
            }
        })
    </script>
```

计数器案例：
```
<div id="app">
        <h3>当前计数：{{counter}}</h3>
        <button v-on:click="add">加1</button>
        <button v-on:click="sub">减1</button>
    </div>

    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                counter: 0
            },
            methods: { //类型key(string):Function()
                add() {
                    this.counter++ //一定要加this
                },
                sub() {
                    this.counter--
                }
            }
        })
    </script>
```

## 2. Vue的插值操作

何为插值操作？

* 上述的#app里面的{{message}}已经交由Vue控制了，这是Vue的Mustache语法,它显示的值就是`data`里的`message`

1. v-once
```
    <div id="app">
        <h2>{{message}}</h2>
        <h2 v-once>{{message}}</h2>
        <!-- 使用了v-once插入的值只会改变一次 -->
    </div>
```

2. v-html
```
    <div id="app">
        <h2>{{url}}</h2>
        <!--这里的url会是字符串-->
        <h2 v-html="url"></h2>
        <!--这里会包含html语法并且覆盖<h2>标签内的所有内容-->
    </div>
    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                url: '<a href="http://www.baidu.com">百度一下</a>'
            }
        })
    </script>
```

3. v-text
    
    * 用法同`v-html`,但是它不包含html用法，会覆盖标签内的全部内容，一般很少使用

4. v-pre

```
<h2 v-pre>{{message}}</h2>
```
这里显示的解果是{{message}}，原封不动的显示


5. v-cloak(cloak斗篷的意思)
```
    <style>
        [v-cloak] {
            display: none;
        }
    </style>
        <div id="app" v-cloak>
        <!--v-cloak加上style标签里的东西可以使内容在加载Vue之前不显示，加载完vue后删除v-cloak-->
        {{message}}
    </div>
    <script src="../js/vue.js"></script>
    <script>
        setTimeout(function() {
            const app = new Vue({
                el: '#app',
                data: {
                    message: '你好啊'
                }
            })
        }, 3000)
    </script>
```

## 3. v-bind绑定

举例：
```
    <div id="app">
        <!-- 错误的做法
        <img src="{{imgURL}}" alt=""> -->
        <img :src="imgURL" alt="">
    </div>
    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                imgURL: 'https://cn.vuejs.org/images/logo.png'
            }
        })
    </script>
```

1. v-bind绑定class
    
    * 对象语法
```
    <div id="app">
        <!-- 对象语法 -->
        <h2 class="title" v-bind:class="{active:isActive,line:isLine}">{{message}}</h2>
        <!-- 还可以放到methods里面,也可以放data里 -->
        <h2 class="title" v-bind:class="getClasses()">{{message}}</h2>
        <button @click="change">变色</button>
    </div>

    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                isActive: true,
                isLine: false
            },
            methods: {
                change() {
                    this.isActive = !this.isActive
                },
                getClasses() {
                    return {
                        active: this.isActive,
                        line: this.isLine
                    }
                }
            }
        })
    </script>
```

   * 数组语法(用的少)
```
    <div id="app">
        <!-- 这是字符串 -->
        <h2 :class="['active','line']">{{message}}</h2>
        <!-- 这是变量 -->
        <h2 :class="getClasses()">{{message}}</h2>
    </div>

    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                active: 'aaa',
                line: 'bbb'
            },
            methods: {
                getClasses() {
                    return [this.active, this.line]
                }
            }
        })
```   


2. v-bind绑定字符串

    * 对象语法
```
    <div id="app">
        <!-- <h2 :style="{key(css属性名):value（属性值）}">{{message}}</h2> -->
        <!-- 50px必须加上单引号 -->
        <!-- <h2 :style="{fontSize: '50px'}">{{message}}</h2> -->
        <h2 :style="{fontSize: finalSize+'px',color:finalColor}">{{message}}</h2>
        <h2 :style="styleObject">{{message}}</h2>

    </div>

    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                finalSize: 100,
                finalColor: 'red',
                styleObject: {
                    fontSize: '100px',
                    color: 'red'
                }
            }
        })
    </script>
```

   * 数组语法（用的少）
```
    <div id="app">
        <h2 :style="[baseStyle]">{{message}}</h2>
    </div>

    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                baseStyle: {
                    backgroundColor: 'red'
                }
            }
        })
    </script>
```

4. 计算属性
 * 记住计算属性有缓存，而methods没有（更占内存）

举例,通过计算属性计算`books`的总价格
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
    <div id="app">
        <h2>总价格：{{totalPrice}}</h2>
    </div>

    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                books: [{
                    id: 1,
                    name: 'Unix编程艺术',
                    price: 100
                }, {
                    id: 2,
                    name: '代码大全',
                    price: 180
                }, {
                    id: 3,
                    name: '深入李杰计算机原理',
                    price: 95
                }, {
                    id: 4,
                    name: '现代操作系统',
                    price: 70
                }]
            },
            computed: {
                totalPrice() {
                    let result = 0;
                    //ES5的方法 for 循环
                    // for (let i = 0; i < this.books.length; i++) {
                    //     result += this.books[i].price
                    // }

                    //ES6

                    for (let book of this.books) {
                        result += book.price
                    }
                    return result
                }
            }
        })
    </script>
</body>

</html>
```

Vue的第一天学习就这样了，争取半个月搞定Vue!!奥力给