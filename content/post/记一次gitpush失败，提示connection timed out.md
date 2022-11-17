---
# 常用定义
title: "记一次git push失败，提示connection timed out"           # 标题
date: 2020-03-06    # 创建时间
draft: false                       # 是否是草稿？
tags: ["git"]  # 标签
categories: ["工具"]              # 分类
author: "Carri"                  # 作者
keywords: ["git"]
description : "记一次git push失败，提示connection time out"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: false       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

首先，报错信息
```
ssh: connect to host github.com port 22: Connection timed out
fatal: Could not read from remote repository.

Please make sure you have the correct access rights and the repository exists.
```

这可能是防火墙导致的，解决方法参考官方帮助文档
https://help.github.com/en/github/authenticating-to-github/using-ssh-over-the-https-port

## 具体的解决步骤
1. 先测试可用性
```
ssh -T -p 443 git@ssh.github.com
```
提示如下证明可用
```
Hi username! You’ve successfully authenticated, but GitHub does not
provide shell access.
```

2. 然后编辑~/.ssh/config 文件，如果没有config文件的话就直接vim ~/.ssh/config加入如下内容
```
Host github.com
Hostname ssh.github.com
Port 443
```

3. 再次测试
```
ssh -T git@github.com
```
提示如下即可用
```
Hi username! You’ve successfully authenticated, but GitHub does not provide shell access.
```

问题解决！

奇妙的是我把它更改为443端口后再更改回22端口，居然不报错了，尚不清楚为什么，如果有人有新的发现，请给我留言，非常感谢(已更正，并不能- -！)