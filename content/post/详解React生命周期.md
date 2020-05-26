---
# 常用定义
title: "详解React生命周期"           # 标题
date: 2020-05-07   # 创建时间
draft: false                       # 是否是草稿？
tags: ["React"]  # 标签
categories: ["React"]              # 分类
author: "Carri"                 # 作者
keywords: ["React"]
description : "详解React生命周期"
# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# React生命周期

1. 类比代码

```jsx
let div = document.ceateElement('div')
// 这是div的create/construc过程
div.textContent= 'hi'
// 这是初始化state
document.body.appendChild(div)
// 这是div的update过程
div.remove
// 这是div的unmount过程
```

2. 生命周期列表

- constructor() --在这里初始化state
- static getDerivedStateFromProps() 不常用
- shouldComponentUpdate() -- return false阻止更新
- render() -创建虚拟DOM
- getSnapshotBeforeUpdate() 不常用
- componentDidMount() 挂在之后就会执行 --组件已出现在页面
- componentDidUpdate() 更新之后就会执行 --组件已更新
- componentWillUnmount() 将要死亡的时候 --组件将死
- static getDerivedFromError() 不常用
- componentDidCatch() 不常用

3. constructor

- 初始化props
- 初始化state,但不能调用setState
- 可不写

4. shouldComponentUpdate

- 返回true表示不阻止UI更新
- 返回false表示阻止UI更新
- 它可以让我们手动判断是否要进行组件更新，我们可以根据应用场景灵活地设置返回值，以避免不必要的更新，性能优化

```jsx
class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      n: 1
    }
  }

  onClick = () => {
    this.setState(state => ({ n: state.n + 1 }))
    this.setState(state => ({ n: state.n - 1 }))
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (nextState.n === this.state.n) {
      return false // 不更新
    }
  }

  render() {
    return (
      <div>App
        <div>{this.state.n}
          <button onClick={this.onClick}>+1</button>
        </div>
      </div>
    )
  }
}

// 前后n没变，没有该生命周期的时候点击会再render一次（因为react的两个n不是一个n，有办法让它比比较，见后面），有的时候不会再render
// render不是更新UI，render是虚拟DOM里面的
```

- 内置的pureComponent

  PureComponent 会在 render 之前对比新 state 和旧 state 的每一个 key，以及新 props 和旧 props 的每一个 key。
  如果所有 key 的值全都一样，就不会 render；如果有任何一个 key 的值不同，就会 render。

```jsx
class App extends React.PureComponent{
	render(){
		return <div>App</div>
	}
}
```

- react修改对象setState返回新对象，不直接在this.state上改

5. render

- return一个虚拟DOM
- 只能有一个根元素，否则用<React.Fragment></React.Fragment>包起来，这个元素不渲染，只用来做个占位，可以用**空标签**代替<></>

- 技巧

  - 还可以写if..else
  - 还可以map(一定要写key)

  ```jsx
  render(){
  	return this.state.array.map(n=><span key={n}>{n}</span>)
  }
  ```

6. componentdidMount

- 执行依赖于DOM的方法

```jsx
class App extends React.PureComponent{
	constructor(props){
		super(props)
		this.state={
			width:undefined
		}
	}
	// 元素的宽度智能在挂载后取，所以
	componentDidMount(){
		const div = document.getElementById('xxx')
		const width=div.getBoundingClientRect().width
		// 等价于 const {width}=div.getBoundingClientRect()
		this.setState({width})
	}
	
	render(){
	return(
		<div id="xxx">Hello World{width}</div>
		)
	}
}
```

- 题外话ref语法

```jsx
class App extends React.PureComponent{
    divRef = undefined // 推荐写上，便于阅读
	constructor(props){
		super(props)
		this.state={
		n:1
		}
		this.divRef = React.createRef()
	}
	render(){
	return (
		<div ref={this.divRef}>APP<div>	
	)
	}
}
            
// 此时在任何地方都可以this.divRef获取到div
// this.div = this.divRef.current
```

- 发起加载数据的AJAX请求（官方推荐）
- 首次渲染会执行

7. componentDidUpdate()

- 视图更新后执行代码
- 此处也可以发起AJAX，用于更新数据
- 此处setState可能会引起无限循环，除非放在if里
- 如果shouldComponentUpdate的为false，则不会触发此钩子

8. componentWillUnmount

- 组件要被**移出页面然后被销毁时**执行代码
- 举例
  - 如果你在c...DidMount里面创建了Timer
  - 那么你就要在c..WillUnmount里面取消Timer
  - 如果在c..DidMount里面创建了AJAX
  - 那就要在c..WillUnmount里面取消请求
  - 原则：哪个组件污染哪个组件治理
