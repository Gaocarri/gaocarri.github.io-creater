---
# 常用定义
title: "手写一个自己的Promise"          # 标题
date: 2021-12-16  # 创建时间
draft: false                       # 是否是草稿？
tags: ["javascript"]  # 标签
categories: ["javascript"]              # 分类
author: "Carri"                  # 作者
keywords: ["Web音视频"]
description: "手写一个自己的Promise"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---
话不多说，直接上代码
# Promise手写
```javascript
function MyPromise(executor){
  let self = this
  self.status='pending'
  self.value=undefined
  self.reason = undefined
  self.fullfilledCallbacks=[]
  self.rejectedCallbacks=[]
  function resolve(value){
     if(self.status==='pending'){
       self.status = 'fullfilled'
       self.value = value
       self.fullfilledCallbacks.forEach(fn=>{
         fn()
       })
     
  }
  function reject(reason){
    if(self.status==='pending'){
      self.status = 'rejected'
      self.reason =reason
      self.rejectedCallbacks.forEach(fn=>[
        fn()
      ])
    }
  }
  try{
    executor(resolve,reject)
  }catch(err){
    reject(err)
  }
}
```
# 完善Promise.then
```javascript
MyPromise.prototype.then=function(fn1,fn2){
  let self = this
  let promise2 =new MyPromise(function(resolve,reject){
   if(self.status==='fullfilled'){
    try{
      let x =fn1(self.value)
      resolve(x)
       }catch(e){
         reject(e)
       }
  }
  if(self.status==='rejected'){
        try{
      let x =fn2(self.reason)
      reslove(x)
       }catch(e){
         reject(e)
       }
  }
  if(self.status==='pending'){
    self.fullfilledCallbacks.push(function(){
          try{
          let x =fn1(self.value)
          resolve(x)
       }catch(e){
         reject(e)
       }
    })
    self.rejectedCallbacks.push(function(){
              try{
      let x =fn2(self.reason)
      reslove(x)
       }catch(e){
         reject(e)
       }
    })
  }
  })
}
```

# 补充Promise.all
```javascript
myPromise.all=function(arr){
  return new myPromise(function(resolve,reject){
    if(!Array.isArray(arr)){
      throw new Error('arguments must be array')
    }
    let dataArr = []
    let num = 0
    for(let i = 0;i<arr.length;i++){
      let p =arr[i]
      p.then(data=>{
        dataArr.push(data)
        num++
        if(num===arr.length){
          resolve(dataArr)
        }
      }).catch((e)=>{
        reject(e)
      })
  })
}
```

# demo运行
```javascript
let p = new MyPromise(function (resolve, reject) {
  console.log('start')
  setTimeout(function(){
      resolve('data1')
  },2000)
})
p.then(
  (v) => {
    console.log('success： ' + v)
  },
  (v) => {
    console.log('error： ' + v)
  }
)
p.then(
  (v) => {
    console.log('success： ' + v)
  },
  (v) => {
    console.log('error： ' + v)
  }
)
console.log('end')

```