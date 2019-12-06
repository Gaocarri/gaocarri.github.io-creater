---
# 常用定义
title: "JS基本语法"           # 标题
date: 2019-11-03    # 创建时间
draft: false                       # 是否是草稿？
tags: ["JS","JavaScript","JavaScript入门",JavaScript语法"]  # 标签
categories: ["JavaScript"]              # 分类
author: "Carri"                  # 作者
keywords: ["JS","JavaScript","JavaScript入门",JavaScript语法"]
description : "JS基本语法" 

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---


# 表达式和语句
1. 表达式
    * 1+2表达式的值是3
    * add(1,2)表达式的值是函数的返回值
    * console.log表达式的值为函数本身
    * console.log(3)表达式的值为undefined
2. 语句
    * var a =1 是一个语句
3. 区别
    * 表达式一般都有值，语句可能有可能没有
    * 语句一般会改变环境（赋值，变量）
    * 上面两句话并不是绝对的

# 大小写与空格
1. JS大小写敏感
2. 空格和回车没有实际意义（当return后面跟空格会返回undefined）

# 标识符
1. 第一个字符,可以是Unicode字母或$或_或中文
2. 后面的字符，除了上述字符还可以是数字

# 区块block
* 将代码包在一起
* 常常与if/while/for在一起使用
```
{
    let a = 1
    let b = 2
}
```

# if语句
* if结构先判断一个表达式的布尔值，然后根据布尔值的真伪，执行不同的语句。所谓布尔值，指的是 JavaScript 的两个特殊值，true表示真，false表示伪
```
if (a === 1){
    console.log('a是1')
}else if(a === 2){
    console.log('a是2')
}else{
    console.log('a不是1和2')
}
```
* 建议总是在if语句中使用大括号，因为这样方便插入语句。
* 注意，if后面的表达式之中，不要混淆赋值表达式（=）、严格相等运算符（===）和相等运算符（==）。尤其是赋值表达式不具有比较作用。

# switch语句
* 多个if...else连在一起使用的时候，可以转为使用更方便的switch结构。
```
switch(fruit){
     case 'banana': 1;
     break;
     case 'apple': 2;
     break;
     case 'peach': 3;
     break;
     default;
}
```
* 上面代码根据变量fruit的值，选择执行相应的case。如果所有case都不符合，则执行最后的default部分。需要注意的是，每个case代码块内部的break语句不能少，否则会接下去执行下一个case代码块，而不是跳出switch结构。

# 三元运算符
* 如果条件为true，返回表达式1，否则返回表达式2（if...else的简写形式）
```
(条件)?表达式1:表达式2
```

# while循环
```
var i = 0;

while (i < 100) {
  console.log('i 当前为：' + i);
  i = i + 1;
}
```
* 上面代码将循环100次直到i=100为止
* 当循环条件为真，会无限循环


# for循环
```
for (初始化表达式; 条件; 递增表达式) {
  语句
}
```
* 初始化表达式（initialize）：确定循环变量的初始值，只在循环开始时执行一次。
* 条件表达式（test）：每轮循环开始时，都要执行这个条件表达式，只有值为真，才继续进行循环。
* 递增表达式（increment）：每轮循环的最后一个操作，通常用来递增循环变量。

```
var x = 3;
for (var i = 0; i < x; i++) {
  console.log(i);
}
```
* 上面代码中，初始化表达式是var i = 0，即初始化一个变量i；测试表达式是i < x，即只要i小于x，就会执行循环；递增表达式是i++，即每次循环结束后，i增大1。

# break语句和continue语句
* break语句和continue语句都具有跳转作用，可以让代码不按既有的顺序执行。

```
while(i < 100) {
  console.log('i 当前为：' + i);
  i++;
  if (i === 10) break;
}
```
* break语句用于跳出代码块或循环。
* 上面代码只会执行10次循环，一旦i等于10，就会跳出循环。
* for循环也可以使用break语句跳出循环。

```
var i = 0;

while (i < 100){
  i++;
  if (i % 2 === 0) continue;
  console.log('i 当前为：' + i);
}
```
* continue语句用于立即终止本轮循环，返回循环结构的头部，开始下一轮循环。
* 上面代码只有在i为奇数时，才会输出i的值。如果i为偶数，则直接进入下一轮循环。

# label（标签）
* JavaScript 语言允许，语句的前面有标签（label），相当于定位符，用于跳转到程序的任意位置，标签的格式如下。
* 标签可以是任意的标识符，但不能是保留字，语句部分可以是任意语句。
* 标签通常与break语句和continue语句配合使用，跳出特定的循环。

```
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) break top;
      console.log('i=' + i + ', j=' + j);
    }
  }
```
* 上面代码为一个双重循环区块，break命令后面加上了top标签（注意，top不用加引号），满足条件时，直接跳出双层循环。如果break语句后面不使用标签，则只能跳出内层循环，进入下一次的外层循环。

```
foo: {
  console.log(1);
  break foo;
  console.log('本行不会输出');
}
console.log(2);
// 1
// 2
```
* 标签也可以用于跳出代码块。
* 上面代码执行到break foo，就会跳出区块

```
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) continue top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
// i=2, j=0
// i=2, j=1
// i=2, j=2
```
* continue语句也可以与标签配合使用。
* 上面代码中，continue命令后面有一个标签名，满足条件时，会跳过当前循环，直接进入下一轮外层循环。如果continue语句后面不使用标签，则只能进入下一轮的内层循环。