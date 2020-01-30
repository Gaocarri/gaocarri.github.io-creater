---
# 常用定义
title: "JS描述数据结构与算法（三）"           # 标题
date: 2020-01-20    # 创建时间
draft: false                       # 是否是草稿？
tags: ["数据结构与算法","数据结构"]  # 标签
categories: ["数据结构与算法"]              # 分类
author: "Carri"                  # 作者
keywords: ["数据结构与算法","数据结构"]
description : "JS描述数据结构与算法（三）"   

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: false       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---
* 包含集合与哈希表

## 集合

1. 由一组**无序的**，**不能重复的**元素构成

* 一种特殊的数组，**没有顺序，也不能重复**
* 没有顺序意味着**不能通过下标值访问**，不能重复意味着**相同的对象**在集合中只会**存在一份**
* 就是ES6里的Set

2. 封装集合类

```javascript
    // 封装集合类
    function Set() {
      // 属性
      this.items = {}

      // 方法
      // add方法
      Set.prototype.add = function(value) {
        // 判断当前集合中是否已经包含了该元素
        if (this.has(value)) return false

        //将元素添加到集合中
        this.items[value] = value
        return true
      }

      // has方法
      Set.prototype.has = function(value) {
        return this.items.hasOwnProperty(value)
      }

      // remove方法
      Set.prototype.remove = function(value) {
        // 1.判断该集合中是否包含该元素
        if (!this.has(value)) {
          return false
        }

        // 2.将元素从属性中删除
        // delete操作符删除对象的某个属性
        delete this.items[value]
        return true
      }

      // clear方法
      Set.prototype.clear = function() {
        this.items = {}
      }

      // size方法
      Set.prototype.size = function() {
        return Object.keys(this.items).length
      }

      // 获取集合中所有的值
      Set.prototype.values = function() {
        return Object.keys(this.items)
      }
    }

    // 测试Set类
    var set = new Set()

    // 添加元素
    set.add('aaa')
    set.add('bbb')
    set.add('ccc')
    set.add('ddd')
    console.log(set);

    // has
    console.log(set.has('aaa'));

    // remove
    set.remove('aaa')
    console.log(set);

    // size
    console.log(set.size());
    // values
    console.log(set.values());
```

3. 集合间操作

* 并集

```javascript
      Set.prototype.union = function(otherSet) {
        // 1.创建一个新的集合
        var unionSet = new Set()

        // 2.将A集合中所有的元素添加到新集合中
        var values = this.values()
        for (var i = 0; i < values.length; i++) {
          unionSet.add(values[i])
        }

        // 3.取出B集合中的新的元素，判断是否需要添加到新集合
        values = otherSet.values()
        for (var i = 0; i < values.length; i++) {
          unionSet.add(values[i])
        }

        return unionSet
      }
```

* 交集

```javascript
      // 交集
      Set.prototype.intersection  =function(otherSet){
        // this集合A
        // otherSet 集合B
        // 1.创建新的集合
        var intersectionSet = new Set()

        // 2.从A中取出一个个元素，判断是否同时存在于集合B中，存放在新集合中
        var values = this.values()
        for(var i = 0;i<values.length;i++){
          var item = values[i]
          if(otherSet.has(item)){
            intersectionSet.add(item)
          }
        }
          
          return intersectionSet
      }
```



* 差集

```javascript
 // 差集（x存在于A当中，但不存在于B当中）
      Set.prototype.difference = function(otherSet) {
        // this集合A
        // otherSet 集合B
        // 1.创建新的集合
        var differenceSet = new Set()

        // 2.取出A集合一个个元素，判断是否同时存在于B中，不存在B中，则添加到新集合中
        var values = this.values()
        for (var i = 0; i < values.length; i++) {
          var item = values[i]
          if (!otherSet.has(item)) {
            differenceSet.add(item)
          }
        }

        return differenceSet
      }
```



* 子集

```javascript
      //子集（A是B的子集）
      Set.prototype.subset = function(otherSet) {
        // this集合A
        // otherSet 集合B
        // 遍历集合A中所有的元素，如果发现集合A中的元素，在集合B中不存在，那么false
        // 如果遍历完了整个集合，依然没有返回false,那么返回true即可
        var values = this.values()
        for (var i = 0; i < values.length; i++) {
          var item = values[i]
          if (otherSet.has(item)) {
            return false
          }
        }
        return true
      }
```



## 字典

* 在JavaScript中，似乎对象本身就是一种字典，所以在早期的JavaScript中，没有字典这种数据类型，因为你完全可以用对象做字典
* ES6添加了Map（映射关系，相当于字典）

# 哈希表

哈希表通常是基于**数组**进行实现的，但是相对于数组，他有很多优势

* 它可以提供非常快速的**插入-删除-查找操作**
* 无论多少数据，插入和删除值需要接近常量的时间：即O(1)的时间级，实际上，只需要**几个机器指令**即可完成
* 哈希表的速度比树还快，基本可以瞬间查找到想要的原元素
* 哈希表相对于树来说编码要容易很多

哈希表相对于数组的劣势

* 哈希表中的数据是**没有顺序的**，所以不能以一种固定的方式（比如从小到大）来遍历其中的元素
* 通常情况下，哈希表的key是**不允许重复的**，不能放置相同的key，用于保存不同的元素

1. 哈希表的原理

* 哈希化：将**大数字**转化成**数组范围内下标**的过程，我们称之为**哈希化**
* 哈希函数：通常我们会将**单词**转成**大数字**，**大数字**在进行**哈希化**的代码实现放在一个函数中，这个函数我们称之为**哈希函数**
* 哈希表：最终数据插入到这个数组，对整个**结构的封装**，我们就称之为一个**哈希表**

2. 解决冲突的方案

* 链地址法（每个下标值存储一个数组，而不是一个单独的对象），也称拉链法

* 开放地址法（寻找空白的单元格）

  * 线性探测（线性的查找空白的单元）

    * 注意：删除一个数据的时候，不能将这个位置下标的内容**设置为null**
    * 因为将它设置为null时可能会影响我们后续的其他操作，所以通常我们删除一个位置的数据项时，我们将它进行特殊的处理（比如设置为-1）
    * 比如我们设置的两个数据都放在了依次的两个null里，当我们删除第一个数据并将它设置为null的时候，我们查找第二个数据时会查找不到（查找的规则是查找原本的位置，没有的话再查找它后面的第一个null,存在数据再查找下一个，不存在数据直接返回不存在）
    * 存在一个严重的问题：聚集（一连串的填充单元），不如我们插入一个32，会发现**连续的单元都不允许**我们放置数据，并且这个过程中我们需要**探索多次**

  * 二次探测（为解决线性探测的问题）

    * 主要优化的是探测时的步长，什么意思呢
    * 步长，线性探测（比如从下标值x开始，x+1,x+2,x+3）,二次探测（x+1^2,x+2^2,x+3^2）,这样就可以依次探测比较长的距离
    
  * 再哈希法（不同的关键字求出不同的地址）
    * 第二次哈希化具备如下特点
      * 和第一个哈希函数不同
      * 不能输出0（否则原地踏步）
  
3. 哈希化的效率

* 如果**没有产生冲突**，那么效率就会更高
* 如果**发生冲突**，存取时间就依赖后来的探测长度
* 平均探测长度以及平均存取时间，取决于**填装因子**，随着填装因子变大，探测长度也越来越长
* 随着填装因子变大，效率下降的情况，将不同开放地址方案中比链地址法更严重，所以我们来对比一下它们的效率，再决定我们选取的方案

4. 在分析效率之前，我们先了解一个概念：装填因子

* 装填因子表示当前哈希表中已经**包含的数据项**和整个**哈希表长度**的**比值**
* 装填因子=总数据项/哈希表长度
* **开放地址法的装填因子**最大是多少呢？1，因为他必须寻找到空白的单元才能将元素放入
* 链地址法的装填因子呢？可以大于1，因为链地址法可以无限的眼伸下去，只要你愿意
* 一般的真实开发中，使用链地址法的情况较多（因为它不会因为添加了某元素后性能急剧下降）

5. 霍纳法则提升时间复杂度(减少了乘法的次数)

* 时间复杂度从O(N^2)降到了O(N)

6. 均匀分布（尽可能使用质数）

* 质数的使用
  * 哈希表的长度
  * N次幂的底数

7. 封装hash函数

```javascript
    // 设计哈希函数
    // 1）将字符串转换成比较大的数字：hashCode
    // 2）将大的数字hashCode压缩到数组范围之内
    function hashFunc(str, size) {
      // 1.定义hashCode变量
      var hashCode = 0

      // 2.霍纳算法，来计算hashCode的值
      // cats -> Unicode编码
      for (var i = 0; i < str.length; i++) {
        hashCode = 37 * hashCode + str.charCodeAt(i)
      }

      // 3.取余操作
      var index = hashCode % size

      return index
    }

    // 测试哈希函数
    alert(hashFunc('abc', 4))
    alert(hashFunc('sdsdd', 4))
    alert(hashFunc('awwe', 4))
    alert(hashFunc('annn', 4))
    alert(hashFunc('wke', 4))
```

8. 哈希表的实现

```javascript
    // 封装哈希表类
    function HashTable() {
      // 属性
      this.storage = []
      this.count = 0
      this.limit = 7 // 容量
        // loadFactor扩容或缩容

      // 方法

      // 哈希函数
      // 设计哈希函数
      // 1）将字符串转换成比较大的数字：hashCode
      // 2）将大的数字hashCode压缩到数组范围之内
      HashTable.prototype.hashFunc = function(str, size) {
        // 1.定义hashCode变量
        var hashCode = 0

        // 2.霍纳算法，来计算hashCode的值
        // cats -> Unicode编码
        for (var i = 0; i < str.length; i++) {
          hashCode = 37 * hashCode + str.charCodeAt(i)
        }

        // 3.取余操作
        var index = hashCode % size

        return index
      }

      // 插入和修改数据（因为key唯一，所以插入和修改是同一种）
      HashTable.prototype.put = function(key, value) {
        // 1.根据key获取索引值（目的是将数据插入到对应的位置）
        var index = this.hashFunc(key, this.limit)

        // 2. 根据索引值取出bucket
        var bucket = this.storage[index]
          // 1）如果桶不存在，创建桶(bucket)，并且放置在该索引的位置
        if (bucket == null) {
          bucket = []
          this.storage[index] = bucket
        }

        // 3.判断新增还是修改原来的值
        // 如果已经有值了，就修改值
        for (var i = 0; i < bucket.length; i++) {
          var tuple = bucket[i]
          if (tuple[0] == key) {
            tuple[1] = value
            return
          }
        }
        // 如果没有，执行后续的添加操作

        // 4.新增操作
        bucket.push([key, value])
        this.count += 1
      }

      // 获取方法
      HashTable.prototype.get = function(key) {
        // 1.根据key获取对应的index
        var index = this.hashFunc(key, this.limit)
          // 2.根据index获取对应的bucket
        var bucket = this.storage[index]
          // 3.判断bucket是否为null，如果为null,直接返回null
        if (bucket == null) {
          return null
        }
        // 4.线性查找bucket中每一个key是否等于传入的key，如果等于，直接返回对应的value
        for (var i = 0; i < bucket.length; i++) {
          var tuple = bucket[i]
          if (tuple[0] == key) {
            return tuple[1]
          }
        }
        // 5.遍历完后，依然没有找到对应的key,return null即可
        return null
      }

      // 删除操作
      HashTable.prototype.remove = function(key) {
        // 1.根据key获取对应的index
        var index = this.hashFunc(key, this.limit)
          // 2.根据index获取bucket
        var bucket = this.storage[index]
          // 3.判断bucket是否存在，如果不存在，那么直接返回null
        if (bucket == null) {
          return null
        }
        // 4.线性查找bucket,寻找对应的数据，并且删除
        for (var i = 0; i < bucket.length; i++) {
          var tuple = bucket[i]
          if (tuple[0] == key) {
            bucket.splice(i, 1)
            this.count--
              return tuple[1]
          }
        }
        // 5.依然没有找到那么返回null
        return null
      }

      // 其他方法
      // 判断哈希表是否为null
      HashTable.prototype.isEmpty = function() {
        return this.count == 0
      }

      // 获取哈希表中元素的个数
      HashTable.prototype, size = function() {
        return this.count
      }
    }

    var myInfo = new HashTable()
    myInfo.put('gaozheng', {
      age: 18,
      height: 175,
      weight: 65
    })

    console.log(myInfo);

    var ht = new HashTable()
      // 1.插入数据
    ht.put('cba', 'one')
    ht.put('bca', 'two')
    ht.put('nba', 'three')
    ht.put('bbb', 'four')
    ht.put('mba', 'six')
    console.log(ht);
    // 2.修改数据
    ht.put('cba', 'five')
    console.log(ht)
      //   // 3.获取数据
    console.log(ht.get('cba'))
      //   // 4.删除数据
    ht.remove('cba')
    console.log(ht);
```

9. 哈希表的扩容/缩容

* 为什么需要扩容

目前，所有数据项都存在**长度为7的数组中**，因为使用的是链地址法，**loadFactor**可以大于1，所以这个哈希表可以无限制的插入新数据，但是随着数据量的增多，每个index对应的bucket会越来越长，也就造车过了**效率的降低**，所以需要扩容

* 如何扩容

可以简单的将容量**扩大两倍**（不是质数，质数第10会解决），但是这种情况下，所有的**数据项一定要同时进行修改**

* 什么情况下扩容

比较常见的是loadFactor>0.75的时候进行扩容

* 代码(再在add和remove方法后判断是否扩容)

```javascript
 // 哈希表的扩容
      HashTable.prototype.resize = function(newLimit) {
        // 1.保存旧的数组内容
        var oldStorage = this.storage

        // 2.重置所有的属性
        this.storage = []
        this.count = 0
        this.limit = newLimit

        // 3.遍历oldStorage中所有的bucket
        for (var i = 0; i < oldStorage.length; i++) {
          // 3.1取出对应的bucket
          var bucket = oldStorage[i]

          // 3.2判断bucket是否为null
          if (bucket == null) {
            continue
          }

          // 3.3 bucket中有数据，那么取出数据，重新插入
          for (var j = 0; j < bucket.length; j++) {
            var tuple = bucket[j]
            this.put(tuple[0], tuple[1])
          }
        }
      }
```

10. 容量质数（有利于均匀分布）

* 判断一个数是否是质数

```javascript
function isPrime(num){
    for(var i =2;i<num;i++){
        if(num%i==0){
            return false
        }
    }
    return true
}
```

* 但是这种方法效率并不高，可以只遍历它的开平方根之前（因为一个数如果不是质数，它能被两个数相乘得到，那么其中一个数一定小于它的开平方根）

  ```javascript
      // 更高效的方式
      function isPrime(num) {
        // 1.获取平方根
        var temp = parseInt(Math.sqrt(num))
  
        // 2.循环判断
        for (var i = 2; i <= temp; i++) {
          if (num % i == 0) {
            return false
          }
        }
  
        return true
      }
  ```

* 代码

  * 定义判断质数方法
  * 定义获取质数的方法
  * 在添加和删除的扩容缩容方法中得到一个质数的容量

  ```
        // 判断某个数字是否是质数
        HashTable.prototype.isPrime = function(num) {
          // 1.获取平方根
          var temp = parseInt(Math.sqrt(num))
  
          // 2.循环判断
          for (var i = 2; i <= temp; i++) {
            if (num % i == 0) {
              return false
            }
          }
  
          return true
        }
  
        // 获取质数方法
        HashTable.prototype.getPrime = function(num) {
          // 14-->17
          // 34-->37
          while (!this.isPrime(num)) {
            num++
          }
          return num
        }
  ```

* 哈希表完整代码

```javascript
   // 封装哈希表类
    function HashTable() {
      // 属性
      this.storage = []
      this.count = 0
      this.limit = 7 // 容量
        // loadFactor扩容或缩容

      // 方法

      // 哈希函数
      // 设计哈希函数
      // 1）将字符串转换成比较大的数字：hashCode
      // 2）将大的数字hashCode压缩到数组范围之内
      HashTable.prototype.hashFunc = function(str, size) {
        // 1.定义hashCode变量
        var hashCode = 0

        // 2.霍纳算法，来计算hashCode的值
        // cats -> Unicode编码
        for (var i = 0; i < str.length; i++) {
          hashCode = 37 * hashCode + str.charCodeAt(i)
        }

        // 3.取余操作
        var index = hashCode % size

        return index
      }

      // 插入和修改数据（因为key唯一，所以插入和修改是同一种）
      HashTable.prototype.put = function(key, value) {
        // 1.根据key获取索引值（目的是将数据插入到对应的位置）
        var index = this.hashFunc(key, this.limit)

        // 2. 根据索引值取出bucket
        var bucket = this.storage[index]
          // 1）如果桶不存在，创建桶(bucket)，并且放置在该索引的位置
        if (bucket == null) {
          bucket = []
          this.storage[index] = bucket
        }

        // 3.判断新增还是修改原来的值
        // 如果已经有值了，就修改值
        for (var i = 0; i < bucket.length; i++) {
          var tuple = bucket[i]
          if (tuple[0] == key) {
            tuple[1] = value
            return
          }
        }
        // 如果没有，执行后续的添加操作

        // 4.新增操作
        bucket.push([key, value])
        this.count += 1

        // 5.判断是否需要扩容操作
        if (this.count > this.limit * 0.75) {
          var newSize = this.limit * 2
          var newPrime = this.getPrime(newSize)
          this.resize(newPrime)
        }
      }

      // 获取方法
      HashTable.prototype.get = function(key) {
        // 1.根据key获取对应的index
        var index = this.hashFunc(key, this.limit)
          // 2.根据index获取对应的bucket
        var bucket = this.storage[index]
          // 3.判断bucket是否为null，如果为null,直接返回null
        if (bucket == null) {
          return null
        }
        // 4.线性查找bucket中每一个key是否等于传入的key，如果等于，直接返回对应的value
        for (var i = 0; i < bucket.length; i++) {
          var tuple = bucket[i]
          if (tuple[0] == key) {
            return tuple[1]
          }
        }
        // 5.遍历完后，依然没有找到对应的key,return null即可
        return null
      }

      // 删除操作
      HashTable.prototype.remove = function(key) {
        // 1.根据key获取对应的index
        var index = this.hashFunc(key, this.limit)
          // 2.根据index获取bucket
        var bucket = this.storage[index]
          // 3.判断bucket是否存在，如果不存在，那么直接返回null
        if (bucket == null) {
          return null
        }
        // 4.线性查找bucket,寻找对应的数据，并且删除
        for (var i = 0; i < bucket.length; i++) {
          var tuple = bucket[i]
          if (tuple[0] == key) {
            bucket.splice(i, 1)
            this.count--
              return tuple[1]
          }
        }
        // 5.缩小容量
        if (this.count > 7 && this.count < this.limit * 0.25) {
          var newSize = Math.floor(this.limit / 2)
          var newPrime = this.getPrime(newSize)
          this.resize(newPrime)
        }
        // 6.依然没有找到那么返回null
        return null
      }

      // 其他方法
      // 判断哈希表是否为null
      HashTable.prototype.isEmpty = function() {
        return this.count == 0
      }

      // 获取哈希表中元素的个数
      HashTable.prototype, size = function() {
        return this.count
      }

      // 哈希表的扩容/缩容
      HashTable.prototype.resize = function(newLimit) {
        // 1.保存旧的数组内容
        var oldStorage = this.storage

        // 2.重置所有的属性
        this.storage = []
        this.count = 0
        this.limit = newLimit

        // 3.遍历oldStorage中所有的bucket
        for (var i = 0; i < oldStorage.length; i++) {
          // 3.1取出对应的bucket
          var bucket = oldStorage[i]

          // 3.2判断bucket是否为null
          if (bucket == null) {
            continue
          }

          // 3.3 bucket中有数据，那么取出数据，重新插入
          for (var j = 0; j < bucket.length; j++) {
            var tuple = bucket[j]
            this.put(tuple[0], tuple[1])
          }
        }
      }

      // 判断某个数字是否是质数
      HashTable.prototype.isPrime = function(num) {
        // 1.获取平方根
        var temp = parseInt(Math.sqrt(num))

        // 2.循环判断
        for (var i = 2; i <= temp; i++) {
          if (num % i == 0) {
            return false
          }
        }

        return true
      }

      // 获取质数方法
      HashTable.prototype.getPrime = function(num) {
        // 14-->17
        // 34-->37
        while (!this.isPrime(num)) {
          num++
        }
        return num
      }
    }
```

（注意在删除和插入操作时都做了质数变换）