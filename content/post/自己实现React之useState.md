---
# 常用定义
title: "自己实现React之useState"           # 标题
date: 2020-05-12 # 创建时间
draft: false                       # 是否是草稿？
tags: ["React"]  # 标签
categories: ["React"]              # 分类
author: "Carri"                 # 作者
keywords: ["React"]
description : "自己实现React之useState"
# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# useState原理深入

1. `const [n,setN] = React.use(0)`

2. 分析

- setN会修改数据x，将n+1存入x
- setN一定会触发<App / >重新渲染（re-render）
- useState肯定会从x读取n的最新值
- 每个组件都有自己的数据x，我们将其命名为state

3. 自己写一个useState

```jsx
let _state 

const myUseState = initialValue=>{
	_state =  _state===undefined?initalValue:_state
	const setState = (newValue) =>{
		_state = newValue
		render()
	}
	return [_state,setState]
}

const render = ()=>{
ReactDOM.render(<App />,rootElement)
}

const App = ()=>{
    const [n,setN] = myUseState(0)
}
```

4. 但是myUseState有问题，如果调用了两次myUseState会有冲突

- 把_state改成数组

```
let _state = []
let index = 0

const myUseState = initialValue=>{
const currentIndex = index
	_state[currentIndex] =  _state[currentIndex]===undefined?initalValue:_state
	const setState = (newValue) =>{
		_state[currentIndex] = newValue
		render()
	}
	index += 1
	return [_state[currentIndex],setState]
}

const render = ()=>{
index = 0
ReactDOM.render(<App />,rootElement)
}

const App = ()=>{
    const [n,setN] = myUseState(0)
    const [m,setM] = myUseState(0)
}
```

5. 因为顺序的原因，myUse不能放在if..else..里,可以自己尝试一下原版的useState，提示组件调用的Hook不能有顺序变化
6. myUseState还有一个问题，两个组件不能同时用这个_state和index

- React的解决办法，放在组件对应的虚拟节点对象上

7. 总结

- 每个函数组件对应一个React节点
- 每个节点保存着state和index
- useState会读取state[index]
- index由useState出现的顺序决定
- setState会修改state，并触发更新

8. 由于useState产生新的n，并不直接修改原来的n，所以该状态没有贯穿失重，其他贯穿始终的状态

- useRef
  - useRef不仅可以用于div，还能用于任意数据（它改变数据就是改变的当前数据），但不能自己触发render，可以用setN触发
  - useContext不仅能贯穿始终，还能贯穿不同组件

# React生命周期