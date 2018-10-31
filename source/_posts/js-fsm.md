---
title: javascript 有限状态机
date: 2018-09-06 18:13:50
tags:
  - 状态机
categories:
  - 前端
  - js
---

有限状态机是一种模型，模拟事物状态的变化，有以下特点

- 可以用状态来描述事物，并任意时刻事物总是处于一种状态
- 事物拥有的状态是有限个的
- 某种条件下，状态可以发生改变，变为另一种状态

以下是阮一峰老师在博客中写的例子[JavaScript 与有限状态机](http://www.ruanyifeng.com/blog/2013/09/finite-state_machine_for_javascript.html)

```js
var menu = {
  // 当前状态
  currentState: 'hide', // 绑定事件
  initialize: function() {
    var self = this
    self.on('hover', self.transition)
  }, // 状态转换
  transition: function(event) {
    switch (this.currentState) {
      case 'hide':
        this.currentState = 'show'
        doSomething()
        break
      case 'show':
        this.currentState = 'hide'
        doSomething()
        break
      default:
        console.log('Invalid State!')
        break
    }
  }
}
```

这个例子展示的是 menu 触发 hover 时改变 menu 的显示状态。

<!--more-->

### 例子

[javascript-state-machine](https://github.com/jakesgordon/javascript-state-machine)是 github 上 star 数量到达 5k 的状态机库， 创建状态机方式如下

```js
var fsm = StateMachine.create({
  initial: 'green',
  events: [
    { name: 'warn', from: 'green', to: 'yellow' },
    { name: 'stop', from: 'yellow', to: 'red' },
    { name: 'ready', from: 'red', to: 'yellow' },
    { name: 'go', from: 'yellow', to: 'green' }
  ]
})
```

这样我们就创建了一个信号的状态机。我们可以模拟上边的例子，创建自己的状态机

```js
class StateMachine {
  constructor(options) {
    this.init = options.init
    this.events = options.events
    this._init()
  }

  /**
   * 初始化
   */
  _init() {
    this.events.forEach(event => {
      this.eventHandler(event)
    })
  }

  /**
   * 处理状态变化
   * @param {State} from 当前状态
   * @param {State} to 改变后的状态
   */
  stateHandler(from, to) {
    return function() {
      if (from === this.init) {
        console.log(from, '---->', to)
        this.init = to
      } else {
        console.log('Invalid transition')
      }
    }
  }

  /**
   * 绑定事件
   */
  eventHandler(event) {
    this[event['name']] = this.stateHandler(event['from'], event['to'])
  }
}

var fsm = new StateMachine({
  init: 'green',
  events: [
    { name: 'warn', from: 'green', to: 'yellow' },
    { name: 'stop', from: 'yellow', to: 'red' },
    { name: 'ready', from: 'red', to: 'yellow' },
    { name: 'go', from: 'yellow', to: 'green' }
  ]
})

fsm.warn()
fsm.stop()
fsm.ready()
fsm.go()
```

这样我们每触发一个事件，信号的状态就会发生改变。且信号状态是根据事件定义的。

### 参考

- http://www.ruanyifeng.com/blog/2013/09/finite-state_machine_for_javascript.html
