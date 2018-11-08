---
title: 哈夫曼树
date: 2018-11-08 17:39:18
tags:
  - js
categories:
  - 数据结构
---

原理: 通过实数集S = { s1, s2, s3 ... s} 来扩充二叉树, 其带权外部路径长度（WPL）达到最小的树称为哈夫曼树。
例子: {2, 3, 4, 11}  的WPL 可能为(34, 54, 40) 那么WPL为34的为树哈夫曼树。
实现: 通过优先队列，弹出最小的两个树，并构成新的子树，新的子树的根节点为两个树跟节点的和，比如 (2, 3) 新的节点为 5, 并把树压入队列中一直循环，直到队列中只存在一颗树。


使用`valueOf`重载运算符
```js
class _Node {
  constructor(data = null, left = null, right = null) {
    this.data = data
    this.left = left
    this.right = right
  }

  valueOf() {
    return this.data
  }
}

// 工厂函数
const Node = (data, left = null, right = null) => {
  return new _Node(data, left, right)
}
```
<!--more-->

哈夫曼算法是通过获取两个权值最小的节点来构建新的节点直到生成一个二叉树，这里使用小顶堆来实现队列

```js
class Huffman {
  constructor(list) {
    this.tree = this.build(list)
  }

  build(list) {
    let nodes = list.map(item => Node(item))
    let que = new PriorityQue(nodes)
    while (que.getSize() > 1) {
      // 最小的节点出堆
      let t1 = que.dequeue()
      let t2 = que.dequeue()
      console.log('Get Node：', t1.data, t2.data)

      // 构成新的节点压入
      que.enqueue(Node(t1.data + t2.data, t1, t2))
    }
    return que.dequeue()
  }

}

let lst = [2, 3, 7, 4, 10, 2, 5]
let b = new Huffman(lst)

console.log(b.tree.data, b.tree.left.data, b.tree.right.data)
```
