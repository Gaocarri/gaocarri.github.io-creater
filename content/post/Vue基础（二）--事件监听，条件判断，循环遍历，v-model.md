---
# 常用定义
title: "Vue基础(二)--事件监听，条件判断，循环遍历，v-model"          # 标题
date: 2019-12-14    # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架"]  # 标签
categories: ["框架"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架"]
description: "Vue基础（二）"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

## 事件监听
分为以下三部分

* v-on的基本使用
* v-on的参数
* v-on的修饰符

### v-on的基本使用

```
    <div id="app">
        <h2>{{counter}}</h2>
        <button @click="increment">+</button>
    </div>
    ......
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                counter: 0
            },
            methods: {
                increment() {
                    this.counter++
                }
            }
        })
    </script>
```

increment是一个方法名，定义在app的Vue实例的方法中

### v-on的参数

```
<button @click="increment">+</button>
```
这里`increment`后面没有跟(),这是省略的写法，括号内无参数时,Vue会把浏览器生成的event事件对象作为参数传入到方法

```
    <button @click="btn3Click(123,$event)">3</button>
```
如果既需要event对象，又需要参数，可以用上述写法

### v-on的修饰符
基本用法：

```
    <div @click="divClick">
            aaaa
            <button @click.stop="btnClick">按钮</button>
     </div>
```

四种常用的修饰符:
  * .stop：阻止事件冒泡
  * .prevent：阻止默认事件
  * .enter：监听某个键盘键帽的点击
  * .once：该事件只触发一次

## 条件判断
* v-if、v-else-if、v-else
* v-show

### v-if、v-else-if、v-else

很简单，举例：
```
    <div id="app">
        <h2 v-if="score>=90">优秀</h2>
        <h2 v-else-if="score>=80">良好</h2>
        <h2 v-else-if="score>=60">及格</h2>
        <h2 v-else>不及格</h2>
    </div>
   <!--会根据返回的布尔值决定显示哪一个标签-->
```

### v-show


* 当切换频率高时选择v-show，只有一次通常使用v-if,因为频繁操作DOM影响性能

```
    <!-- v-show HTML节点仍然存在 只是display:none -->
    <h2 v-show="isShow">{{message}}</h2>
```

## 循环遍历

这里主要记录下参数的问题
* v-for遍历数组
* v-for遍历对象
* v-for使用过程添加key(绑定key)
* 哪些数组方法是响应式的

### v-for遍历数组

参数:元素，index
```
    <ul>
        <li v-for="(item,index) in names">{{index}}-{{item}}</li>
    </ul>
```

### v-for遍历对象

参数：值，key，index
```
    <ul>
        <li v-for="(value,key,index) in info">{{index}}.{{key}}:{{value}}</li>
    </ul>
```

### v-for使用过程添加key(绑定key)

```
    <ul>
        <!-- 保证绑定的key和要展示的item是一样的(key是唯一不可变的) 可以使得Diff算法识别此节点并且找到正确的位置插入新的节点 -->
        <!-- key的作用就是为了更高效的更新虚拟DOM -->
        <li v-for="item in letters" :key="item">{{item}}</li>
    </ul>
```

### 哪些数组方法是响应式的

响应式的：

   * push
   * pop
   * shift
   * unshift
   * splice
   * sort
   * reverse
   * set---`Vue.set(this.letters,0,'bbb')`

非响应式的：

   * `this.letters[0]='a'`通过索引值修改数组中的元素不是响应式的
 
## v-model

* 基本使用方法
* v-model原理
* radio,checkbox,select 
* v-model修饰符


### 基本使用方法

直接举例,此时输入什么值，显示什么值
```
    <div id="app">
        <input type="text" v-model="message"> {{message}}
    </div>
```

### v-model原理

v-model等同于：
```
    <!-- <input type="text" v-model="message"> -->
    <!-- input有一个input事件 -->
    <!-- <input type="text" :value="message" @input="valueChange"> -->

    <input type="text" :value="message" @input="message=$event.target.value">
    <h2>{{message}}</h2>

    <!-- textarea也可以用v-model -->
```

### radio,checkbox,select 

radio(选哪个，sex就变成它的value):
```
    <label for="male">
        <input type="radio" id="male" value="男" v-model="sex">男
    </label>
    <label for="female">
        <input type="radio" id="female" value="女" v-model="sex">女
    </label>
    <h2>您选择的性别是{{sex}}</h2>
```

checkbox(直接上学习的代码了)：
```
    <div id="app">
        <!-- checkbox单选框 -->
        <label for="agreement">
            <input type="checkbox" id="agreement" v-model="isAgree">同意协议
        </label>
        <!--isAgree是个布尔值-->
        <h2>你选择的是{{isAgree}}</h2>

        <!-- checkbox多选框 -->
        <input type="checkbox" value="足球" v-model="hobbies">足球
        <input type="checkbox" value="篮球" v-model="hobbies">篮球
        <input type="checkbox" value="羽毛球" v-model="hobbies">羽毛球
        <input type="checkbox" value="打游戏" v-model="hobbies">打游戏
        <h2>您的爱好是{{hobbies}}</h2>

        <!-- 使用for循环 -->
        <label v-for="item in originHobbies" :for="item">
            <input type="checkbox" :value="item" v-model="hobbies" :id="item">{{item}}
        </label>
    </div>

    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                isAgree: false,
                hobbies: [],
                originHobbies: ['足球', '篮球', '打游戏', '羽毛球']
            }
        })
    </script>
```

select(用法很简单)：
```
<div id="app">
        <!-- 1. 选择一个 -->
        <select name="abc" id="" v-model="fruit">
            <option value="苹果" >苹果</option>
            <option value="香蕉" >香蕉</option>
            <option value="橘子" >橘子</option>
            <option value="梨子" >梨子</option>
        </select>
        <h2>您选择的水果是:{{fruit}}</h2>

        <!-- 2.选择多个 -->
        <select name="abc" id="" v-model="fruits" multiple>
            <option value="苹果">苹果</option>
            <option value="香蕉">香蕉</option>
            <option value="橘子">橘子</option>
            <option value="梨子">梨子</option>
        </select>
        <h2>您选择的水果是:{{fruits}}</h2>
    </div>

    <script>
        const app = new Vue({
            el: '#app',
            data: {
                message: '你好啊',
                fruit: '香蕉',
                fruits: []
            }
        })
```

### v-model的修饰符

基本使用：
```
    <input type="text" v-model.lazy="message">
    <h2>{{message}}</h2>
```
* lazy:按下enter或者失去焦点时才会改变
* number: 默认情况下，v-model赋值的都是string类型,加上.number会把它转为Number
* trim:去除输入的两边空格，换行符等等