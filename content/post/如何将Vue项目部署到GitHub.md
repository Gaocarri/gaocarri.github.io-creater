---
# 常用定义
title: "如何将Vue项目部署到GitHub"          # 标题
date: 2020-08-10   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架","TypeScript"]  # 标签
categories: ["黑曜石记账项目笔记"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架","JS库"]
description: "如何将Vue项目部署到GitHub"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# 部署

按照官方文档的指示 检查dist是否打包正确

```
yarn build 

yarn global add serve  # 全局安装serve

serve -s dist
```

部署到gitHub my-project===我的仓库名

```
module.exports = {
  publicPath: process.env.NODE_ENV === 'production'
    ? '/my-project/'
    : '/'
}
```

创建deploy.sh

```
#!/usr/bin/env sh

# 当发生错误时中止脚本
set -e

# 构建
yarn build

# cd 到构建输出的目录下 
cd dist

# 部署到自定义域域名
# echo 'www.example.com' > CNAME

git init
git add -A
git commit -m 'deploy'

# 部署到 https://<USERNAME>.github.io
# git push -f git@github.com:<USERNAME>/<USERNAME>.github.io.git master

# 部署到 https://<USERNAME>.github.io/<REPO>
git push -f git@github.com:Gaocarri/Money-website.git master:gh-pages

cd -
```

部署 新建一个仓库
```
sh deploy.sh
```