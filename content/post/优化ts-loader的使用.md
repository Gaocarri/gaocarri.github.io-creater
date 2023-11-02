---
# 常用定义
title: "优化ts-loader的使用" # 标题
date: 2023-01-14 # 创建时间
draft: false # 是否是草稿？
tags: ["JavaScript", "Webpack", “ts-loader”] # 标签
categories: ["JavaScript"] # 分类
author: "Carri" # 作者
keywords: ["JavaScript", "Webpack", “ts-loader”]
description: "优化ts-loader的使用"

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true # 关闭评论
toc: true # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false # 关闭打赏
mathjax: true # 打开 mathjax
---

在熟悉项目的过程中发现项目使用 ts-loader 处理 TypeScript，存在优化空间，首先说明问题原因，其次给出几种优化方式。

## 1. 为什么使用 ts-loader 启动/热更新/构建会慢

ts-loader（ts-loader 使用的是 tsc）对 ts 文件进行处理时会进行类型检查，在开发/启动/热更新的过程中类型检查是比较耗时的，通过将 ts-loader 的 transpileOnly 默认设置为 true（不进行类型检查）进行对比可以查看 ts-loader 的类型检查耗时。

|                                 | 进行类型检查                                     | 不进行类型检查                                   |
| ------------------------------- | ------------------------------------------------ | ------------------------------------------------ |
| analyze 命令查看 ts-loader 耗时 | ![图片1](/images/tsLoader/图片1.png)<br />48.21s | ![图片2](/images/tsLoader/图片2.png)<br />28.76s |
| build 命令查看构建总耗时        | ![图片3](/images/tsLoader/图片3.png)<br />71.91s | ![图片4](/images/tsLoader/图片4.png)<br />47.83s |
| 类型有问题影响构建时            | 报 error，退出                                   | 不报 error，不退出                               |

可以看到类型检查的耗时，除构建外，每次**启动、热更新**时ts-loader都会进行类型检查，直至检查完毕后，才算完成。

## 2. 优化方案

目前有几种处理方案个人认为优于当前**纯ts-loader**方案，考虑项目成熟度，选择**fork-ts-checker-webpack-plugin**是综合考虑对原有项目执行方式较合适的方案，而使用**babel-loader**处理TypeScript更适合脚手架生成的新项目。

### 2.1 使用fork-ts-checker-webpack-plugin分离类型检查和打包

插件介绍见GitHub：[fork-ts-checker-webpack-plugin](https://github.com/TypeStrong/fork-ts-checker-webpack-plugin/tree/main)

概括一下，就是ts-loader关闭类型检查功能，让此插件开辟一条新的进程进行类型检查，以起到加快打包速度的作用。

在项目中的使用方式，先关闭ts-loader的类型检查，配置完成后，fork-ts-checker-webpack-plugin就会根据tsconfig.json进行类型检查了

```javascript
// webpack.config.base.js
const ForkTsCheckerWebpackPlugin = require('fork-ts-checker-webpack-plugin');

const options = {
	// ...
plugins: [new AntdDayjsWebpackPlugin({ preset: 'antd', plugins: ['isoWeek', 'isToday', 'weekOfYear'], locale: 'zh-cn' }), new ForkTsCheckerWebpackPlugin()],
	// ...
}
```

相比ts-loader，使用插件后如果类型错误，打包不会受到影响，会在控制台进行提示

![图片5](/images/tsLoader/图片5.png)

查看使用后的速度结果，与上面表格对比

|                              | fork-ts-checker-webpack-plugin                               |
| ---------------------------- | ------------------------------------------------------------ |
| analyze命令查看ts-loader耗时 | ![图片5](/images/tsLoader/图片5.png)<br />ts-loader的耗时结果与关闭检查一致 28.98s |
| build命令查看构建总耗时      | ![图片6](/images/tsLoader/图片6.png)                         |
| 类型有问题影响构建时         | 报error                                                      |

使用插件后启动/热更新速度也会加快。

总结：fork-ts-checker-webpack-plugin可以提升打包速度，同时兼顾类型检查

### 2.2 直接关闭ts-loader的类型检查，开发时利用vscode的静态类型检查，自行进行tsc的类型检查

这种方式如标题，只有在我们需要时使用tsc进行类型检查时才检查

检查指令

```json
// package.json
"scripts": {
  "check:types": "tsc"
}
```

除此之外，我们build时也不进行类型检查，检查的步骤可以放到commit时，这样未通过检查的代码无法commit，需要用到**husky**和**pre-commit**

```json
// package.json
"husky": {
    "hooks": {
        "pre-commit": "yarn check:type" // 或者其他的ts类型检查方式也可以
    }
}
```

总结: 相较上述插件方案，类型检查不够自动，而且使用该方案时，最好的ts打包工具不应是ts-loader，而是babel-loader

### 2.3 使用babel-loader是更好的方案

使用babel-loader打包TypeScript可以查看这篇文章：[TypeScript With Babel: A Beautiful Marriage](https://iamturns.com/typescript-babel/)，掘金上也有很多类似的文章。

概括下，就是从babel 7版本开始，babel支持了TypeScript的处理，但是babel本身不会对TypeScript进行检查，需要配合tsc或者eslint进行类型检查（方式可以参考2.2，或者可以使用2.1的插件）。

总结：

使用babel-loader的优点

- 构建体积更小
- 构建速度更快
- 构建目标更精确（可以指定支持百分之多少的浏览器），而ts-loader只支持构建出何种es标准的代码
- babel丰富的插件体系，更易拓展

使用babel-loader的缺点

- 不支持某些特定语法，如const enum {}
- 对于成熟项目有迁移成本，适合从零开始

## 3. 方案选取

1. 在我们的项目中，使用2.1方案成本低、快、有效，之后的话完全迁移到babel-loader会更好。（目前还有babel-loader和ts-loader处理ts文件混用，用于处理特殊场景）
2. 脚手架可以使用babel-loader代替ts-loader

总之，纯ts-loader不是最好的选择
