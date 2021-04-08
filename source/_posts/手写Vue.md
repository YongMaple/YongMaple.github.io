---
title: 手写Vue
date: 2021-04-07 20:31:46
tags:
---

### MVVM 框架的三要素：数据响应式、模板引擎及其渲染

1. 数据响应式：监听数据变化并在视图中更新

- Object.defineProperty()
- Proxy

2. 模版引擎：提供描述视图的模版语法

- 插值：{{}}
- 指令：v-bind，v-on，v-model，v-for，v-if

3. 渲染：如何将模板转换为 html

- 模板 => vdom => dom

### 数据响应式原理

数据变更能够响应在视图中，就是数据响应式。vue2 中利⽤ Object.defineProperty() 实现变更检
测。

### 实现一个简单的 reactive.js

_Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。_

语法

_Object.defineProperty(obj, prop, descriptor)_

参数

- obj
  要定义属性的对象。
- prop
  要定义或修改的属性的名称或 Symbol 。
- descriptor
  要定义或修改的属性描述符。

```js
// 数据响应式
function defineReactive(obj, key, val) {
  Object.defineProperty(obj, key, {
    get() {
      console.log(`get ${key}:${val}`)
      return val
    },
    set(newVal) {
      if (newVal !== val) {
        console.log(`set ${key}:${newVal}`)
        // 函数内部有一个函数，并把值暴露出去，形成了闭包
        val = newVal
      }
    },
  })
}
```

测试逻辑

```js
const obj = {}
defineReactive(obj, 'foo', 'foo')
obj.foo
obj.foo = 'foooooooooooo'
obj.foo
```

```bash
node reactive.js
get foo:foo
set foo:foooooooooooo
get foo:foooooooooooo
```

放到页面中，简单写一个 update 模拟一下

```html
<div id="app">{{foo}}</div>
<script>
  function defineReactive(obj, key, val) {
    Object.defineProperty(obj, key, {
      get() {
        console.log(`get ${key}:${val}`)
        return val
      },
      set(newVal) {
        if (newVal !== val) {
          console.log(`set ${key}:${newVal}`)
          // 函数内部有一个函数，并把值暴露出去，形成了闭包
          val = newVal
          update()
        }
      },
    })
  }

  function update() {
    // app:简写 相当于document.getElementById('app')
    app.innerText = obj.foo
  }

  const obj = {}
  defineReactive(obj, 'foo', 'foo')

  setInterval(() => {
    obj.foo = new Date().toLocaleTimeString()
  }, 1000)
</script>
```

现在已经可以在页面中看到时间在变化了，但目前只能响应一个属性

#### 遍历需要响应的对象

回到 reactive.js

```js
// 遍历obj，动态拦截obj的所有key
function observe(obj) {
  if (typeof obj !== 'object' || obj == null) {
    return obj
  }
  Object.keys(obj).forEach((key) => {
    defineReactive(obj, key, obj[key])
  })
}
```

目前只是遍历了浅层的属性，还需要实现递归

```js
function defineReactive(obj, key, val) {
  // 递归
  observe(val)
  Object.defineProperty(obj, key, {
    get() {
      console.log(`get ${key}:${val}`)
      return val
    },
    set(newVal) {
      if (newVal !== val) {
        console.log(`set ${key}:${newVal}`)
        // 函数内部有一个函数，并把值暴露出去，形成了闭包
        val = newVal
      }
    },
  })
}
```

在 observe 之后进行赋值的对象还不能实现响应式，需要重新递归遍历

```js
function defineReactive(obj, key, val) {
  // 递归
  observe(val)
  Object.defineProperty(obj, key, {
    get() {
      console.log(`get ${key}:${val}`)
      return val
    },
    set(newVal) {
      if (newVal !== val) {
        // 对于后赋值的对象重新递归遍历
        observe(newVal)
        console.log(`set ${key}:${newVal}`)
        // 函数内部有一个函数，并把值暴露出去，形成了闭包
        val = newVal
      }
    },
  })
}
```

目前动态设置的属性还不能生效，例如：

```js
obj.dong = 'dong'
```

此时是不生效的，模仿 Vue 进行一个封装

```js
function set(obj, key, val) {
  defineReactive(obj, key, val)
}
```

需要设置一个新的属性时，只能使用 set 方法

```js
set(obj, 'dong', 'dong')
```

用户直接使用 defineReactive，这里除了 set 写起来简单之外，set 还需要处理其他逻辑，比如数组的处理。这里暂不处理

未完待续
