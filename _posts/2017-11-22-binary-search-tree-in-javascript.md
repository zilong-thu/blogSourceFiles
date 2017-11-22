---
title: Binary-Search-Tree in JavaScript
date: 2017-11-22 01:20:13
tags: 算法
categories: 算法
---


这里用 JavaScript 实现一个二叉搜索树。

``` javascript
function BinarySearchTree() {
  var Node = function(key) {
    this.key = key;
    this.left = null;
    this.right = null;
  };

  var root = null;

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