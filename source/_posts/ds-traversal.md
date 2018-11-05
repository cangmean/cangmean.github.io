---
title: 二叉树的遍历
date: 2018-11-05 15:29:59
tags:
  - js
  - 深度优先
  - 广度优先
categories:
  - 数据结构
---

原理: 根据二叉树的子树也是一个二叉树来遍历整个二叉树的节点。
深度优先： 前序、中序、后序是相对与根节点的访问顺序来命名的。

- 前序遍历：先访问树的根节点， 在访问左右子树。DLR
- 中序遍历：(也称对称序)先访问左子树， 在访问根节点， 后访问右节点。LDR
- 后序遍历：先访问树的左右子树， 在访问根节点。LRD

广度优先：通过队列来缓存访问的节点， 如果访问的节点包含左右子树则依次入队保证层级关系。

<!--more-->

### 深度优先

首先新建一个二叉树的节点和工厂函数

```js
class _Node {
  constructor(data = null, left = null, right = null) {
    this.data = data
    this.left = left
    this.right = right
  }
}

// 工厂函数
const Node = (data, left = null, right = null) => {
  return new _Node(data, left, right)
}

// 创建一个二叉树
let tree = Node(
  'A',
  Node('B', Node('D')),
  Node('C', Node('E', (right = Node('G'))), Node('F', Node('H'), Node('I')))
)
```

深度优先根据对节点值的访问顺序分为前序，中序和后序。有根据实现方式有递归和分递归的区别

#### 前序遍历

递归实现

```js
function preorder(tree) {
  // 前序遍历
  if (tree) {
    console.log(tree.data)
    preorder(tree.left)
    preorder(tree.right)
  }
}
```

非递归实现, （使用栈来保存节点信息）

```js
function stack_preorder(tree) {
  // 非递归前序遍历
  let stack = []
  // 一直循环到，栈和树都为空
  while (tree || stack.length > 0) {
    while (tree) {
      // 访问节点的值
      console.log(tree.data)
      // 把节点压入栈
      stack.push(tree)
      // 循环遍历左子树
      tree = tree.left
    }
    // 退到这里表明，树的左子树遍历结束或为空
    // 弹出上层树, 并开始遍历右子树
    tree = stack.pop()
    tree = tree.right
  }
}
```

#### 中序遍历

递归实现

```js
function inorder(tree) {
  // 中序遍历
  if (tree) {
    inorder(tree.left)
    console.log(tree.data)
    inorder(tree.right)
  }
}
```

非递归实现

```js
function stack_inorder(tree) {
  // 非递归中序遍历
  let stack = []
  while (tree || stack.length > 0) {
    while (tree) {
      // 把节点压入栈
      // 循环遍历左子树
      stack.push(tree)
      tree = tree.left
    }
    // 退到这里表明，树的左子树遍历结束或为空
    // 打印当前节点并将右子树压入栈中
    tree = stack.pop()
    console.log(tree.data)
    tree = tree.right
  }
}
```

#### 后序遍历

递归实现

```js
function postorder(tree) {
  // 后序遍历
  if (tree) {
    postorder(tree.left)
    postorder(tree.right)
    console.log(tree.data)
  }
}
```

非递归实现

```js
function stack_postorder(tree) {
  // 非递归后序遍历
  let stack = []
  while (tree || stack.length > 0) {
    while (tree) {
      stack.push(tree)
      if (tree.left) {
        tree = tree.left
      } else {
        tree = tree.right
      }
    }
    // 当子树到底，弹出节点并打印
    tree = stack.pop()
    console.log(tree.data)
    //判断当前节点是否是上层节点的左子树, 如果是就遍历右子树
    if (stack.length > 0 && stack[stack.length - 1].left === tree) {
      tree = stack[stack.length - 1].right
    } else {
      // 说明左右子树遍历完或者没有右子树
      // 设置树为空并通过循环弹出树的根节点
      tree = null
    }
  }
}
```

### 广度优先

通过队列来存储节点信息

```js
function order(tree) {
  // 广度优先
  let que = [tree]
  // 初始化队列，并把跟节点放入队列
  // 循环到队列为空
  while (que.length > 0) {
    // 节点出队列
    let node = que.shift()
    console.log(node.data)
    if (node.left) {
      que.push(node.left)
    }
    if (node.right) {
      que.push(node.right)
    }
  }
}
```
