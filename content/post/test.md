---
# 常用定义
title: "test"           # 标题
date: 2020-08-07   # 创建时间
draft: false                       # 是否是草稿？
tags: ["React"]  # 标签
categories: ["React"]              # 分类
author: "Carri"                 # 作者
keywords: ["React"]
description : "JSX语法几步搞定"
# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# JSX注意事项

1. 注意className

```jsx
// 要写成
<div className="red">n</div>
```

2. 插入变量

- 标签里面的所有JS代码要用{}抱起来
- 如果有需要的变量n，难么就用{}把n包起来
- 如果你需要对象，那么就要用{}把对象包起来，如{{name:'gaocarri'}}

3. 习惯return后面加()

# JSX条件判断

1. 直接返回

```
const App = () => {
  let n = 1
  return  {n % 2 === 0 ? <div>n是偶数</div> : <div>n是奇数</div>}
}
```

2. 在div里

```jsx
const App = () => {
  let n = 1
  return (
    <div>
      {n % 2 === 0 ? <div>n是偶数</div> : <div>n是奇数</div>}
    </div>
  )
}
```

3. 变量写法

```jsx
const App = () => {
  let n = 1
  const content=(
   <div>
      {n % 2 === 0 ? <div>n是偶数</div> : <div>n是奇数</div>}
    </div>
  )
  return content
}
```

# 循环遍历

```
const App = (props)=>{
return (
	<div>
	{props.numbers.map(n,index)=>{
	return <div>下标{index}值为{n}</div>
	}}
	</div>
)
}
```

还可以用for循环

```
const App =(props)=>{
const arr = []
for(let i = 0;i<props.numbers.length;i++){
arr.push(<div>下标{i}的值为{props.numbers[i]}</div>)
}
return <div>{arr}</div>
}
```

例子

```jsx
const App = () => {
  let n = 1
  return (
    <div>
      <Component numbers={[1, 2, 3]} />
    </div>
  )
}


const Component = (props) => {
  return (
    props.numbers.map((n, i) => {
      return <div>numbers的第{i}个下标值是{n}</div>
    })
  )
}
```

