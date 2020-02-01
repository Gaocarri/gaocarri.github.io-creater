---
# 常用定义
title: "JS描述数据结构与算法（五）"           # 标题
date: 2020-01-28    # 创建时间
draft: false                       # 是否是草稿？
tags: ["数据结构与算法","数据结构"]  # 标签
categories: ["数据结构与算法"]              # 分类
author: "Carri"                  # 作者
keywords: ["数据结构与算法","数据结构"]
description : "JS描述数据结构与算法（五）"   

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: false       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---
* 包含红黑树与图论

## 红黑树

1. 保持二叉搜索树的平衡性

为了能以较快的时间O(logN)来操作一棵树，我们需要保证树总是平衡的

- 至少大部分是平衡的，那么时间复杂度也是接近O(logN)的
- 也就是说树中**每个节点左边的子孙节点**的个数，应该尽可能的**等于右边子孙节点的个数**
- 常见的平衡树：
  - AVL树
  - 红黑树

2. 红黑树的规则

红黑树除了符合二叉搜索树的基本规则外，还添加了以下性质：

* 节点是红色或者黑色
* 根节点是黑色
* 每个叶子节点都是黑色的空节点（NIL节点）
* 每个红色节点的两个子节点都是黑色。（从每个叶子到根的所有路径上不能有两个连续的红色节点）
* 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点

3. 红黑树的相对平衡

* 从**根到叶子**的**最长可能路径**，不会超过**最短可能路径**的**两倍长**
* 结果就是这个树**基本**是平衡的
* 虽然没有做到绝对的平衡，但是可以保证在最坏的情况下，依然是高效的
* 为什么可以做到最长的路径不超过最短路径的两倍长
  * **性质4**决定了路径不能有两个相连的红色节点
  * 最短的可能路径都是黑色节点
  * 最长的可能路径都是黑色节点
  * **性质5**所有路径都有相同数目的黑色节点
  * 这就表明了没有路径能多余其他路径的两倍长

4. 变色

* 插入一个新节点时，有可能树不再平衡，可以通过三种方式的变换，让树保持平衡
  * 换色-左旋转-右旋转
* 变色
  * 为了重新符合红黑树的规则，尝试把**红色节点**变为黑色、或者把**黑色**节点变为红色
* 首先，需要直到**插入的新的节点**通常是**红色节点**
  * 因为在**插入节点为红色**的时候，有可能插入依次是**不违反红黑树任何规则**
  * 而**插入黑色节点**，必然会导致有一条路径上**多了黑色节点**，这是很难调整的
  * 红色节点可能导致出现**红红相连**的情况，但是这种情况可以通**过颜色变换和旋转来调整**

5. 旋转

* 左旋转
  * 逆时针旋转红黑树的两个节点，使得父节点被自己的右孩子取代，而自己成为自己的左孩子
  * 它们有子树是否会影响旋转？ 答：不会
* 右旋转
  * 顺时针旋转红黑树的两个节点，使得父节点被自己的左孩子取代，而自己成为自己的右孩子
  * 它们有子树是否会影响旋转？答：不会

6. 插入操作

   设要插入**节点为N**，其**父节点为P**

   其**祖父节点为G**，其父亲的**兄弟节点为U**（即P和U是同一个节点的子节点）

* 情况一

  ![红黑树插入情况1](/images/红黑树插入情况1.jpg)

  * 新节点N位于树的根上，没有父节点
  * 这种情况下，我们直接将红色变为黑色即可，这样满足性质2

* 情况二

  ![红黑树插入情况2](/images/红黑树插入情况2.jpg)

  * 新节点的父节点P是黑色
  * 性质四没有失效（新节点是红色的），性质5也没有人和问题
  * 尽管新节点N有两个黑色的叶子节点NIL，但是新节点N是红色的，所以通过它的路径中黑色节点的个数依然相同，满足性质5

* 情况三

  ![红黑树插入情况3](/images/红黑树插入情况3.jpg)

  * P为红色，U也是红色
  * 父红，叔红，祖黑
  * 变为 父黑，叔黑，祖红
  * 可能出现的问题
    * 但是,G的父节点也可能是红色，这就违反了性质3，可以递归地调整颜色
    * 但是如果递归调整颜色到了根节点，就需要进行旋转了（见后例）

* 情况四

  ![红黑树插入情况4](/images/红黑树插入情况4.jpg)

  * N的叔叔U是黑色，且N是左儿子
  * 父红，叔黑，祖黑，N是左儿子
  * 变成父黑祖红，进行右旋转，（先变色再旋转）

* 情况五

  ![红黑树插入情况5](/images/红黑树插入情况5.jpg)

  * N的叔叔U是黑色节点，且N是右孩子
  * 父红，叔黑，祖黑，且N是右儿子
  * 先以P为根进行左旋转，将P作为新插入的红色节点考虑即可，此时同情况四，将自己变成黑色，祖父变成红色，以祖为根进行左旋转

7. 插入案例

* 依次插入10 9 8 7 6 5 4 3 2 1

![](/images/红黑树插入案例1.jpg)

![](/images/红黑树插入案例2.jpg)

![](/images/红黑树插入案例3.jpg)



## 图论

1. 什么是图

* 图结构是一种与树结构有些相似的树结构
* 图论是数学的一个分支，并且，在数学的概念上，**树是图的一种**
* 它以图为研究对象，研究**顶点**和**边**组成的**图形的数学理论和方法**
* 主要研究的目的是**事物之间的关系**，**顶点**代表**事物**，**边**代表两个事物间的**关系**

2. 六度空间理论

* 理论上认为世界上任何两个互相不认识的两人
* 只需要很少的中间人就可以建立联系
* 并非一定要经过6步，只是需要很少的步骤

3. 图的现实案例

* 人的关系网
* 北京地铁线路
* 村庄间的关系网
* 互联网各个终端
* ......

4. 图的通常特点

* 一组顶点：通常用E（Vertex）表示顶点的集合
* 一组边：通常用E（Edge）表示边的集合
* 边可以是无向的，也可以是有向的
* 比如A --- B,通常表示无向，A --> B,通常表示有向

5. 图一笔画的充要条件

* 奇点的数目不是0个就是2个
* 连到一点的**边的数目**如是奇数条，就称为奇点
* 如果是**偶数条**就成为偶点
* 要想一笔画成，必须中间点均是偶点
* 也就是有来路必有另一条去路，奇点只能在两端，因此任何图能一笔画成，奇点要么没有要么在两端

6. 图的术语

* 顶点
  * 表示图的一个**节点**
  * 如某个村庄/某个车站/人际关系中的人
*  边
  * 表示顶点与顶点之间的连线
  * 注意不要称为路径，路径有别的含义
* 相邻顶点
  * 由一条边相连的两个节点
* 度
  * 一个顶点的度是**相邻顶点的数量**
* 路径
  * 路径是顶点到另一个顶点
  * 简单路径：简单路径要求不包含重复的顶点，比如 0  1 5 9 是一条简单路径
  * 回路：第一个顶点和最后一个顶点**相同**的路径称为回路
* 无向图，有向图
  * 无向图：任何边都没有方向
  * 有向图：边有方向
* 无权图，带权图
  * 无权图：不表达边的长短，意义
  * 带权图：表示边有一定的权重，比如距离或者花费的时间或者票价（任意你希望表示的数据）

7. 图的表示

* 邻接矩阵

  * 每个节点和一个整数相关联，该整数作为数组的下标值

  * 我们用一个二维数组来表示顶点之间的连接

  * 二维数组0 2 --> A --> C

  * 在二维数组中，0表示没有连线，1表示有连线

  * 通过二维数组，可以很快找到一个顶点和哪些顶点有联系

    A  B  C  D  E

  A     0  1   0  1  1

  B    ......

  C	......

  D    ......

  E	......

* 邻接表

  * 由图中每个顶点以及顶点相邻的顶点列表组成
  * 这个列表有很多种方式来存储：**数组/链表/字典（哈希表）**都可以

8. 使用邻接表封装图

使用到了队列和字典

```javascript
<script src="./dictionary.js"></script>
  <script src="./queue.js"></script>
  <script>
    // 封装图结构
    function Graph() {
      // 属性：顶点(数组)/边(字典)
      this.vertexes = []
      this.edges = new Dictionary() //边

      // 方法
      // 添加方法
      // 1.添加顶点的方法
      Graph.prototype.addVertex = function(v) {
          this.vertexes.push(v)
            // 先给顶点设置一个空的数组
          this.edges.set(v, [])
        }
        // 2.添加边的方法
      Graph.prototype.addEdge = function(v1, v2) {
        this.edges.get(v1).push(v2)
        this.edges.get(v2).push(v1)
      }

      // 2.toString方法
      Graph.prototype.toString = function() {
          // 1.定义字符串保存当前结果
          var resultString = ""

          // 2.遍历所有的顶点，以及顶点对应的边
          for (var i = 0; i < this.vertexes.length; i++) {
            resultString += this.vertexes[i] + '-->'
            var vEdges = this.edges.get(this.vertexes[i])
            for (var j = 0; j < vEdges.length; j++) {
              resultString += vEdges[j] + ' '
            }
            resultString += '\n'
          }
          return resultString
        }
        // 3.图的遍历
        // 初始化状态颜色
      Graph.prototype.initializeColor = function() {
          var colors = []
          for (var i = 0; i < this.vertexes.length; i++) {
            colors[this.vertexes[i]] = 'white'
          }
          return colors
        }
        // 实现广度优先搜索BFS
      Graph.prototype.bfs = function(initV, handler) {
        // 1.初始化颜色 
        var colors = this.initializeColor()
          // 2.创建队列
        var queue = new Queue()
          // 3.将顶点加入到队列中
        queue.enqueue(initV)
          // 4.循环从队列中取出元素
        while (!queue.isEmpty()) {
          // 4.1 从队列中取出一个顶点
          var v = queue.dequeue()
            // 4.2获取和顶点相连的另外顶点
          var vList = this.edges.get(v)
            // 4.3将v的颜色设置成灰色
          colors[v] = 'gray'
            // 4.4遍历所有的顶点，加入到队列中
          for (var i = 0; i < vList.length; i++) {
            var e = vList[i]
            if (colors[e] == 'white') {
              colors[e] = 'gray' // 就不会重复加了
              queue.enqueue(e)
            }
          }
          // 4.5访问顶点
          handler(v)
            // 4.6将顶点设置为黑色
          colors[v] = 'black'
        }
      }

      // 深度优先搜索DFS
      Graph.prototype.dfs = function(initV, handler) {
        // 1.初始化颜色
        var colors = this.initializeColor()
          // 2.从某个顶点开始依次递归访问
        this.dfsVisit(initV, colors, handler)
      }

      Graph.prototype.dfsVisit = function(v, colors, handler) {
        // 1.将颜色设置为灰色
        colors[v] = 'grey'
          // 2.处理v顶点
        handler(v)
          // 3.访问v相邻的顶点
        var vList = this.edges.get(v)
        for (var i = 0; i < vList.length; i++) {
          var e = vList[i]
          if (colors[e] == 'white') {
            this.dfsVisit(e, colors, handler)
          }
        }
        // 4.将v设置成黑色
        colors[v] = 'black'
      }
    }

    // 测试代码
    // 1.创建图结构
    var g = new Graph()

    // 2.添加顶点
    var myVertexes = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I']
    for (var i = 0; i < myVertexes.length; i++) {
      g.addVertex(myVertexes[i])
    }
    // 3.添加边
    g.addEdge('A', 'B')
    g.addEdge('A', 'C')
    g.addEdge('A', 'D')
    g.addEdge('C', 'D')
    g.addEdge('C', 'G')
    g.addEdge('D', 'G')
    g.addEdge('D', 'H')
    g.addEdge('B', 'E')
    g.addEdge('B', 'F')
    g.addEdge('E', 'I')
    console.log(g);
    // 4.测试toString
    console.log(g.toString())
      // 5.测试BFS遍历
    var result = ''
    g.bfs(g.vertexes[0], function(v) {
      result += v + ' '
    })
    console.log(result);
    var result = ''
    g.dfs(g.vertexes[0], function(v) {
      result += v + ' '
    })
    console.log(result);
  </script>
```

1. 图的遍历

* 图的遍历思想
  * 图的遍历思想和树的遍历思想是一样的
  * 图的遍历意味着需要将图中**每个顶点访问一边**，并且不能有重复
* 遍历的两种算法
  * 广度优先搜索(Breadth-First Search,简称**BFS**)
  * 深度优先搜索（Dep-First Search,简称**DFS**)
  * 两种遍历算法，都需要明确指定**第一个被访问的顶点**
* BFS:基于队列，入队列的顶点先被探索（图片来源博客：https://www.cnblogs.com/skywang12345/p/3711483.html）

![](/images/广度优先.jpg)

* DFS：基于栈或者使用递归，通过将顶点存入栈中，顶点是沿着路径被探索的，存在新的相邻顶点就去访问（图片来源博客：https://www.cnblogs.com/skywang12345/p/3711483.html）

![](/images/深度优先.jpg)

* 为了记录顶点是否被访问过，我们使用三种颜色来反应它们的状态
  * 白色：该顶点未被访问
  * 灰色：表示该顶点被访问过，但未被探索
  * 黑色：表示该顶点被访问且被完全探索过