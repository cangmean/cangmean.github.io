---
title: javascript 事件机制
date: 2018-08-27 15:03:09
tags:
  - 事件
categories:
  - 前端
  - js
---

当我们对一个元素和他的子元素各绑定事件时候，点击元素会触发两个事件

```html
<div class="box1">
  <div class="box2"></div>
</box>
```

```js
box1.addEventListener(
  'click',
  function(e) {
    console.log('click box1')
  },
  false
)

box2.addEventListener(
  'click',
  function(e) {
    console.log('click box2')
  },
  flase
)
```

可以看到先触发 box2 在触发 box1, 如果想要阻止 box1 事件，添加`stopPropagation`阻止事件传递

```js
box2.addEventListener(
  'click',
  function(e) {
    e.stopPropagation()
    console.log('click box2')
  },
  flase
)
```

这样就只执行了 box2 事件，相反也可以阻止 box2 事件只执行 box1

<!--more-->

### 事件冒泡和事件捕获

当一个绑定事件的元素发生点击事件时会经过捕获、处理阶段、冒泡等过程。可以在绑定事件的时候设置

![事件机制](https://mink.oss-cn-beijing.aliyuncs.com/blog/js-event.png)

**捕获**
`addEventListener` 第三个参数为 true 时，表示这个事件会在捕获阶段触发

```js
Ele.addEventListener('click', function(e) {}, true)
```

**冒泡**
相反 `addEventListener` 第三个参数设置为 false 为冒泡事件

```js
Ele.addEventListener('click', function(e) {}, false)
```

两个事件都可以通过`stopPropagation`阻止传递下去

### 事件委托

管理站点文章后面都会有编辑等操作，这些操作大多都绑定着事件

```js
for (var i=0; i<list.length; i++) {
  list[i].addEventListener('click', function(e) {
    ...
  }, false)
}
```

像这样我们为每个元素都绑定了事件，当数量大时会影响性能，正确的做法是通过事件委托

```js
Ele.addEventListener('click', function(e) {
  if (e.target && e.target.className.toUpperCase() == 'BOX2') {
    ...
  }
}, false)
```

把事件绑定到父节点后通过冒泡机制判断子元素点击事件，这样不用绑定多个，只需要绑定父节点就可以了
