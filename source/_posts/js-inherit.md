---
title: javascript 继承
date: 2018-08-24 17:00:09
tags:
  - js
categories:
  - 前端
  - js
---

继承是面向对象中的重要部分，像 java、python 等语言都是用类来继承的，而 js 的继承是通过原型链来实现的。

### 原型链

通过将 prototype 指向对象的实例，来实现继承

```js
function SuperType() {
  this.property = true
}

SuperType.prototype.getSuperValue = function() {
  return this.property
}

function SubType() {
  this.subproperty = false
}

// 继承 SuperType
SubType.prototype = new SuperType()

SubType.prototype.getSubValue = function() {
  return this.subproperty
}

var instance = new SubType()
console.log(instance.getSuperValue()) // true
```

<!--more-->

#### 缺点

所有 SubType 的实例都会继承同一个引用类型的对象，因为对于所有的 SubType 实例来说继承都是同一个对象的 colors

```js
function SuperType() {
  this.colors = ["red", "blue", "green"];
}

function SubType {
}

SubType.prototype = new SuperType()
var s1 = new SubType()
s1.colors.push('black')
console.log(s1) // "red,blue,green,black"

var s2 = new SubType()
console.log(s2) // "red,blue,green,black"
```

### 借用构造函数

```js
function SuperType(name) {
  this.name = name
}

function SubType() {
  SuperType.call(this, 'mink')
  this.age = 29
}

var instance = new SubType()
console.log(instance.name, instance.age) // mink 29
```

#### 缺点

在超类原型中定义的方法，在子类中不可见

```js
function SuperType(name) {
  this.name = name
}

SuperType.prototype.sayName = function() {
  console.log(this.name)
}

function SubType() {
  SuperType.call(this, 'mink')
  this.age = 29
}

var instance = new SubType()
console.log(instance.name, instance.age) // mink 29
instance.sayName() // error
```

### 组合继承

将原型链和借用构造函数组合在一起的继承方式叫组合继承

```js
function SuperType(name) {
  this.name = name
  this.colors = ['red', 'blue', 'green']
}

SuperType.prototype.sayName = function() {
  console.log(this.name)
}

function SubType(name, age) {
  SuperType.call(this, name)
  this.age = age
}

SubType.prototype = new SuperType()

SubType.prototype.sayAge = function() {
  console.log(this.age)
}

var s1 = new SubType('mink', 29)
s1.colors.push('black')
s1.sayName()
s1.sayAge()

var s2 = new SubType('mink2', 27)
console.log(s2.colors)
s2.sayName()
s2.sayAge()
```

#### 缺点

虽然组合继承解决了以上两个继承的问题，但是会调用两次构造方法

### 原型式

基于原有对象创建一个新的对象

```js
function object(o) {
  function F() {}
  F.prototype = o
  return new F()
}
```

#### 缺点

共享对象的引用类型

```js
var person = {
  friends: ['a', 'b', 'c']
}

var p1 = object(person)
p1.friends.push('d')

console.log(p1.friends) // a, b, c, d
```

### 寄生式

用于创建一个封装继承的对象，新的对象继承原有对象的属性和方法

```js
function createAnother(original) {
  var clone = object(original)
  clone.sayHi = function() {
    console.log('hi')
  }
  return clone
}

var person = {
  name: 'mink',
  age: '27'
}

var another = createAnother(person)
another.sayHi()
```

#### 缺点

封装在函数内的方法无法重用

### 寄生组合式

超类中的属性通过构造的方式继承，而原型通过原型方式继承，解决了组合继承中超类构造两次的缺点

```js
function inheritPrototype(subtype, supertype) {
  var prototype = Object.create(supertype.prototype)
  prototype.constructor = subtype
  subtype.prototype = prototype
}

function SuperType(name) {
  this.name = name
  this.colors = ['red', 'blue', 'green']
}

SuperType.prototype.sayName = function() {
  console.log(this.name)
}

function SubType(name, age) {
  SuperType.call(this, name)
  this.age = age
}

inheritPrototype(SubType, SuperType)

SubType.prototype.sayAge = function() {
  console.log(this.age)
}
```
