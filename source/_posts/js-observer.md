---
title: javascript 观察者模式和发布订阅模式
date: 2018-08-31 12:32:41
tags:
  - 观察者模式
  - 发布订阅模式
categories:
  - 前端
  - js
---

通过例子理解观察者模式和发布订阅模式的区别

### 观察者模式

Subject 是一个主题，Observer 是一个观察者。观察者可以订阅主题，主题发生变化会通知观察者。这是一个典型的观察者模式

```js
class Subject {
  constructor() {
    // 订阅主题的观察者, subscriber
    this.subs = []
  }

  subscribe(sub) {
    // 订阅
    this.subs.push(sub)
  }

  unsubscribe(sub) {
    // 取消订阅
    const index = this.subs.indexOf(sub)
    if (index > -1) {
      this.subs.splice(index, 1)
    }
  }

  fire() {
    // 主题变化，通知订阅主题的观察者
    this.subs.forEach(sub => {
      sub.notify()
    })
  }
}

class Observer {
  constructor(data) {
    this.data = data
  }

  notify() {
    console.log(this.data)
  }
}

let subject = new Subject()
let ob1 = new Observer('hello')
let ob2 = new Observer('world')
subject.subscribe(ob1)
subject.subscribe(ob2)
subject.fire()
```

<!--more-->

DOM 事件中的观察者模式，这里回调函数就是一个观察者，订阅了 body 的 click 事件。所以当 body 触发 click 时，会触发回调。

```js
document.body.addEventListener(
  'click',
  function(e) {
    console.log('click body')
  },
  false
)
```

修改观察者模型，用 Dom 模拟上述例子

```js
class Dom {
  constructor() {
    // 订阅事件的观察者
    this.events = {}
  }

  /**
   * 添加事件的观察者
   * @param {String} event  订阅的事件
   * @param {Function} callback 回调函数(观察者)
   */
  addEventListener(event, callback) {
    if (!this.events[event]) {
      this.events[event] = []
    }
    this.events[event].push(callback)
  }

  removeEventListener(event, callback) {
    if (!this.events[event]) {
      return
    }
    const callbackList = this.events[event]
    const index = callbackList.indexOf(callback)
    if (index > -1) {
      callbackList.splice(index, 1)
    }
  }

  /**
   * 触发事件
   * @param {String} event
   */
  fireEvent(event) {
    if (!this.events[event]) {
      return
    }
    this.events[event].forEach(callback => {
      callback()
    })
  }
}

const handler = () => {
  console.log('fire click')
}
const dom = new Dom()

dom.addEventListener('click', handler)
dom.addEventListener('move', function() {
  console.log('fire click2')
})
dom.fireEvent('click')
```

### 发布订阅模式

与观察者模式相比，发布订阅模式中间多了一层处理机制，用于解耦发布者和订阅者之间的关联

```js
class EventChannel {
  constructor() {
    // 主题
    this.subjects = {}
  }

  hasSubject(subject) {
    return this.subjects[subject] ? true : false
  }

  /**
   * 订阅的主题
   * @param {String} subject 主题
   * @param {Function} callback 订阅者
   */
  on(subject, callback) {
    if (!this.hasSubject(subject)) {
      this.subjects[subject] = []
    }
    this.subjects[subject].push(callback)
  }

  /**
   * 取消订阅
   */
  off(subject, callback) {
    if (!this.hasSubject(subject)) {
      return
    }
    const callbackList = this.subjects[subject]
    const index = callbackList.indexOf(callback)
    if (index > -1) {
      callbackList.splice(index, 1)
    }
  }

  /**
   * 发布主题
   * @param {String} subject 主题
   * @param {Argument} data 参数
   */
  emit(subject, ...data) {
    if (!this.hasSubject(subject)) {
      return
    }
    this.subjects[subject].forEach(callback => {
      callback(...data)
    })
  }
}

const channel = new EventChannel()

channel.on('update', function(data) {
  console.log(`update value: ${data}`)
})

channel.emit('update', 123)
```

这里通过 emit 向主题 update 发布一条消息， 在通过 on 来订阅 update 的主题来接受消息。

### 参考

- https://molunerfinn.com/observer-vs-pubsub-pattern/#订阅发布模式
- https://juejin.im/post/5a14e9edf265da4312808d86
