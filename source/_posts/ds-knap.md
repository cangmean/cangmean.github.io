---
title: 简单背包递归算法
date: 2018-09-20 15:55:30
tags:
  - js
categories:
  - 数据结构
---

### 问题

有 n 个物品，重量分别为 W(i), 现有一包裹负重 W， 是否能从 n 个物品中取若干个物品刚好满足背包负重。

### 原理

当不选 W(n) 的物品时 knap(w, n-1) 是 knap(w, n)的解，当选 W(n) 的物品时 knap(w - W(n), n-1) 是 knap(w, n)的解。这就有了递归的性质，判断两种情况。

### 递归算法

```js
// 每个物品的重量
const list = [5, 10, 3, 7]

/**
 *
 * @param {Int} w 背包的总重量
 * @param {Int} n 每个物品
 */
function knap(w, n) {
  if (w == 0) {
    return true
  } else if (w > 0 && n < 1) {
    return false
  } else if (knap(w, n - 1)) {
    return true
  } else if (knap(w - list[n - 1], n - 1)) {
    console.log(`item： 第${n}个包裹，重量为：${list[n - 1]}`)
    return true
  } else {
    return false
  }
}

;(function main() {
  knap(15, list.length)
})()
```
