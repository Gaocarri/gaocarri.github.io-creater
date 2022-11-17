---
# 常用定义
title: "qiankun的js沙箱源码解析"          # 标题
date: 2022-07-30   # 创建时间
draft: false                       # 是否是草稿？
tags: ["javascript","库"]  # 标签
categories: ["javascript"]              # 分类
author: "Carri"                  # 作者
keywords: ["javascript","库"]
description: "qiankun的js沙箱源码解析"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# qiankun的沙箱种类
1. SnapshotSandbox （快照沙箱）
2. LegacySandbox（支持单应用的代理沙箱）
3. ProxySandbox（支持多应用的代理沙箱）

LegacySandbox 和 ProxySandbox 利用了 ES6 的 Proxy

2.x 使用 ProxySandbox

1.x 使用 LegacySandbox

不支持 Proxy 的，使用 SanpshotSandbox

# 快照沙箱
```javascript
class SnapshotSandbox {
    /** 保存激活前状态 */
    windowSnapshot = {};
    /** 保存激活后状态 */
    modifyPropsMap = {};
    active() {
        for (const prop in window) {
            this.windowSnapshot[prop] = window[prop];
        }
        Object.keys(this.modifyPropsMap).forEach((prop) => {
            window[prop] = this.modifyPropsMap[prop];
        });
    }
    inactive() {
        for (const prop in window) {
            if (window[prop] !== this.windowSnapshot[prop]) {
                this.modifyPropsMap[prop] = window[prop];
                window[prop] = this.windowSnapshot[prop];
            }
        }
    }
}
// 验证:
let snapshotSandbox = new SnapshotSandbox();
snapshotSandbox.active();
window.city = "Guangzhou";
console.log("window.city-01:", window.city);
snapshotSandbox.inactive();
console.log("window.city-02:", window.city);
snapshotSandbox.active();
console.log("window.city-03:", window.city);
snapshotSandbox.inactive();

//输出：
//window.city-01: Guangzhou
//window.city-02: undefined
//window.city-03: Guangzhou
```
1. 沙箱激活时
* 记录 window 当时的状态（相当于快照）
* 恢复上一次沙箱失活时记录的沙箱运行过程中对 window 做的状态改变，也就是上一次沙箱激活后对 window 做了哪些改变，现在也保持一样的改变。
2. 沙箱失活时
* 记录自沙箱运行时 window 的变化
* 清除沙箱在激活之后在 window 上改变的状态，从代码可以看出，就是让 window 此时的属性状态和刚激活时候的 window 的属性状态进行对比，不同的属性状态就以快照为准，恢复到未改变之前的状态。
3. 缺点
* 遍历 window，性能差
* 多子应用时，均改写沙箱状态，造成混乱

# 支持单应用的代理沙箱
基础逻辑
```javascript
class LegacySandbox {
    /** 沙箱期间新增的全局变量 */
    addedPropsMapInSandbox = new Map();
    /** 沙箱期间更新的全局变量 */
    modifiedPropsOriginalValueMapInSandbox = new Map();
    /** 持续记录更新的(新增和修改的)全局变量的 map，用于在任意时刻做 snapshot */
    currentUpdatedPropsValueMap = new Map();
    proxyWindow;
    setWindowProp(prop, value, toDelete = false) {
        if (value === undefined && toDelete) {
            delete window[prop];
        } else {
            window[prop] = value;
        }
    }
    active() {
        this.currentUpdatedPropsValueMap.forEach((value, prop) =>
            this.setWindowProp(prop, value)
        );
    }
    inactive() {
        this.modifiedPropsOriginalValueMapInSandbox.forEach((value, prop) =>
            this.setWindowProp(prop, value)
        );
        this.addedPropsMapInSandbox.forEach((_, prop) =>
            this.setWindowProp(prop, undefined, true)
        );
    }
    constructor() {
        const fakeWindow = Object.create(null);
        this.proxyWindow = new Proxy(fakeWindow, {
            set: (target, prop, value, receiver) => {
                // window对象上的value
                const originalVal = window[prop];
                if (!window.hasOwnProperty(prop)) {
                    // 记录新增的变量
                    this.addedPropsMapInSandbox.set(prop, value);
                } else if (
                    !this.modifiedPropsOriginalValueMapInSandbox.has(prop)
                ) {
                    // 记录window上原有的变量的原有值
                    this.modifiedPropsOriginalValueMapInSandbox.set(
                        prop,
                        originalVal
                    );
                }
                // 每次都更新用于记录更新的map
                this.currentUpdatedPropsValueMap.set(prop, value);
                window[prop] = value;
            },
            get: (target, prop, receiver) => {
                return window[prop];
            },
        });
    }
}
// 验证：
let legacySandbox = new LegacySandbox();
legacySandbox.active();
legacySandbox.proxyWindow.city = "Guangzhou";
console.log("window.city-01:", window.city);
legacySandbox.inactive();
console.log("window.city-02:", window.city);
legacySandbox.active();
console.log("window.city-03:", window.city);
legacySandbox.inactive();
// 输出：
// window.city-01: Beijing
// window.city-02: undefined
// window.city-03: Beijing   
```

1. 特点
* 使用代理 window
* 使用三个变量记录 window 的操作（window 中原来不存在的，window 中原来存在的但被更新的，所有更新的）

2. 沙箱激活时
* 恢复上一次沙箱失活时记录的沙箱运行过程中对 window 做的状态改变，也就是上一次沙箱激活后对 window 做了哪些改变，现在也保持一样的改变。

3. 沙箱失活时
* window 中原来不存在的变量删除
* window 中原来存在的变量但被更新的，恢复

4. 优点：相对快照沙箱有性能优势 
5. 缺点：微应用中 fakeWindow 是一个空对象，并不实际保存变量，而创建的变量最终实际都挂载在 window 上，不适用多个应用时 

如何把子应用中的 window 变成代理 window？

* loadApp 时使用沙箱的代理沙箱代替 window(详见搜索源码的 createSandboxContainer)

# 支持朵应用的代理沙箱
``` javascript
class ProxySandbox {
    proxyWindow;
    isRunning = false;
    active() {
        this.isRunning = true;
    }
    inactive() {
        this.isRunning = false;
    }
    // context:用于共享的上下文
    constructor(context = {}) {
        const fakeWindow = Object.create(null);
        this.proxyWindow = new Proxy(fakeWindow, {
            set: (target, prop, value, receiver) => {
                if (this.isRunning) {
                    target[prop] = value;
                }
            },
            get: (target, prop, receiver) => {
                if (Object.keys(context).includes(prop)) {
                    return context[prop];
                }
                return prop in target ? target[prop] : window[prop];
            },
        });
    }
}

// 验证：
let proxySandbox1 = new ProxySandbox();
let proxySandbox2 = new ProxySandbox();
proxySandbox1.active();
proxySandbox2.active();
proxySandbox1.proxyWindow.city = "Guangzhou";
proxySandbox2.proxyWindow.city = "Nanjing";
console.log(
    "active:proxySandbox1:window.city:",
    proxySandbox1.proxyWindow.city
);
console.log(
    "active:proxySandbox2:window.city:",
    proxySandbox2.proxyWindow.city
);
console.log("window:window.city:", window.city);
proxySandbox1.inactive();
proxySandbox2.inactive();
proxySandbox1.proxyWindow.city = "Shanghai";
proxySandbox2.proxyWindow.city = "Hangzhou";
console.log(
    "inactive:proxySandbox1:window.city:",
    proxySandbox1.proxyWindow.city
);
console.log(
    "inactive:proxySandbox2:window.city:",
    proxySandbox2.proxyWindow.city
);
console.log("window:window.city:", window.city);

// 输出：
// active:proxySandBox1:window.city: Guangzhou
// active:proxySandBox2:window.city: Nanjing
// window:window.city: undefined
// inactive:proxySandBox1:window.city: Guangzhou
// inactive:proxySandBox2:window.city: Nanjing
// window:window.city: undefined
```

1. 特点
* 每个应用都使用自己的代理 window

2. 激活，失活时
* 更改对应沙箱的运行状态

3. 优点：支持多实例
