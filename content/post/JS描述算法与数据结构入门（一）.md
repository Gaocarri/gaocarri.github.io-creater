---
# 常用定义
title: "JS描述数据结构与算法（一）"           # 标题
date: 2020-01-22    # 创建时间
draft: false                       # 是否是草稿？
tags: ["数据结构与算法","数据结构"]  # 标签
categories: ["数据结构与算法"]              # 分类
author: "Carri"                  # 作者
keywords: ["数据结构与算法","数据结构"]
description : "JS描述数据结构与算法（一）"   

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: false       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---
* 包含数组，栈，队列（使用数组实现）

## 什么是算法和数据结构

- 数据结构：计算机中存储和组织数据的方式
- 算法：解决办法的逻辑/操作


## 数组

JS数组就是API的调用

## 栈结构

1. 栈是受限的线性结构：（生活中类似于自助餐的托盘）

- **只能在一端添加/删除元素**（栈顶）
- 进入：进栈（压栈）
- 出去：出栈（退 栈）

2. 函数调用栈

   - A调用B，B调C，C调D
   - D,C,B,A的弹栈顺序

3. 一个栈结构面试题

   - 有6个元素 6，5，4，3，2，1的顺序进栈（要考虑到可以一边入栈一边出栈）
     - 3，4，6，5，2，1不合法

   4.使用数组实现栈

```javascript
    // Method：和某一个对象实例有关系
    // function
    // 封装栈类

    // 1.封装栈类
    function Stack() {
      // 栈中的属性
      this.items = []
        // 栈的相关操作
        // 1.将元素压入栈
        // function push(this){

      // } 这种方法添加方法不好，因为这样每一个new出来的对象都会有一个push实例，占用内存

      Stack.prototype.push = function(element) {
          return this.items.push(element)
        }
        // 2.从栈顶中取出元素
      Stack.prototype.pop = function() {
          return this.items.pop()
        }
        // 3.返回栈顶元素，仅仅返回
      Stack.prototype.peek = function() {
          return this.items[this.items.length - 1]
        }
        // 4.判断栈是否为空
      Stack.prototype.isEmpty = function() {
          return this.items.length == 0
        }
        // 5.返回栈里的元素个数
      Stack.prototype.size = function() {
          return this.items.length
        }
        // 6.将栈结构的内容以字符串形式返回
      Stack.prototype.toString = function() {
        var resultString = ''
        for (var i = 0; i < this.items.length; i++) {
          resultString += this.items[i] + ''
        }
        return resultString
      }
    }

    // 栈的使用
    var s = new Stack()
    s.push(208) //压栈
    s.push(10)
    s.push(7)
    s.push(4)
    s.push(100)
    s.push(13)
    console.log(s);

    s.pop() //弹栈
    console.log(s);
    console.log(s.peek()) //返回栈顶元素，仅仅返回
    console.log(s.isEmpty()) //判断栈是否为空
    console.log(s.size()); //返回栈里的元素个数
    console.log(s.toString()); //将栈结构的内容以字符串形式返回
```



5. 十进制转二进制

   利用栈结构来实现

   ```javascript
    // 函数：将十进制转为二进制
       function dec2bin(decNumber) {
         // 1.定义栈对象
         var stack = new Stack()
   
         // 2.循环操作
         while (decNumber > 0) {
           // 2.1获取余数并放到栈中
           stack.push(decNumber % 2)
   
           // 2.2获取整除后的结果作为下一次运行的数字
           decNumber = Math.floor(decNumber / 2)
         }
         // 3.从栈中取出0和1
         var binaryString = ''
         while (!stack.isEmpty()) {
           binaryString += stack.pop()
         }
         return binaryString
       }
   ```



## 队列

1. 一种受限的线性表，**先进先出**

- 类似于生活中的电影影院，厕所排队，商场
- 优先排队的人优先处理

2. 基于数组实现队列**,基于数组实现性能不高**(因为删除，从前面删除后，每个元素要往前移动)

```javascript
    // 基于数组实现性能不高(因为删除，从前面删除后，每个元素要往前移动)
    // 封装队列类
    function Queue() {
      // 属性
      this.items = []
        // 方法
        // 1.将元素加入到队列
      Queue.prototype.enqueue = function(element) {
          return this.items.push(element)
        }
        // 2.从队列中删除前端元素
      Queue.prototype.dequeue = function() {
          return this.items.shift()
        }
        // 3.查看前端元素
      Queue.prototype.front = function() {
          return this.items[0]
        }
        // 4.查看队列是否为空
      Queue.prototype.isEmpty = function() {
          return this.items.length == 0
        }
        // 5.查看队列中元素的个数
      Queue.prototype.size = function() {
          return this.items.length
        }
        // 6.toString方法
      Queue.prototype.toString = function() {
        var resultString = ''
        for (var i = 0; i < this.items.length; i++) {
          resultString += this.items[i] + ''
        }
        return resultString
      }
    }

    // 使用队列
    var queue = new Queue()
    queue.enqueue('abc')
    queue.enqueue('cba')
    queue.enqueue('nba')
    queue.enqueue('mba')
    alert(queue)

    queue.dequeue()
    alert(queue)

    alert(queue.front())
    alert(queue.isEmpty())
    alert(queue.size())
    alert(queue.toString())
```

3. 击鼓传花面试算法题

   规则

   - 几个朋友一起玩一个游戏，围城一圈，开始叔叔，数到某个数字的人自动出局
   - 最后剩下的人会胜利，请问最后剩下的是在**哪一个位置**的人

```javascript
    function passGame(nameList, num) {
      // 1.创建一个队列
      var queue = new Queue()
        // 2.将所有人依次加入到队列中
      for (var i = 0; i < nameList.length; i++) {
        queue.enqueue(nameList[i])
      }
      // 3. 开始数数字
      while (queue.size() > 1) {
        // 不是num的时候，重新加入到队列的末尾
        // 是num的时候，将其从队列中删除
        // 3.1num数字之前的人重新放入到队列的末尾
        for (var i = 0; i < num - 1; i++) {
          queue.enqueue(queue.dequeue())
        }
        // 3.2num对应的人删除掉
        queue.dequeue()
      }
      var endName = queue.front()
      return nameList.indexOf(endName)
    }

    nameList = ['Lily', 'Tom', 'LiLei', 'carri', 'taozi']

    console.log(passGame(nameList, 3));
```

4. 优先级队列

- 如头等舱和商务舱乘客的优先级高于经济舱
- 医院会优先处理病情比较严重的患者
- 每个元素不仅包含元素还包含优先级

```javascript
// 只有插入方法和数组不同
    // 如头等舱和商务舱乘客的优先级高于经济舱
    // 医院会优先处理病情比较严重的患者

   function PriorityQueue() {
      // 在PriorityQueue重新创建了一个类，可以理解内部类
      function QueueElement(element, priority) {
        this.element = element
        this.priority = priority
      }

      // 封装属性
      this.items = []

      //实现插入方法
      PriorityQueue.prototype.enqueue = function(element, priority) {
          // 1.创建QueueElement对象
          var queueElement = new QueueElement(element, priority)

          // 2.判断队列是否为空
          if (this.items.length === 0) {
            this.items.push(queueElement)
          } else {
            var added = false
            for (var i = 0; i < this.items.length; i++) {
              if (queueElement.priority < this.items[i].priority) {
                this.items.splice(i, 0, queueElement)
                added = true
                break
              }
            }
            if (!added) {
              this.items.push(queueElement)
            }
          }
        }
   }
```



