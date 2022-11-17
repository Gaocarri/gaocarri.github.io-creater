---
# 常用定义
title: "Hooks个人参考手册"           # 标题
date: 2021-01-03 # 创建时间
draft: false                       # 是否是草稿？
tags: ["React"]  # 标签
categories: ["React"]              # 分类
author: "Carri"                 # 作者
keywords: ["React"]
description : "Hooks个人参考手册"
# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

这是一篇个人参考手册！！！

# Hooks

详解React Hooks

## useState

- 见上一篇博客

```jsx
const App = () => {
  const [user, setUser] = React.useState({ name: 'carri', age: 18 })
  const onClick = () => {
    setUser({
      ...user,
      name: 'Gao'
    })
  }
  return (
    <div>
      <span>{user.name}</span>
      <span>{user.age}</span>
      <button onClick={onClick}>click</button>
    </div>
  )
}

```

## useReducer

1. 推荐数据三个以上使用useReducer

```jsx
const initial = {
  n: 0
}

const reducer = (state, action) => {
  if (action.type === 'add') {
    return { n: state.n + action.number }
  } else if (action.type === 'multi') {
    return { n: state.n * 2 }
  }
}

const App = () => {
  const [state, dispatch] = React.useReducer(reducer, initial)
  const onClick = () => {
    dispatch({ type: 'add', number: 3 })
  }


  return (
    <div>
      <div>{state.n}</div>
      <button onClick={onClick}>+</button>
    </div>
  )
}
```

## useContext

- 上下文

  - 全局变量是全局的上下文
  - 上下文是局部的全局变量

- 使用方法

  - 使用

  ```jsx
  C = createContext(null)
  ```

  - 使用<C.provider value={}>圈定作用域
  - 在代码作用域内使用useContext(C)来使用上下文

# useLayoutEffect

- useEffect在浏览器渲染完成后执行
- useLayoutEffect在浏览器渲染前执行
- useLayoutEffect总是比useEffect先执行
- useLayoutEffect里的人物最好影响了Layout
- 优先使用useEffect

# useMemo

- 缓存

```
const onClickChild = useMemo(()=>{
	return ()=>{}
},[m])

// 只有m变化的时候这个赋值才会重新执行，用来解决React.memo传入一个函数时，改变其他数据Memo的组件依然变化的bug,return后面可以是函数也可以是value
// 类似vue2中的computed
```

- useCallBack，useMemo的语法糖

```
useCallBack(x=>console.log(x),[m])
useMemo(()=>x=>console.log(x),[m])
```

# useRef

- useRef不会执行UI的刷新
- `const count = React.useRef(0)`
- 这个count在组件内可以使用，并且与useState不同的是它每次调用`count.current=1`时它会改变原来的current
- 目的
  - 如果你需要一个值，在组件不断render时保持不变
  - 初始化:const count = useRef(0)
  - 读取:count.current
  - 为什么需要current
  - 为了保证两次useRef是同一个值（只有引用能做到）