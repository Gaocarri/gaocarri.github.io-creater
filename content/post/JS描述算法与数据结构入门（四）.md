---
# 常用定义
title: "JS描述数据结构与算法（四）"           # 标题
date: 2020-01-25   # 创建时间
draft: false                       # 是否是草稿？
tags: ["数据结构与算法","数据结构"]  # 标签
categories: ["数据结构与算法"]              # 分类
author: "Carri"                  # 作者
keywords: ["数据结构与算法","数据结构"]
description : "JS描述数据结构与算法（四）"   

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: false       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

* 大过年仍然坚持学习
* 包含树与二叉搜索树

## 树

1. 树的优点

* 综合了其他数据结构的优点（当然优点不足于盖过其他数据结构的优点，比如效率一般情况下没有哈希表高），并且弥补了上面数据结构的缺点
* 树结构的非线性使得它可以表示一对多的关系
* 比如文件的目录结构

2. 树的术语

* 树：n(n>=0)个节点构成的有限集合，当n=0时，称为空树
* 对于一个非空树，它有以下性质
  * 树中有一个称为"根（Root)"的特殊节点，用r表示
  * 其余节点可分为m(m>0)个互不相交的有限集T1，T2，...Tm,其中每个集合本身又是一颗树，称为原来树的“子树（SubTree）
* 节点的度（Degree）：节点的子树个数
* 树的度：树的所有节点中最大的度数
* 叶节点（Leaf）：度为0的节点
* 父节点：有子树的节点是其子树的根节点的父节点
* 子节点：若A节点是B节点的父节点，则称B节点是A节点的子节点；子节点也称孩子节点
* 兄弟节点（Sibling）：具有同一父节点的各节点彼此是兄弟节点
* 路径和路径长度：从节点n1到nk的路径为一个节点序列n1,n2,...nk，ni是ni+1的父节点。路径所包含边的个数为路径的长度
* 节点的层次（Leavel）：规定根节点在1层，其他任一节点的层数是其父节点的层数加1
* 树的深度（Depth）：树中所有节点中的最大层次是这棵树的深度

2. 树的表示

* 儿子，兄弟表示法

```javascript
Node {
    This.data = data
    This.leftChild = B
    This.sibling = C
}
```

任何一棵树，都可以用二叉树模拟出来

3. 二叉树的概念

* 如果树中的每个节点最多只能有两个子节点，这样的树称为'二叉树'
* 二叉树可以为空，也就是没有节点
* 若不为空，则它是由根节点和称为其左子树TL和右子树TR的两个不相交的二叉树组成
* 二叉树的5种形态：空，只有自身，自身加左子节点，自身加右子节点，自身加左右子节点

4. 二叉树的特性

* 一个二叉树第i层的最大节点数为2^(i-1),i>=1
* 深度为k的二叉树有最大节点总数为：2^k-1,k>=1
* 对任何非空二叉树T,若n0表示节点的个数，n2是度2为2的非叶节点个数，那么两者满足关系n0=n2+1

5. 特殊二叉树

* 完美二叉树：除了最下层的叶子节点外，每个节点都有二个子节点
* 完全二叉树：
  * 除二叉树最后一层外，其他各层的节点数都达到了最大个数
  * 且最后一层从左向右的叶节点连续存在，只缺右侧若干节点
  * 完美二叉树是特殊的完全二叉树

6. 二叉树的存储

常见方式是数组和链表

* 使用数组
  * 完全二叉树：从上往下，从左到右
  * 非完全二叉树：转成完全二叉树才能按上面的方案存储，但是会造成很大的空间浪费
* 使用链表（二叉树最常见的方式）
  * 每个节点封装成一个node，node中包含存储的数据，左节点的引用，右节点的引用

7. 二叉搜索树（也称为二叉排序树或者二叉查找树,BST）

* 二叉搜索树是一棵二叉树，可以为空
* 如果不为空
  * 非空左子树的所有键值小于其根节点的键值
  * 非空右子树的所有键值大于其根节点的键值
  * 左，右子树本身也都是二叉搜索树

8. 二叉搜索树的封装

```javascript
    // 封装二叉搜索树
    function BinarySearchTree() {

      function Node(key) {
        this.key = key
        this.left = null
        this.right = null
      }

      // 属性
      this.root = null

      // 方法
      // 1.插入数据
      BinarySearchTree.prototype.insert = function(key) {
        // 1.根据key创建节点
        var newNode = new Node(key)

        // 2.判断根节点是否有值
        // 有
        if (this.root == null) {
          this.root = newNode
        } else {
          this.insertNode(this.root, newNode)
        }
        // 无
      }



      // 内部的方法
      BinarySearchTree.prototype.insertNode = function(node, newNode) {
        if (newNode.key < node.key) { // 向左查找
          if (node.left == null) {
            node.left = newNode
          } else {
            this.insertNode(node.left, newNode)
          }
        } else { // 向右查找
          if (node.right == null) {
            node.right = newNode
          } else {
            this.insertNode(node.right, newNode)
          }
        }
      }

      // 2.树的遍历
      // 1.先序遍历
      BinarySearchTree.prototype.preOrderTraversal = function(handler) {
          this.preOrderTraversalNode(this.root, handler)
        }
        // 内部的方法
      BinarySearchTree.prototype.preOrderTraversalNode = function(node, handler) {
        if (node != null) {
          // 1.处理经过的节点
          handler(node.key)

          // 2.查找经过节点的左子节点
          this.preOrderTraversalNode(node.left, handler)

          // 3.查找经过节点的右子节点
          this.preOrderTraversalNode(node.right, handler)
        }
      }

      // 2.中序遍历
      BinarySearchTree.prototype.midOrderTraversal = function(handler) {
        this.midOrderTraversalNode(this.root, handler)
      }

      BinarySearchTree.prototype.midOrderTraversalNode = function(node, handler) {
        if (node != null) {
          // 1.查找左子树中的节点
          this.midOrderTraversalNode(node.left, handler)

          // 2.处理节点
          handler(node.key)

          // 3.查找右子树中的节点
          this.midOrderTraversalNode(node.right, handler)
        }
      }

      // 3.后序遍历
      BinarySearchTree.prototype.postOrderTraversal = function(handler) {
        this.postOrderTraversalNode(this.root, handler)
      }
      BinarySearchTree.prototype.postOrderTraversalNode = function(node, handler) {
        if (node != null) {
          // 1.查找左子树的节点
          this.postOrderTraversalNode(node.left, handler)
            // 2.查找右子树的节点
          this.postOrderTraversalNode(node.right, handler)
            // 3.处理节点
          handler(node.key)
        }
      }

      // 3.查找最值
      BinarySearchTree.prototype.max = function() {
        // 1.获取根节点
        var node = this.root
          // 2.依次不断的向右查找，直到节点为null
        while (node.right != null) {
          node = node.right
        }
        return node.key
      }

      BinarySearchTree.prototype.min = function() {
        var node = this.root
        while (node.left != null) {
          node = node.left
        }
        return node.key
      }

      // 搜索特定的key
      BinarySearchTree.prototype.search = function(key) {
        // 1.获取根节点
        var node = this.root
          // 2.循环搜索key
        while (node != null) {
          if (key < node.key) {
            node = node.left
          } else if (key > node.key) {
            node = node.right
          } else {
            return true
          }
        }
        return false
      }

      // 删除节点
      BinarySearchTree.prototype.remove = function(key) {
        // 1.寻找要删除的节点
        // 1.1定义变量，保存一些信息
        var current = this.root
        var parent = null
        var isLeftChild = true
          // 1.2开始寻找删除的节点
        while (current.key != key) {
          parent = current
          if (key < current.key) {
            isLeftChild = true
            current = current.left
          } else {
            isLeftChild = false
            current = current.right
          }
          // 某种情况：已经找到叶子节点，依然没有==key
          if (current == null) return false
        }
        // 2.根据对应的情况删除节点
        // 找到current.key==key
        // 2.1删除的节点是叶子节点(没有子节点)
        if (current.left == null && current.right == null) {
          // 是根节点
          if (current == this.root) {
            this.root == null
          } else if (isLeftChild) { // 叶子节点
            parent.left = null
          } else {
            parent.right = null
          }
        }
        // 2.2删除的节点有一个子节点
        else if (current.right == null) {
          if (current == this.root) {
            this.root = current.left
          } else if (isLeftChild) {
            parent.left = current.left
          } else {
            parent.right = current.left
          }
        } else if (current.left == null) {
          if (current == this.root) {
            this.root = current.right
          } else if (isLeftChild) {
            parent.left = current.right
          } else {
            parent.right = current.right
          }
        }
        // 2.3删除的节点有两个节点(后继)
        else {
          // 1.获取后继节点
          var successor = this.getSuccessor(current)

          // 2.判断是否是根节点
          if (current == this.root) {
            this.root = successor
          } else if (isLeftChild) {
            parent.left = successor
          } else {
            parent.right = successor
          }

          // 3.将删除的左子树=current.left
          successor.left = current.left
        }
      }

      // 找后继的方法
      BinarySearchTree.prototype.getSuccessor = function(delNode) {
        // 1.定义变量，保存找到的后继
        var successor = delNode
        var current = delNode.right
        var successorParent = delNode

        // 2.循环查找
        while (current != null) {
          successorParent = successor
          successor = current
          current = current.left
        }

        // 3.判断寻找的后继节点是否直接就是delNode的right节点
        if (successor != delNode.right) {
          successorParent.left = successor.right
          successor.right = delNode.right
        }

        return successor
      }
    }

    // 测试方法
    var bst = new BinarySearchTree()

    // 1.插入数据
    bst.insert(11)
    bst.insert(22)
    bst.insert(13)
    bst.insert(12)
    bst.insert(9)
    bst.insert(9)
    bst.insert(16)

    // 2. 测试遍历
    // 先序遍历
    var resultString = ''
    bst.preOrderTraversal(function(key) {
      resultString += key + ' '
    })
    console.log(resultString);
    // 中序遍历
    resultString = ''
    bst.midOrderTraversal(function(key) {
      resultString += key + ' '
    })
    console.log(resultString);
    // 后序遍历
    resultString = ''
    bst.postOrderTraversal(function(key) {
      resultString += key + ' '
    })
    console.log(resultString);
    // 测试最值
    console.log(bst.max());
    console.log(bst.min());
    // 测试搜索
    console.log(bst.search(26), bst.search(11));
```

9. 删除操作思路

* 先找到要删除的节点，如果没找到，不需要删除
* 找到要删除的节点
  * 删除叶子节点
  * 删除只有一个子节点的节点
  * 删除有两个子节点的节点
* 删除的节点有两个节点的情况
  * 如果要删除的节点有两个子节点，甚至子节点还有子节点，这种情况下我们需要从下面的子节点中找到一个节点，来替换当前的节点。
  * 但是找到的这个节点有什么特征呢？应该是current节点下面所有节点中最接近current节点的
    * 要么比current节点小一点点，要么比current节点大一点点
    * 最接近current，你就可以用来替换current的位置
  * 这个节点怎么找呢
    * 比current小一点点的节点，一定是current左子树的最大值
    * 比current大一点点的节点，一定是current右子树的最小值
  * 前驱&后继
    * 二叉搜索树中，这两个特别的节点，有两个特别的名字
    * 比current小一点的节点，称为current节点的前驱
    * 比current大一点的节点，称为current节点的后驱
  * 要么找他的前驱，要么找他的后驱
  * 所以，要先找到这样的节点