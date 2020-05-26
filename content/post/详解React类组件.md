---
# 常用定义
title: "详解React类组件"           # 标题
date: 2020-05-06   # 创建时间
draft: false                       # 是否是草稿？
tags: ["React"]  # 标签
categories: ["React"]              # 分类
author: "Carri"                 # 作者
keywords: ["React"]
description : "详解React类组件" 
# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# 类组件

1. ES6如何创建组件

```jsx
import React from 'react'

class B extends React.Component{
	constructor(props){
		super(props)
	}
	render(){
		return <div>hi</div>
	}
}
```

- 234行可以省掉（如果不需要在constructor里做什么的话）

2. props

- 传数据给B组件

```jsx
class Parent extends React.Components{
    construtor(props){
	super(props)
    this.state = {name:'carri'}
    }
    onClick = () =>{}
    render(){
		return <B name={this.state.name}
                   onClick={this.onClick}
                   >hi</B>
    }
}
// 外部数据被包装为一个对象
// {name:'carri',onClick:...,children:'hi'}
// 此处onClick是一个回调
```

- B组件

```jsx
class B extends React.Component{
	constructor(props){
		super(props)
	}
	reder(){}
}
// 这么做了之后，this.props就是外部数据对象的地址了
```

- 读取

```jsx
class B extends React.Component{
	constructor(props){
		super(props)
	}
	reder(){
		return(
			<div onClick={this.props.onClick}>
			{this.props.name}
			{this.props.children}
			</div>
		)
	}
}
```

- 写props
  - react的原则就是应该由数据的主人对数据进行更改
  - 不要自己修改props的值和属性

3. props的作用

- 接受外部的数据
- 接受外部的函数

4. state&setState

- setState不会立即改变state，它是异步的

```
onClick=()=>{
	this.setState({x:this.state.x+1})
	this.setState({x:this.state.x+1})
}

// 只会加一次1
```

- 函数型写法可以解决上面的问题

```
onClick2=()=>{
	this.setState((state)=>({x:state.x+1})
	this.setState((state)=>({x:state.x+1})
}

```

- setState接受两个参数，第二个是fn(写入成功后执行的函数，不常见)
- setState写时会自动进行shallow merge，但只对新的state和旧的state进行一级合并
- this.state.n+=1 不推荐用，不render

# 类组件事件绑定

1. 类组件

```
class Welcome extends React.Component{
	addN=()=>this.setState({n:this.state.n+1})
	render(){
		return(
			<div onClick={this.addN}>111</div>
		)
	}
}
```

2. 一个举例,为什么用箭头函数
```
class Person{
sayHi()
}

const p =new Person()
// p的sayHi方法在p._proto_上即Person.prototype上,原型上

class Person2{
sayHi2 = ()=>{}
}
// 等价于这种写法
class Person2{
constructor(){
this.sayHi2 =()=>{}
}
}

const p2 = new Person(2)
// p2的sayHi方法在p2自己身上，对象上


这是语法自己定义的
```
