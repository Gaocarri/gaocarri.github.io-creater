---
# 常用定义
title: "如何使用Hugo搭建个人博客"           # 标题
date: 2019-10-27    # 创建时间
draft: false                       # 是否是草稿？
tags: ["博客使用","Hugo"]  # 标签
categories: ["博客使用"]              # 分类
author: "Carri"                  # 作者

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---


# 关于Hugo

Hugo的官方网站https://gohugo.io/

* Go语言实现的一个博客生成器
* 世界上最快的博客生成器
  


# 安装Hugo

 以下只谈Windows的安装方式

 1. 根据个人电脑的系统下载Hugo https://github.com/gohugoio/hugo/releases 
 2. 解压，将`hugo.exe`添加到指定地址，比如我添加到`D:\Software\hugo\hugo.exe`
 3. 把`D:\Software\hugo\`加到path当中（此电脑>>右击属性>>系统高级设置>>环境变量>>在系统变量中找到Path>>新建将地址添加）
 4. 重启终端，输入`hugo version`,如果上述操作成功则会显示安装的hugo版本


# 快速搭建博客(具体参考hugo官网>>Quick Start)

1. 创建一个新的目录,在终端中输入(XXX为你的GitHub用户名)
```
hugo new site XXX.github.io-crearter
```
2. 使用终端进入`XXX.github.io-crearter`
```
cd XXXX
```
3. 添加主题
```
git init
git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
# Note for non-git users:
#   - If you do not have git installed, you can download the archive of the latest
#     version of this theme from:
#       https://github.com/budparr/gohugo-theme-ananke/archive/master.zip
#   - Extract that .zip file to get a "gohugo-theme-ananke-master" directory.
#   - Rename that directory to "ananke", and move it into the "themes/" directory.
# End of note for non-git users.

# Edit your config.toml configuration file
# and add the Ananke theme.
echo 'theme = "ananke"' >> config.toml
```
4. 创建一篇新的文章
```
hugo new posts/my-first-post.md
```
你可以编辑你的新文章，文章将会显示以下内容
```
---
title: "My First Post"
date: 2019-03-26T08:47:11+01:00
draft: true
---
```
将draft：true改为false,代表文章是在网页中可见的
5. 运行Hugo server
```
hugo server -D

                   | EN
+------------------+----+
  Pages            | 10
  Paginator pages  |  0
  Non-page files   |  0
  Static files     |  3
  Processed images |  0
  Aliases          |  1
  Sitemaps         |  1
  Cleaned          |  0

Total in 11 ms
Watching for changes in /Users/bep/quickstart/{content,data,layouts,static,themes}
Watching for config changes in /Users/bep/quickstart/config.toml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```
    按住Cltrl+C，点击上面显示的网址`http://localhost:1313/` 就可以在本地看到你的个人博客了
6. 配置主题
使用文本编辑器打开`config.toml`，你会看到如下文字
```
baseURL = "https://example.org/"
languageCode = "en-us"
title = "My New Hugo Site"
theme = "ananke"
```
7. 重新打开一个终端运行hugo
```
hugo
```
此操作会在目录当中创建一个名为public的文件夹，
在目录中创建一个名为`.gitignore`的文件，在里面写入/public/
8. 在Github上创建一个新的仓库:名为`XXX.github.io`XXX是你的用户名
9. 进入`public`并上传到GitHub新创建的仓库中
```
cd public
git init
git add .
git commit -v
git remote add origin git@github.com:XXX.github.io
git push -u origin master
```
10.  刷新页面 在仓库的`Settings`当中找到网址`XXX.github.io`即可进入你的个人博客了



enjoy it!
