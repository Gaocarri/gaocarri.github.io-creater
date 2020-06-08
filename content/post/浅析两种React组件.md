---
# 常用定义
title: "浅析两种React组件"           # 标题
date: 2020-05-15   # 创建时间
draft: false                       # 是否是草稿？
tags: ["React"]  # 标签
categories: ["React"]              # 分类
author: "Carri"                 # 作者
keywords: ["React"]
description : "浅析两种React组件"  
# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# React两种组件

1.函数组件

```
function Welcome(props){
 return <h1>Hello,{props.name}</h1>
}
<Welcome name="carri" />
```

2.类组件(注意this)

```
class Welcome extends React.Component{
return {
	<h1>Hello,{this.props.name}</h1>
}
}
<Welcome name="carri" />
```

3.关于这里的Welcome组件

- `<Welcome />`会被翻译成React.createElement(Welcome)*
- 可以点击[babel online]([https://babeljs.io/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2%2Cenv&prettier=false&targets=&version=7.9.6&externalPlugins=](https://babeljs.io/repl#?browsers=defaults%2C not ie 11%2C not ie_mob 11&build=&builtIns=false&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2%2Cenv&prettier=false&targets=&version=7.9.6&externalPlugins=))直接翻译测试

4.React.CreateElement

- 传入字符串'div',会创建一个div
- 传入一个函数，会调用函数，获取其返回值
- 传入一个类，则在类前面加个`new`(这回导致执行`constructor`),获取一个组件对象，然后调用对象的render方法，获取其返回值 

# 类组件

1.类组件写法示例

```jsx
class Weclome extends React.Components{
	constructor(){
	super()
    this.state={n:0}
	}
    render(){
        return <div>hi</div>div>
    }
}
```

2.添加props

- 类组件直接读取属性this.props.xxx
- 函数组件直接读取参数props.xxx

3.添加state(内部数据)

- 类组件用this.state读，this.setState
- 函数组件用useState返回数组，第一项读，第二项写

```
  const [n, setN] = React.useState(0);
  // n是0 数组第一项
  // setN是写 数组第二项
```



4.类组件注意事项

- this.state.n+=1无效

  - 其实n已经改变了，只不过UI不会自动更新
  - 调用setState才会触发UI更新
  - 因为React没有像Vue监听data一样监听state

- setState会异步更新UI

  - setState之后，state不会马上改变，立马读state会失败
  - 更推荐的方式是setState函数(避免异步造成的误解)

  ```
  this.setState(state=>{
  this.setState(state=>{
  	const n = state.n+1
  	console.log(n)
  	return {n}
  })
  })
  ```

  

- this.setState(this.state)不推荐

  - 因为React希望我们不要修改旧的state（不可变数据）
  - 常用代码:this.setState({n:state.n+1})

# 函数组件

1.函数组件注意事项

- 跟类组件相似的地方
  - 也要通过setX（新值）来更新UI
- 跟类组件不同的地方 
  - 没有this，一律用参数和变量

2.类组件的setState会自动合并（只合并对象的第一层），函数组件的setX不会自动合并（所以分开写）

- 类组件要合并第二层的写法(先复制)，默认值合并第一层

```
// const user = Object.assign({},this.state.user)
// 也可以写成
const user = {...this.state.user}
user.name='jack'
this.setState({
user:user
})
```

