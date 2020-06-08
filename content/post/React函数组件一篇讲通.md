---
# 常用定义
title: "React函数组件一篇讲通"           # 标题
date: 2020-05-18  # 创建时间
draft: false                       # 是否是草稿？
tags: ["React"]  # 标签
categories: ["React"]              # 分类
author: "Carri"                 # 作者
keywords: ["React"]
description : "详解React函数组件"
# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

函数组件相对简单，注意函数组件没有生命周期，但可以模拟生命周期

# 函数组件

1. 基本写法

```
const App = ()=>{
	return <div>app</div>
}
```

2. 使用函数组件写一个加1

```
const App = props => {
  const [n, setN] = React.useState(0)
  const onClick = () => {
    setN(n + 1)
  }
  return (
    <div><button onClick={onClick}>+1</button>{n}</div>
  )
}
```

3. 函数组件Tips

- 函数组件没有state,要使用React.useState
- 没有生命周期

# 函数组件模拟生命周期

1. 使用useEffect解决生命周期问题

```
useEffeect(()=>{},[])
// 空数组代表只在第一次渲染调用，即componentDidMount
useEffect(()=>{
	console.log('n变了')
},[n])
// n变得时候调用(但是第一次渲染也调用了)，即componentDidUpdate

useEffect(()=>{
	console.log('n变了')
})
// 不写n 所有state变得时候都会变

useEffect(()=>{
	console.log('更新了')
	return ()=>{console.log('销毁了')}
})
// return一个函数代表销毁 即componentWillUmount
```

2. 模拟Update除去第一次渲染,自定义React Hooks

```
const [nUpdateCount,setNUpdcateCount] = React.useState(0)
useEffect(()=>{
	setNUpdateCount(x=>x+1)
},[n])
useEffect(()=>{
	if(nUpdateCount>1){
		console.log('n变了')
	}
},[nUpdateCount])

// 更强的写法
const useUpdate = (fn,dep)=>{
	const [count,setCount] = React.useState(0)
	useEffect(()=>{
		setCount(x=>x+1)
	},[dep])
	useEffect(()=>{
		if(Count>1){
			fn()
		}
	},[count,fn])
}

const App = ()=>{
	useUpdate(()=>{console.log('n变了')},n)
}
// 此时就可以在全局使用useUpdate，甚至可以从别的引入useUpdate
```
