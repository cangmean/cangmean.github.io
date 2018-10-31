---
title: 链表
date: 2018-08-27 12:24:12
tags:
  - js
categories:
  - 数据结构
---

### 单链表

```js
class Node {
  constructor(data, next = null) {
    this.data = data
    this.next = next
  }
}

class List {
  constructor() {
    // 初始化
    this.head = null
    this.length = 0
  }

  isEmpty() {
    return this.length == 0
  }

  getSize() {
    return this.length
  }

  append(e) {
    /*
      从head开始以为循环到尾部, 追加新的节点
      如果创建pop函数也是一样
    */
    if (this.head === null) {
      this.head = new Node(e)
      this.length += 1
    } else {
      let head = this.head
      while (head.next !== null) {
        head = head.next
      }
      head.next = new Node(e)
      this.length += 1
    }
  }

  reverse() {
    /*
      反转链表
      移动head指针，指向下个节点， 并把原来节点指向上一个节点
    */
    let p = null
    let head = this.head
    while (head !== null) {
      // 把head赋值给q, 这里q和head都指向同一个Node
      let q = head
      // 然后head指向给了下一个节点
      head = q.next
      // q指向上一个节点
      q.next = p
      // 把当前节点指向为上一个节点
      p = q
    }
    this.head = p
  }

  print() {
    let idx = 0
    let head = this.head
    while (head !== null) {
      idx += 1
      console.log(`idx ${idx}, data: ${head.data}`)
      head = head.next
    }
  }
}

;(function main() {
  let list = new List()
  list.append(3)
  list.append(5)
  list.append(1)
  list.print()
  list.reverse()
  list.print()
})()
```

<!--more-->

### 双端链表

双端链表: 一个节点可以查到它的前驱和后继

```js
class Node {
  constructor(data, prev = null, next = null) {
    this.data = data
    this.prev = prev
    this.next = next
  }
}

class List {
  constructor() {
    this.head = null
    this.tail = null
    this.length = 0
  }

  isEmpty() {
    return this.length == 0
  }

  getSize() {
    return this.length
  }

  push(e) {
    /**
     *
     * 通过判断尾节点， 如果为空， head和tail指向一个新的节点
     * 如果不为空, 当前tail指向一个新的节点， 新的节点的prev指向当前节点, tail向后移位
     *
     */
    let node = new Node(e)
    let tail = this.tail
    if (tail !== null) {
      tail.next = node
      node.prev = tail
      this.tail = node
    } else {
      this.head = this.tail = node
    }
    this.length += 1
  }

  pop() {
    /**
     * 通过判断尾节点， 如果不为空， 并且有上一个节点， 那么当前tail节点的指向为空， 并把上一个节点设置为新的tail节点
     */
    let tail = this.tail
    if (tail !== null) {
      this.length -= 1
      let prev = tail.prev
      if (prev !== null) {
        prev.next = null
        tail.prev = null
        this.tail = prev
      } else {
        this.head = this.tail = null
      }
      return tail
    }

    return null
  }

  lpush(e) {
    let node = new Node(e)
    let head = this.head
    if (head !== null) {
      head.prev = node
      node.next = head
      this.head = node
    } else {
      this.head = this.tail = node
    }
    this.length += 1
  }

  lpop() {
    let head = this.head
    if (head !== null) {
      this.length -= 1
      let next = head.next
      if (next !== null) {
        head.next = null
        next.prev = null
        this.head = next
      } else {
        this.head = this.tail = null
      }
      return head
    }
    return null
  }

  print() {
    let idx = 0
    let head = this.head
    while (head !== null) {
      idx += 1
      console.log(`idx ${idx}, data: ${head.data}`)
      head = head.next
    }
  }
}

;(function main() {
  let list = new List()
  list.push(3)
  list.push(5)
  list.lpush(1)
  list.print()
  list.pop()
  list.print()
})()
```
