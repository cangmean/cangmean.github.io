---
title: 栈的括号匹配
date: 2018-08-27 12:34:41
tags:
  - js
categories:
  - 数据结构
---

### 栈

栈是一种后进先出的队列(LIFO)， 是一种存储数据的容器.

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
;(function main() {
  let list = new Stack()
  list.push(3)
  list.push(5)
  list.push(1)
  console.log(`栈顶元素为: ${list.top()}`)
  list.pop()
  console.log(`栈顶元素为: ${list.top()}`)
})()
```

<!--more-->

### 栈的括号匹配

通过栈的后进先出， 来实现括号匹配， 如果遇到开号则压入栈中， 遇到闭号弹出栈顶元素匹配， 匹配成功则打印， 失败则报错

```js
const match = text => {
  let open_handle = ['(', '{', '[']
  let handle_pair = {
    ')': '(',
    '}': '{',
    ']': '['
  }
  let list = new Stack()
  for (let i = 0; i < text.length; i++) {
    let s = text[i]
    if (open_handle.indexOf(s) > -1) {
      list.push(s)
    } else if (Object.keys(handle_pair).indexOf(s) > -1) {
      if (list.pop() != handle_pair[s]) {
        console.log(`${s} 和 ${handle_pair[s]} 不匹配`)
      } else {
        console.log(`${s} 和 ${handle_pair[s]} 匹配`)
      }
    }
  }
}
;(function main() {
  let text = "nihao (lklkl I'm sdfsd{ haha })f"
  match(text)
})()
```

#### 中缀表达式转后缀表达式

原理：从左遍历中缀表达式, 遇到操作数打印，遇到操作符， 判断栈顶操作符优先级是否大于等于当前操作符
是 弹出栈顶， 当前操作符压入栈中
否 压入栈顶
遇到（ 压入栈中， 如果 ）出现， 则循环弹出栈顶操作符直到与 ( 匹配。
计算后缀表达式：从左到右遍历后缀表达式，遇到操作数放进栈，遇到操作符，栈顶两个数出栈进行运算，运算结果放进栈，直到读完后缀表达式。
这里还需要处理表达式中双位数， 比如: 17 。 因为遍历字符会把它当做 1, 7。

```js
const priority = {
  '(': 1,
  '+': 2,
  '-': 2,
  '*': 3,
  '/': 3
}

function match(text) {
  let op = '+-*/()'
  let stack = new Stack()

  //循环字符串
  for (let i = 0; i < text.length; i++) {
    let s = text[i]

    if (!op.includes(s)) {
      // 操作数， 打印出来
      console.log(s)
    } else if (stack.top() === null || s === '(') {
      // 操作符， 入栈
      stack.push(s)
    } else if (s === ')') {
      let top = stack.top()
      // 一直循环到开阔号
      while (top != '(') {
        console.log(top)
        top = stack.pop()
      }
    } else if (priority[stack.top()] >= priority[s]) {
      // 如果栈顶元素优先级大于当前操作符， 栈顶元素出栈， 操作符入栈
      console.log(stack.pop())
      stack.push(s)
    } else {
      stack.push(s)
    }
  }
}

;(function main() {
  let text = '(3 - 5) * (6 + 17 * 4) / 3'
  match(text)
})()
```
