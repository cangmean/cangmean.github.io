---
title: javascript 函数防抖和函数节流
date: 2018-09-05 16:57:45
categories:
  - 前端
  - js
---

昨天在逛掘金的时候无意间看到 Js 防抖和节流的主题， 什么东西听都没听过，看了以后才明白是特别实用的东西，所以记录一下。

> 作者：薄荷前端
> 链接：[7 分钟理解 JS 的节流、防抖及使用场景](https://juejin.im/post/5b8de829f265da43623c4261)
> 来源：掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
> Git 地址 https://github.com/BooheeFE/weekly/issues/13

### 函数防抖

> 在事件被触发 n 秒后再执行回调，如果在这 n 秒内又被触发，则重新计时。

```js
function ajax(content) {
  console.log('ajax request ' + content)
}

var inputa = document.getElementById('unDebounce')

inputa.addEventListener('keyup', function(e) {
  ajax(e.target.value)
})
```

这个例子中每次键盘输入都会触发一次 ajax 请求， 用户查询内容的时候会发送多次不必要的查询。可以通过防抖来过滤不必要的请求

<!--more-->

```js
function ajax(content) {
  console.log('ajax request ' + content)
}

function debounce(fn, delay) {
  return function(args) {
    var self = this
    clearTimeout(fn.id)
    fn.id = setTimeout(function() {
      fn.call(self, args)
    }, delay)
  }
}

var inputb = document.getElementById('debounce')
var req = debounce(ajax, 500)

inputb.addEventListener('keyup', function(e) {
  req(e.target.value)
})
```

这个例子中，我们通过给函数 ajax 设置 id 来判断当前输入是否执行， 如果间隔时间内有新的输入，清除原有的任务，开启新的任务。

### 函数节流

> 规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。

函数节流与函数防抖类似，都是通过`清除任务`和`创建任务`来实现的。

```js
function throttle(fn, delay) {
  var last, deferTimer
  return function(args) {
    var self = this
    var now = +new Date()
    if (last && now < last + delay) {
      clearTimeout(deferTimer)
      deferTimer = setTimeout(function() {
        last = now
        fn.call(self, args)
      }, delay)
    } else {
      last = now
      fn.call(self, args)
    }
  }
}

var throttleAjax = throttle(ajax, 1000)

var inputc = document.getElementById('throttle')
inputc.addEventListener('keyup', function(e) {
  throttleAjax(e.target.value)
})
```

通过比较每次触发 keyup 事件的时间，判断是否执行 ajax 请求。
