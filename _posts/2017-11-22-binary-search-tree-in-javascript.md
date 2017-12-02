---
title: Binary-Search-Tree in JavaScript
date: 2017-11-22 01:20:13
tags: 算法
categories: 算法
---

这里用 JavaScript 实现一个二叉搜索树。二叉搜索树可以很方便地进行排序。下面的实现比较简单，很容易就会出现非平衡二叉树。G.M. Adelson-Velsky 和 E.M. Landis 在 1962 年的论文《An Algorithm For The Organization Of Information》中提出了一种可以实现自平衡二叉树，后世称为 AVL 树。

除了 AVL 树，还有很多其他的平衡二叉树的实现，例如 [AVL树，红黑树，B树，B+树，Trie树都分别应用在哪些现实场景中？](https://www.zhihu.com/question/30527705) 就是个不错的入门级参考，可以大致了解这些平衡树的特点、区别。看起来，红黑树是应用最广泛的。

<!-- more -->

``` javascript
/**
 * 二叉搜索树构造函数
 */
function BinarySearchTree() {
  var Node = function(key) {
    this.key = key;
    this.left = null;
    this.right = null;
  };

  var root = null;

  /**
   * 插入值
   */
  this.insert = function(key) {
    var newNode = new Node(key);

    if (root === null) {
      root = newNode;
    } else {
      insertNode(root, newNode);
    }
  };

  function insertNode(node, newNode) {
    if (newNode.key < node.key) {
      if (node.left === null) {
        node.left = newNode;
      } else {
        insertNode(node.left, newNode);
      }
    } else {
      if (node.right === null) {
        node.right = newNode;
      } else {
        insertNode(node.right, newNode);
      }
    }
  }

  /**
   * 中序遍历二叉树
   */
  this.inOrderTraverse = function(cb) {
    inOrderTraverseNode(root, cb);
  };

  function inOrderTraverseNode(node, cb) {
    if (node !== null) {
      inOrderTraverseNode(node.left, cb);
      cb(node);
      inOrderTraverseNode(node.right, cb);
    }
  }

  /**
   * 先序遍历二叉树
   */
  this.preOrderTraverse = function(cb) {
    preOrderTraverseNode(root, cb);
  };

  function preOrderTraverseNode(node, cb) {
    if (node !== null) {
      cb(node);
      preOrderTraverseNode(node.left, cb);
      preOrderTraverseNode(node.right, cb);
    }
  }

  /**
   * 后序遍历二叉树
   */
  this.postOrderTraverse = function(cb) {
    postOrderTraverseNode(root, cb);
  };

  function postOrderTraverseNode(node, cb) {
    if (node !== null) {
      postOrderTraverseNode(node.left, cb);
      postOrderTraverseNode(node.right, cb);
      cb(node);
    }
  }

  /**
   * 获取最小值节点
   */
  this.min = function() {
    return findMinNode(root);
  };

  function findMinNode(node) {
    var n = node;
    while(n && n.left) {
      n = n.left;
    }

    return n;
  }

  /**
   * 移除一个节点
   */
  this.remove = function(key) {
    root = removeNode(root, key);
  }

  function removeNode(node, key) {
    if (node === null) {
      return null;
    }

    if (key < node.key) {
      node.left = removeNode(node.left, key);
      return node;
    } else if (key > node.key) {
      node.right = removeNode(node.right, key);
      return node;
    } else {
      if (node.left === null && node.right === null) {
        node = null;
        return node;
      } else if (node.left === null) {
        node = node.right;
        return node;
      } else if (node.right === null) {
        node = node.left;
        return node;
      }

      var aux = findMinNode(node.right);
      node.key = aux.key;
      node.right = removeNode(node.right, aux.key);
      return node;
    }
  }
}


/**
 * 测试代码
 */

var bst = new BinarySearchTree();

bst.insert(17);
bst.insert(1);
bst.insert(2);
bst.insert(1);
bst.insert(10);
bst.insert(4);
bst.insert(15);
bst.insert(6);

console.log('\n中序遍历二叉树 =>\n');
bst.inOrderTraverse(function(node) {
  console.log(node.key);
});

console.log('\n先序遍历二叉树 =>\n');
bst.preOrderTraverse(function(node) {
  console.log(node.key);
});

console.log('\n后序遍历二叉树 =>\n');
bst.postOrderTraverse(function(node) {
  console.log(node.key);
});

console.log('\nmin node =>', bst.min().key);

bst.remove(2);
console.log('\n先序遍历二叉树 =>\n');
bst.preOrderTraverse(function(node) {
  console.log(node.key);
});
```

## 代码分析

上面的测试代码，执行完所有的插入操作之后，二叉搜索树是这个样子的：

<img src="/images/2017/11/bst_result.jpg" />

也就是说，它并非尽可能是满二叉树。会因为输入数据的顺序原因，而导致某个或某些分支特别长。这样是非常影响搜索效率的。AVL 树、红黑树等都是为了尽可能实现满二叉树而提出的算法。
