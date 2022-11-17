---
# 常用定义
title: "axios拦截器原理"          # 标题
date: 2021-07-29   # 创建时间
draft: false                       # 是否是草稿？
tags: ["javascript","库"]  # 标签
categories: ["javascript"]              # 分类
author: "Carri"                  # 作者
keywords: ["javascript","库"]
description: "axios拦截器原理"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# 拦截器是什么
页面发送http请求时，很多情况我们要对**请求和其响应**进行**特定的处理**；

例如

* 每个请求都附带后端返回的token
* 拿到response之前loading动画的展示等。

如果请求数非常多，这样处理起来会非常的麻烦，程序的优雅性也会大打折扣。在这种情况下，axios为开发者提供了这样一个API：**拦截器**。拦截器分为 请求（request）拦截器和 响应（response）拦截器。

# 怎么使用拦截器
1.请求拦截器

请求拦截器的作用是在请求发送前进行一些操作，例如在每个请求体里加上token，统一做处理的好处在于以后想改的时候直接就能改。

使用方法:
```javascript
service.interceptors.request.use(
  config => {
    // 在发送请求之前做什么
    if (config.method === "post") {
      // 序列化
      // config.data = qs.stringify(config.data);
      // config.data = JSON.stringify(config.data);
    }else {
          if (store.getters.token) {
               // 若是有做鉴权token , 就给头部带上token
               // 让每个请求携带token-- ['X-Token']为自定义key 请根据实际情况自行修改
               // 若是需要跨站点,存放到 cookie 会好一点,限制也没那么多,有些浏览环境限制了 localstorage (隐身模式)的使用
                config.headers['X-Token'] = getToken()
          }
    }
    return config;
  },
  error => {
    // 对请求错误做些什么，自己定义
    Message({                  //比如可以使用element-ui的message进行信息提示
      showClose: true,
      message: error,
      type: "warning"
    });
    return Promise.reject(error);
  }
```
这里说一下token，一般是在登录完成之后，将用户的token通过localStorage或者cookie存在本地，然后用户每次在进入页面的时候，会首先从本地存储中读取token，如果token存在说明用户已经登陆过，则更新vuex中的token状态。然后，在每次请求接口的时候，都会在请求的header中携带token，服务器就可以根据你携带的token来判断你的登录是否过期，如果没有携带，则说明没有登录过。

2.响应拦截器

响应拦截器的作用是在接收到响应后进行一些操作，例如在服务器返回登录状态失效，需要重新登录的时候，跳转到登录页等。

使用方法：
```javascript
// response interceptor
service.interceptors.response.use(
    response => {
        // 如果返回的状态码为200，说明接口请求成功，可以正常拿到数据
        // 否则的话抛出错误
        if (response.status === 200) {
            return Promise.resolve(response);
        } else {
            return Promise.reject(response);
        }
    },
    // 服务器状态码不是2开头的的情况
    // 然后根据返回的状态码进行一些操作，例如登录过期提示，错误提示等等
    // 下面列举几个常见的操作，其他需求可自行扩展
    error => {
        if (error.response.status) {
            switch (error.response.status) {
                // 例如401: 未登录
                // 未登录则跳转登录页面，并携带当前页面的路径
                // 在登录成功后返回当前页面，这一步需要在登录页操作。
                case 401:
                    router.replace({
                        path: '/login',
                        query: {
                            redirect: router.currentRoute.fullPath
                        }
                    });
                    break;

                // 403 token过期
                // 登录过期对用户进行提示
                // 清除本地token和清空vuex中token对象
                // 跳转登录页面
                case 403:
                      Message({
                        message: '登录过期，请重新登录',
                        duration: 1000,
                        forbidClick: true
                    });
                    // 清除token
                    localStorage.removeItem('token');
                    store.commit('loginSuccess', null);
                    // 跳转登录页面，并将要浏览的页面fullPath传过去，登录成功后跳转需要访问的页面
                    setTimeout(() => {
                        router.replace({
                            path: '/login',
                            query: {
                                redirect: router.currentRoute.fullPath
                            }
                        });
                    }, 1000);
                    break;

                // 404请求不存在
                case 404:
                    Message({
                        message: '网络请求不存在',
                        duration: 1500,
                        forbidClick: true
                    });
                    break;
                // 其他错误，直接抛出错误提示
                default:
                    Message({
                        message: error.response.data.message,
                        duration: 1500,
                        forbidClick: true
                    });
            }
            return Promise.reject(error.response);
        }
    }
});
```

响应拦截器很好理解，就是服务器返回给我们的数据，我们在拿到之前可以对他进行一些处理。例如：如果后台返回的状态码是200，则正常返回数据，否则的根据错误的状态码类型进行一些我们需要的错误，其实这里主要就是进行了错误的统一处理和没登录或登录过期后调整登录页的一个操作。

# axios拦截器原理

首先直接看源代码吧，注意chain这个数组
```javascript
  // 1. 定义拦截器中间件钩子
  // dispatchRequest是真正开始下发请求，执行config中设置的adapter方法
  var chain = [dispatchRequest, undefined];
  var promise = Promise.resolve(config);
  // 添加请求前钩子
  this.interceptors.request.forEach(function unshiftRequestInterceptors(interceptor) {
    chain.unshift(interceptor.fulfilled, interceptor.rejected);
  });
  // 添加请求后钩子
  this.interceptors.response.forEach(function pushResponseInterceptors(interceptor) {
    chain.push(interceptor.fulfilled, interceptor.rejected);
  });

  // 2. chain链关键代码，promise链路传递下去
  while (chain.length) {
    promise = promise.then(chain.shift(), chain.shift());
  }

  return promise;
};
```

我们来通俗地捋一下拦截器原理吧

1. 创建一个chain数组，数组中保存了拦截器相应方法以及dispatchRequest（dispatchRequest这个函数调用才会真正的开始下发请求）
2. 我们需要把请求拦截器的方法放到chain数组中dispatchRequest的前面
3. 把响应拦截器的方法放到chain数组中dispatchRequest的后面
4. 2，3两步依赖forEach将它们分别unshift,push到chain数组中
5. 为了保证它们的执行顺序，需要使用promise，以出队列的方式对chain数组中的方法挨个执行


# 参考
[axios拦截器接口配置与使用](https://www.jianshu.com/p/646ed4edf51f)

[axios用法及原理](https://lq782655835.github.io/blogs/project/axios-usage-theory.html)