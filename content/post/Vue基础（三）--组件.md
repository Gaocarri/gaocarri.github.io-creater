---
# 常用定义
title: "Vue基础(三)--组件相关"          # 标题
date: 2019-12-20   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["Vue"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "Vue基础（三）"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

包含以下几个部分

* 组件注册
* 组件通信
* 父子组件相互访问
* 插槽

## 全局组件和局部组件的注册

### 全局组件

直接上代码,这样即注册了一个全局组件
```
Vue.component('cpn',{
                template: `
                <div>
                        <h2>我是标题</h2>
                        <p>我是内容,hhh</p>
                        <p>我是内容,hehehe</p>
                </div>`
            })
```

### 局部组件

也直接上代码了,此时相当于在app内部注册了一个组件，我们只能在该app中使用它
```
// 1.创建组件构造器
        const cpnC = Vue.extend({
                template: `
             <div>
            <h2>我是标题</h2>
            <p>我是内容</p>
        </div>
            `
            })
            // 2.注册局部组件
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊'
            },
            components: { //局部组件
                //cpn使用组件时的标签名,cpnC组件构造器
                cpn: cpnC
            }
        })
```

### 组件模板的分离写法
有时template过于冗长，我们需要将其中包含的html代码写在组件外面，此时有两种写法
```
<!-- 1. script标签，注意：类型必须是text/x-template -->
    <script type="text/x-template" id="cpn">
        <div>
            <h2>我是标题</h2>
            <p>我是内容</p>
        </div>
    </script>
<!-- 2. template标签 -->
    <template id="cpn">
        <div>
            <h2>{{title}}</h2>
            <h2>我是标题</h2>
            <p>我是内容11</p>
        </div>
    </template>

// 注册组件，引用template模板
Vue.component('cpn', {
            template: `#cpn`,
            data() {
                return {
                    title: 'abc'
                }
            }
        })
```

### 关于组件中的data为什么是函数

一句话概括：如果data不是一个函数，各个组件实例会相互影响（因为它们共同指向了一个对象），是函数的话每次都会返回一个新的对象。

* 如果组件中的data写成对象的形式会报错

## 组件通信

### 父传子

还是直接在代码中解释吧
```
// 1.父传子
<div id="app">
        <!-- 不用v-bind的话会将movies和message当作字符串来寻找 -->
        <cpn :cmovies="movies" :cmessage="message"></cpn>
</div>

<script>
        const cpn = {
            template: '#cpn',
            // props: ['cmovies', 'cmessage']
            props: {
                // 1.类型限制
                // cmovies: Array,
                // cmessage: String

                // 2.提供一些默认值
                cmessage: {
                    type: [String, Number], //也可以是自定义类型
                    default: 'aaaaa',
                    required: true //表示必须传值，否则报错
                },
                cmovies: {
                    //类型是对象或者数组时，默认值必须是一个函数
                    type: Array,
                    default () {
                        return [1, 2, 3]
                    }
                }
            }
        }
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                movies: ['海王', '海贼王', '海尔兄弟']
            },
            components: {
                cpn
            }
        })
</script>        
```

#### props中的驼峰标识问题
```
<div id="app">
    <cpn :c-info="info" :child-my-message="message"></cpn>
</div>

<script>
        const cpn = {
            template: '#cpn',
            props: {
                cInfo: {
                    type: Object,
                    default () {
                        return {}
                    }
                },
                childMyMessage: {
                    type: String,
                    default: '11'
                }
            }
        }
</script>
```

代码如上，html标签中要传给子组件的数据必须以`-`连接，而子组件`props`中必须以驼峰命名的方式来写


### 子传父

#### 关键词`$emit`
```
// 在子组件的方法中定义this.$emit(事件名)
    const cpn = {
        template: '#cpn',
        props: {
            cInfo: {
                type: Object,
                default () {
                    return {}
                }
            },
            childMyMessage: {
                type: String,
                default: '11'
            }
        }
    }

// 父组件接受
   <div id="app">
        <h2>{{count}}</h2>
        <cpn @decrement-click="add" @increment-click="increase"></cpn>
    </div>
```

#### 参数问题

来看以下一段代码,注意默认传递的参数以及this.$emit()里的不同
```
    <!-- 父组件模板 -->
    <div id="app">
        <!-- 默认会把item传给cpnClick作参数，而不是一般情况下的默认传浏览器的evnt -->
        <cpn @item-click="cpnClick"></cpn>
    </div>

    <!-- 子组件模板 -->
    <template id="cpn">
        <div>
            <button v-for="item in categories" @click="btnClick(item)">{{item.name}}</button>
        </div>
    </template>

    <script src="../js/vue.js"></script>
    <script>
        const cpn = {
            template: '#cpn',
            data() {
                return {
                    categories: [{
                        id: 'aaa',
                        name: '热门推荐'
                    }, {
                        id: 'bbb',
                        name: '手机数码'
                    }, {
                        id: 'ccc',
                        name: '家用家电'
                    }]
                }
            },
            methods: {
                btnClick(item) {
                    this.$emit('item-click', item)
                }
            }
        }

        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊'
            },
            components: {
                cpn
            },
            methods: {
                cpnClick(item) {
                    console.log(item);
                }
            }
        })
    </script>
```

### 一个很纠结的父子组件通信案例

目的是实现类似`v-model`的效果，父组件传递过去的值在父组件中也会同时刷新

* 常规实现
* watch实现

1. 常规实现，不解释了，直接看代码
```
<div id="app">
        <h2>父组件值：{{number}}</h2>
        <cpn :num="number" @num-change="numberChange"></cpn>
    </div>


    <template id="cpn">
        <div>
            <!-- 不要直接改变props里的值，会报错 -->
            <h2>组件绑定值：{{num}}</h2>
            <h2>组件：{{cpnNumber}}</h2>
            <!-- <input type="text" v-model="cpnNumber"> -->
            <input type="text" :value="cpnNumber" @input="numInput">
        </div>
    </template>
    <script src="../js/vue.js"></script>
    <script>
        const cpn = {
            template: '#cpn',
            props: {
                num: Number
            },
            data() {
                return {
                    cpnNumber: this.num
                }
            },
            methods: {
                numInput(event) {
                    this.cpnNumber = event.target.value
                    this.$emit('num-change', this.cpnNumber)
                }
            }
        }

        const app = new Vue({
            el: '#app',
            data: {
                number: 0
            },
            components: {
                cpn
            },
            methods: {
                numberChange(val) {
                    this.number = parseFloat(val)
                }
            }
        })
```

2. watch实现，watch实现简单的多了,watch会监听数值的变化

```
<div id="app">
        <h2>父组件值：{{number}}</h2>
        <cpn :num="number" @num-change="numberChange"></cpn>
</div>


<template id="cpn">
        <div>
            <!-- 不要直接改变props里的值，会报错 -->
            <h2>组件绑定值：{{num}}</h2>
            <h2>组件：{{cpnNumber}}</h2>
            <!-- <input type="text" v-model="cpnNumber"> -->
            <input type="text" v-model="cpnNumber">
        </div>
</template>

<script>
        const cpn = {
            template: '#cpn',
            props: {
                num: Number
            },
            data() {
                return {
                    cpnNumber: this.num
                }
            },
            watch: {
                cpnNumber(newValue) {
                    this.$emit('num-change', newValue)
                }
            }
        }

        const app = new Vue({
            el: '#app',
            data: {
                number: 0
            },
            components: {
                cpn
            },
            methods: {
                numberChange(val) {
                    this.number = parseFloat(val)
                }
            }
        })
</script>
```

## 父子相互访问

### 父访问子

按照代码中的注释来看吧，自己跑一下就知道了
```
    <div id="app">
        <cpn></cpn>
        <cpn></cpn>
        <cpn ref="aaa"></cpn>
        <button @click="btnClick">按钮</button>
    </div>

    <template id="cpn">
        <div>我是子组件</div>
    </template>

    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊'
            },
            methods: {
                btnClick() {
                    // 1.$children
                    // 一般真实开发中 不拿$children取属性
                    // console.log(this.$children)
                    // for (let c of this.$children) {
                    //     console.log(c.name);
                    //     c.showMessage()
                    // }

                    // 2.$refs => 默认是一个空的对象，给子组件ref='bbb'
                    // 通过给cpn加ref,如aaa
                    console.log(this.$refs.aaa);
                }
            },
            components: {
                cpn: {
                    template: '#cpn',
                    data() {
                        return {
                            name: '我是子组件的name'
                        }
                    },
                    methods: {
                        showMessage() {
                            console.log('showMessage')
                        }
                    }
                }
            }
        })
    </script>
```

### 子访问父

参照代码注释
```
    <div id="app">
        <h2>我是cpn组件</h2>
        <cpn></cpn>
    </div>

    <template id="cpn">
        <ccpn></ccpn>
    </template>

    <template id="ccpn">
     <div>
        <h2>我是子组件</h2>
        <button @click="btnClick">按钮</button>
        </div>
    </template>

    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊'
            },
            components: {
                cpn: {
                    template: '#cpn',
                    data() {
                        return {
                            name: '我是cpn组件的name'
                        }
                    },
                    components: {
                        ccpn: {
                            template: '#ccpn',
                            methods: {
                                btnClick() {
                                    // 1.访问父组件$parent
                                    console.log(this.$parent);
                                    console.log(this.$parent.name);

                                    // 2.访问根组件$root
                                    console.log(this.$root);
                                    console.log(this.$root.message);

                                }
                            }
                        }
                    }
                }
            }
        })
    </script>
```

## 插槽

由于vue3会使用新的v-slot，所以这里直接写v-slot好了,以下`v-slot`都可以简写成`#`

### 匿名插槽

`v-slot:default`通过default默认匿名的插槽
```
<div id="app">
        <cpn>
        <template v-slot:default>
            <button>按钮</button>
        </template>
        </cpn>
</div>

    <template id="cpn">
        <div>
            <h2>我是组件</h2>
            <p>我是组件，哈哈哈</p>
            <slot></slot>
    <!-- <slot><button>按钮</button></slot> 可以放默认值-->
    </div>
    </template>

    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊'
            },
            components: {
                cpn: {
                    template: '#cpn'
                }
            }
        })
    </script>
```

### 具名插槽

在子组件中给插槽`name`之后，父组件中必须声明此`name`才能向插槽中插入数据
```
    <div id="app">
        <cpn>
            <template v-slot:todo>
                <button>按钮</button>
            </template>
            <template v-slot:default>没名字的</template>
        </cpn>
    </div>

    <template id="cpn">
        <div>
            <slot></slot>
            <h2>我是组件</h2>
            <p>我是组件，哈哈哈</p>
            <slot name="todo"></slot>
        </div>

    </template>

    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊'

            },
            components: {
                cpn: {
                    template: '#cpn'
                }
            }
        })
    </script>
```

### 作用域插槽
重点是slotProps接取子组件里:`clanguage="language"`类似属性的数据,slotProps 接取的是子组件标签slot上属性数据的集合所有v-bind:user="user"
完整代码
```
<div id="app">
        <cpn>
            <!-- slotProps是任意命名的 -->
            <template v-slot:default="slotProps">
                <span v-for="item in slotProps.clanguage">{{item}}--</span>
            </template>

        </cpn>
    </div>

    <template id="cpn">
        <div>
            <slot  :clanguage="language">
            <!--ul里是默认值-->
            <ul>
                <li v-for="item in language">{{item}}</li>
            </ul>
            </slot>
        </div>

    </template>
    <script src="../js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊'
            },
            components: {
                cpn: {
                    template: '#cpn',
                    data() {
                        return {
                            language: ['JS', 'JAVA', 'C#', 'Python', 'Go']
                        }
                    }
                }
            }

        })
```



ok，Vue基础就到这里了，后面将学习webpack，之后就是正式上手Vue项目了