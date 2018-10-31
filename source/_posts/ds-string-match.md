---
title: 字符串匹配
date: 2018-08-27 12:29:09
tags:
  - js
categories:
  - 数据结构
---

### 朴素匹配算法

朴素匹配算法是对目标字符串和模板字符串的一一匹配。如果匹配得上,下标向右移一位, 否则清空并重新开始匹配。

```js
const match = (target, pattern) => {
  let [i, j] = [0, 0]
  let [n, m] = [target.length, pattern.length]
  while (i < n && j < m) {
    if (target[i] == pattern[j]) {
      console.log(
        `匹配的元素: target[${i}]=${target[i]}, pattern[${j}]=${pattern[j]}`
      )
      i += 1
      j += 1
    } else {
      console.log(
        `不匹配的元素: target[${i}]=${target[i]}, pattern[${j}]=${pattern[j]}`
      )
      i = i - j + 1
      j = 0
    }
  }
  if (j == m) {
    return i - j
  }
  return -1
}
;(function main() {
  let target = 'abb abad abac'
  let pattern = 'abac'
  console.log('target', target)
  console.log('pattern', pattern)
  match(target, pattern)
})()
```

<!--more-->

### KMP 匹配算法

kmp 是通过已知匹配的字符进行移位的算法，位移数是通过已匹配的字符串的前缀和后缀元素集合中最长子元素长度。[详解](http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html)

```js
/**
 * 获取模板字符串所有前缀子串
 * @param {String} pattern 模板字符串
 */
const getPrefix = pattern => {
  let list = []
  for (let i = 0; i < pattern.length - 1; i++) {
    let s = pattern.slice(0, i + 1)
    if (s.length > 0) {
      list.push(s)
    }
  }
  return list
}

/**
 * 获取模板字符串所有后缀子串
 * @param {String} pattern 模板字符串
 */
const getSuffix = pattern => {
  let list = []
  for (let i = 0; i < pattern.length - 1; i++) {
    let s = pattern.slice(i + 1, -1)
    if (s.length > 0) {
      list.push(s)
    }
  }
  return list
}

/**
 * 获取偏移量
 * @param {String} pattern
 */
const patternNext = pattern => {
  let prefix = getPrefix(pattern)
  let suffix = getSuffix(pattern)
  let lst = prefix.filter(function(v) {
    return suffix.indexOf(v) > -1
  })
  let offset = -1

  for (let i = 0; i < lst.length; i++) {
    if (offset < lst[i].length) {
      offset = lst[i].length
    }
  }
  return offset
}

/**
 * kmp 匹配算法
 * @param {String} target
 * @param {String} pattern
 */
const match = (target, pattern) => {
  let [i, j] = [0, 0]
  let [n, m] = [target.length, pattern.length]
  while (i < n && j < m) {
    if (target[i] === pattern[j]) {
      console.log(
        `匹配元素: target[${i}]=${target[i]}, pattern[${j}]=${pattern[j]}`
      )
      i += 1
      j += 1
    } else {
      let offset = j - patternNext(pattern.slice(0, j))
      console.log(
        `不匹配元素: target[${i}]=${target[i]}, pattern[${j}]=${pattern[j]}`
      )
      console.log(`位移数: ${offset}`)
      i = i + offset
      j = 0
    }
  }
  if (j == m) {
    return i - j
  }
  return -1
}
;(function main() {
  let target = 'BBC ABCDAB ABCDABCDABDE'
  let pattern = 'ABCDABD'
  match(target, pattern)
})()
```
