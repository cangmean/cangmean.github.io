---
title: 表达式树
date: 2018-11-01 17:04:03
tags:
  - js
  - 表达式树
categories:
  - 数据结构
---

将树的节点和叶子节点分别当做操作符和操作数，用来描述表达式的方式叫表达式树。比如: `(1 + 2) \* (5 - 3)` 的表达式树

![表达式树](https://mink.oss-cn-beijing.aliyuncs.com/blog/exp_tree.jpg)

下面是表达式树的代码， 首先通过栈来存储操作符和操作数

```js
class Stack {
  constructor() {
    this.list = []
  }

  getSize() {
    return this.list.length
  }

  isEmpty() {
    return this.getSize() === 0
  }

  push(e) {
    this.list.push(e)
  }

  pop() {
    return this.list.pop()
  }

  top() {
    if (this.getSize() === 0) {
      return null
    }
    return this.list[this.getSize() - 1]
  }
}
```

<!--more-->

并设置符号的优先级，跟栈的括号匹配是同等原理

```js
// 优先级
const priority = {
  '(': 1,
  '+': 2,
  '-': 2,
  '*': 3,
  '/': 3
}
```

以下是设置节点的部分， 节点可以计算该节点的值

```js
class Node {
  constructor(data = null, left = null, right = null) {
    this.data = data
    this.left = left
    this.right = right
    this.value = this.get_value()
  }

  get_value() {
    // 获取该节点的值
    let left_value = this.left
    let right_value = this.right

    if (this.left instanceof Node) {
      left_value = this.left.value
    }
    if (this.right instanceof Node) {
      right_value = this.right.value
    }

    if (left_value && right_value) {
      return this.eval(left_value, right_value)
    } else if (left_value) {
      return left_value
    } else if (right_value) {
      return right_value
    } else {
      return null
    }
  }

  eval(left, right) {
    // 计算节点值
    switch (this.data) {
      case '+':
        return left + right
      case '-':
        return left - right
      case '*':
        return left * right
      case '/':
        return left / right
      default:
        throw 'Error Operator: ' + this.data
    }
  }
}
```

对字符串进行解析，并构建表达式树。这里通过设置栈来临时存储`操作符`和`操作数`

```js
class ExpTree {
  constructor(exp) {
    this.exp = exp
    this.op_stack = new Stack()
    this.data_stack = new Stack()
    this.op = '+-*/()'
    this.root = null
  }

  /**
   * 解析表达式
   * @param {String} exp 表达式
   */
  parse_exp() {
    for (let i of this.exp) {
      if (i.trim() === '') {
        continue
      } else {
        if (!this.op.includes(i)) {
          // 数值压入数据栈中
          this.data_stack.push(parseInt(i))
        } else if (this.op_stack.top() === null || i === '(') {
          // 栈顶为空或者是开括号
          this.op_stack.push(i)
        } else if (i === ')') {
          // 符号为闭括号, 循环弹出符号并添加到节点
          let top = this.op_stack.pop()
          while (top != '(') {
            this.setNode(top)
            top = this.op_stack.pop()
          }
        } else if (priority[this.op_stack.top()] >= priority[i]) {
          // 判断优先级，添加到节点
          let top = this.op_stack.pop()
          this.setNode(top)
          this.op_stack.push(i)
        } else {
          this.op_stack.push(i)
        }
      }
    }

    // 生成表达式树
    let top = this.op_stack.pop()
    while (top) {
      this.setNode(top)
      top = this.op_stack.pop()
    }
    this.root = this.data_stack.pop()
  }

  setNode(op) {
    // 表达式转换成节点
    let right = this.data_stack.pop()
    let left = this.data_stack.pop()
    let node = new Node(op, left, right)
    this.data_stack.push(node)
  }
}

let et = new ExpTree('(1 + 2) * (5 - 3)')
et.parse_exp()
console.log(et.root)
```
