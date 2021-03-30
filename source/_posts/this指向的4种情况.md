---
title: this指向的4种情况
date: 2021-01-05 15:07:22
tags:
  - this
---

#### 1. 在对象属性中的 this

对象的属性是函数，那么函数中的 this 指向对象本身

<!--more-->

```js
var obj = {
  x: 123,
  fn: function () {
    console.log(this) // {x: 123, fn: ƒ}
    console.log(this.x) // 123
  },
}
obj.fn()
```

对象的属性是函数，函数内部还有函数，那么二级（及以上）函数的 this 都指向 window

```js
var obj = {
  x: 456,
  fn: function () {
    console.log('fn', this) // {x: 456, fn: ƒ}
    var f1 = function () {
      console.log('fn.f1', this) // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
      console.log(this.x) // undefined
      var f2 = function () {
        console.log('fn.f2', this) // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
      }
      f2()
    }
    f1()
  },
}
obj.fn()
```

#### 2. 构造函数中的 this

构造函数中的一级函数，this 指向通过构造函数 new 出来的实例（例子中的 person）

```js
var Person = function () {
  this.name = 'linlif'
  this.fn = function () {
    console.log('fn', this) // {name: "linlif", fn: ƒ}
  }
}
var person = new Person()
person.fn()
```

构造函数中的二级(及以上)函数，this 指向的是 window

```js
var Person = function () {
  this.name = 'linlif'
  this.fn = function () {
    console.log('fn', this) // {name: "linlif", fn: ƒ}
    var f2 = function () {
      console.log('f2', this) // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
      var f3 = function () {
        console.log('f3', this) // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
      }
      f3()
    }
    f2()
  }
}
var person = new Person()
person.fn()
```

#### 3. 全局上下文环境中 this

全局上下文环境，this 指向浏览器的 window 对象，例如：

```js
// 全局的this
console.log(this) // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
console.log(this === window) // true

// 全局的普通函数
var global = function () {
  console.log(this) // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
}
global()
```

#### 4. call()和 apply()对 this 指向的影响

使用 call()方法后，this 指向 call()方法的参数。使用 apply()的结果和 call()是一致的

```js
// 改变调用对象为gObj
var gObj = {
  name: 'gName',
}
var aaa = function () {
  console.log(this) // {name: "gName"}
  console.log(this.name) // gName
}
aaa.call(gObj)

// 改变调用对象为window
var name = 'global'
var bbb = function () {
  console.log(this) // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
  console.log(this.name) // global
}
bbb.call(this)
```
