---
# 常用定义
title: "在Vue+TypeScript中使用Toast"          # 标题
date: 2020-03-21   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架","TypeScript"]  # 标签
categories: ["黑曜石记账项目笔记"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架","JS库"]
description: "在Vue+TypeScript中使用Toast"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# TypeScript中使用toast

1. 建立一个文件夹 toast
2. 封装一个Toast组件(css略)

```
<template>
  <div class="toast" v-show="isShow">
    <div>{{message}}</div>
  </div>
</template>

<script lang='ts'>
import Vue from "vue";
import { Component } from "vue-property-decorator";

@Component
export default class Toast extends Vue {
  message: string = "";
  isShow: boolean = false;

  show(message: string, duration = 1500) {
    this.isShow = true;
    this.message = message;
    setTimeout(() => {
      this.isShow = false;
      this.message = "";
    }, duration);
  }
}
</script>
```

3. 在index.ts中使用

```
import Toast from './Toast.vue'

const obj = {} as any

obj.install = (Vue: any) => {
  // 1.创建一个组件构造器
  const toastConstructor = Vue.extend(Toast)
  // 2.new的方式，根据组件构造器，可以创建出来一个组件对象
  const toast = new toastConstructor()
  // 3.将组件对象，手动挂载到某一个元素上
  toast.$mount(document.createElement('div'))
  // 4.tost.$el对应的就是div(这样才真正的加到了界面上面)
  document.body.appendChild(toast.$el)
  Vue.prototype.$toast = toast
}

export default obj
```

4. 在main.ts中安装toast

```
import toast from '@/components/common/toast'
Vue.use(toast)
```

5. 这时在任意Vue组件中使用toast即可

```
this.$toast.show('这是一句提示',1000)
```

6. 但是很不巧这里在TypeScript中会报错（$toast提示没定义问题）解决方法：

   - 在shims-vue.d.ts中

   ```
   declare module 'vue/types/vue' {
     // 3. 声明为 Vue 补充的东西
     interface Vue {
       $toast: any
     }
   }
   ```

   - 这样等于告诉编辑器，你这个属性是有的

7. 记一个踩过的坑，如何在vuex的store中更据不同的结果返回不同的toast

   - 首先在state中声明一个属性

   ```
   state:{
   	toastMessage:''
   }
   ```

   - mutations中声明一个updateToastMessage的方法

   ```
   mutations:{
       // toastMessage方法
       updateToastMessage(state, message) {
         state.toastMessage = message
       }
   }
   ```

   - 当其他方法改变toast时，如

   ```
   mutations:{
    createTag{
    	if(true){
    		store.commit('updateToastMessage',1)
    	}else{
    		store.commit('updateToastMessage',1)
    	}
    }
   }
   ```

   - 此时我们在组件中想要使用toast，可以：

   ```
       this.$store.commit("createTag", this.selectedTag);
       this.$toast.show(this.$store.state.toastMessage);
   ```

   