---
title: 手写Vue
date: 2021-04-07 20:31:46
tags:
---

##### _项目地址见本文结尾_

### MVVM 框架的三要素：数据响应式、模板引擎及其渲染

1. 数据响应式：监听数据变化并在视图中更新

- Object.defineProperty()
- Proxy

2. 模版引擎：提供描述视图的模版语法

- 插值：`{{}}`
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
    return
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

### 开始实现 Vue

先写测试代码，新建一个`vue.html`，引入自己写的`vue.js`

```html
<meta http-equiv="Content-Type" content="text/html;charset=UTF-8" />
<div id="app">
  <p>{{counter}}</p>
</div>
<script src="./vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      counter: 1,
    },
  })
  setInterval(() => {
    app.counter++
  }, 1000)
</script>
```

#### 原理分析

1. `new Vue()` ⾸先执⾏初始化，对 data 执⾏响应化处理，这个过程发⽣在 Observer 中
2. 同时对模板执⾏编译，找到其中动态绑定的数据，从 data 中获取并初始化视图，这个过程发⽣在
   Compile 中
3. 同时定义⼀个更新函数和 Watcher，将来对应数据变化时 Watcher 会调⽤更新函数
4. 由于 data 的某个 key 在⼀个视图中可能出现多次，所以每个 key 都需要⼀个管家 Dep 来管理多个
   Watcher
5. 将来 data 中数据⼀旦发⽣变化，会⾸先找到对应的 Dep，通知所有 Watcher 执⾏更新函数

#### 实现

先将之前的`defineReactive`和`observe`搬过来

```js
// 数据响应式
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
        observe(newVal)
        console.log(`set ${key}:${newVal}`)
        // 函数内部有一个函数，并把值暴露出去，形成了闭包
        val = newVal
      }
    },
  })
}

// 递归遍历obj，动态拦截obj的所有key
function observe(obj) {
  if (typeof obj !== 'object' || obj == null) {
    return
  }
  Object.keys(obj).forEach((key) => {
    defineReactive(obj, key, obj[key])
  })
}

class Vue {
  constructor(options) {
    this.$options = options
    this.$data = options.data

    // 对data做响应式处理
    observe(this.$data)
  }
}
```

定义一个`Observer`来根据传入对象的类型做不同的相应处理

将 observe 中的 defineReactive 移到 walk 中

```js
function observe(obj) {
  if (typeof obj !== 'object' || obj == null) {
    return
  }
  // 创建Observer实例
  new Observer(obj)
}

// 响应式对象中的某个key只要是一个对象就要创建一个Observer实例
class Observer {
  // 核心功能：根据传入对象的类型做不同的相应处理
  constructor(obj) {
    if (Array.isArray(obj)) {
      // TODO: 数组处理
    } else {
      // 对象响应式
      this.walk(obj)
    }
  }

  walk(obj) {
    Object.keys(obj).forEach((key) => {
      defineReactive(obj, key, obj[key])
    })
  }
}
```

这里在调用 observe 时，会递归遍历。在平时 Vue 中看到的`__ob__`就是这个 Observer

现在还缺一层代理，在平时 Vue 中，可以通过`app.counter`的方式访问到属性，目前还只能通过`app.$data.counter`来访问

代理

```js
function proxy(vm) {
  Object.keys(vm.$data).forEach((key) => {
    Object.defineProperty(vm, key, {
      get() {
        return vm.$data[key]
      },
      set(newVal) {
        vm.$data[key] = newVal
      },
    })
  })
}
class Vue {
  constructor(options) {
    this.$options = options
    this.$data = options.data

    // 对data做响应式处理
    observe(this.$data)
    // 代理
    proxy(this)
  }
}
```

现在就可以通过`app.counter`直接访问到了

#### 实现编译

需要在遍历子元素的时候分别编译节点和文本

编译节点时，遍历其属性，对`v-`开头的分别处理，对`@`开头的绑定时间

编译文本时，判断是否有`{{}}`

```js
class Compile {
  constructor(el, vm) {
    this.$vm = vm
    this.$el = document.querySelector(el)

    // 遍历宿主元素
    if (this.$el) {
      this.compile(this.$el)
    }
  }

  compile(el) {
    // 递归遍历根元素
    el.childNodes.forEach((node) => {
      if (this.isElement(node)) {
        console.log('编译元素', node.nodeName)
      } else if (this.isInterpolation(node)) {
        console.log('编译插值文本', node.textContent)
      }
      // 递归
      if (node.childNodes.length > 0) {
        this.compile(node)
      }
    })
  }
  // 元素判断
  isElement(node) {
    return node.nodeType === 1
  }
  // 插值判断
  isInterpolation(node) {
    // 正则通过()分组，把{{}}中的内容放入RegExp中的$1
    return node.nodeType === 3 && /\{\{(.*)\}\}/.test(node.textContent)
  }
}

class Vue {
  constructor(options) {
    this.$options = options
    this.$data = options.data

    // 对data做响应式处理
    observe(this.$data)
    // 代理
    proxy(this)

    new Compile(options.el, this)
  }
}
```

下面开始解析插值文本

```js
class Compile {
  ...
  compile(el) {
    el.childNodes.forEach((node) => {
      if (this.isElement(node)) {
        console.log('编译元素', node.nodeName)
      } else if (this.isInterpolation(node)) {
        console.log('编译插值文本', node.textContent)
        this.compileText(node)
      }
      ...
    })
  }
  ...
 // 解析插值文本
  compileText(node) {
    node.textContent = this.$vm[RegExp.$1]
  }
}
```

现在已经可以在页面上看到编译出来的`counter`了，不过还不会更新，因为只编译了一次，先不处理，先把编译属性处理一下

```js
class Compile {
  ...
  compile(el) {
    // 递归遍历根元素
    el.childNodes.forEach((node) => {
      if (this.isElement(node)) {
        console.log('编译元素', node.nodeName)
        this.compileElement(node)
      } else if (this.isInterpolation(node)) {
        console.log('编译插值文本', node.textContent)
        this.compileText(node)
      }
      ...
    })
  }
  ...
  // 编译元素
  compileElement(node) {
    // 遍历所有属性：检查是否存在指令和事件
    const attrs = node.attributes
    Array.from(attrs).forEach(attr => {
      console.log(attr)
      // 例如：v-text="counter"
      // attrName就是v-text
      // expression就是counter
      const attrName = attr.name
      const expression = attr.value

      // 只处理动态值
      // 指令 v-
      if (this.isDirective(attrName)) {
        // 希望执行一个指令处理函数
        const directive = attrName.substring(2)
        // 如果存在这个指令对应的函数就执行，比如v-text就执行text()
        this[directive] && this[directive](node, expression)
      }
    })
  }
  // 判断指令
  isDirective(attrName) {
    return attrName.startsWith('v-')
  }
}
```

先处理一个 `v-text`，添加如下方法

```js
// v-text
text(node, expression) {
  node.textContent = this.$vm[expression]
}
```

在页面中添加`<p v-text="counter"></p>`，已经可以正常显示了

再添加一个`v-html`

```js
// v-html
html(node, expression) {
  node.innerHTML = this.$vm[expression]
}
```

页面上添加

```html
<p v-html="desc"></p>
...
<script>
  const app = new Vue({
    el: '#app',
    data: {
      counter: 1,
      desc: 'foo<span style="color:red">bar</span>'
    }
  })
  ...
</script>
```

同样生效了

#### 依赖收集

给每个 key 创建一个 Dep，用来管理这个 key 的 Watcher

```js
const watchers = []
// 更新执行者Watcher
class Watcher {
  constructor(vm, key, updater) {
    this.vm = vm
    this.key = key
    this.updater = updater

    watchers.push(this)
  }

  update() {
    this.updater.call(this.vm, this.vm[this.key])
  }
}
```

先不做依赖收集，简单的把 watcher 放到 watchers 数组中

改造`Compile`

```js
class Compile {
  ...
  // v-text
  text(node, expression) {
    this.update(node, expression, 'text')
  }

  textUpdater(node, val) {
    node.textContent = val
  }

  update(node, expression, directive) {
    // 在解析指令时，不光要给它初始化，还要给它做更新函数的创建
    // 执行directive对应的实操函数
    const fn = this[directive + 'Updater']
    fn && fn(node, this.$vm[expression])

    // 创建Watcher
    new Watcher(this.$vm, expression, function (val) {
      // 形成闭包
      fn && fn(node, val)
    })
  }
}
```

改造`defineReactive`，每次 set 时把 watchers 遍历

```js
// 数据响应式
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
        observe(newVal)
        console.log(`set ${key}:${newVal}`)
        // 函数内部有一个函数，并把值暴露出去，形成了闭包
        val = newVal
        // update()
        watchers.forEach((w) => w.update())
      }
    },
  })
}
```

现在`v-text`已经可以更新了

改造插值文本

```js
// 解析插值文本
compileText(node) {
  this.update(node, RegExp.$1, 'text')
}
```

改造`v-html`

```js
// v-html
html(node, expression) {
  this.update(node, expression, 'html')
}

htmlUpdater(node, val) {
  node.innerHTML = val
}
```

上面是通过 watchers 是全量更新，现在创建 Dep，用来收集每个 key 的 watcher

```js
class Dep {
  constructor() {
    this.deps = []
  }

  addDep(dep) {
    this.deps.push(dep)
  }

  notify() {
    this.deps.forEach((w) => w.update())
  }
}
```

改造 defineReactive 和 Watcher

```js
// 数据响应式
function defineReactive(obj, key, val) {
  // 递归
  observe(val)

  // 创建一个对应的Dep实例
  const dep = new Dep() // 这里也是闭包，dep和key是一对一的对应关系

  Object.defineProperty(obj, key, {
    get() {
      console.log(`get ${key}:${val}`)

      // 依赖收集
      Dep.target && dep.addDep(Dep.target)

      return val
    },
    set(newVal) {
      if (newVal !== val) {
        observe(newVal)
        console.log(`set ${key}:${newVal}`)
        // 函数内部有一个函数，并把值暴露出去，形成了闭包
        val = newVal
        // update()
        // watchers.forEach((w) => w.update())
        dep.notify()
      }
    },
  })
}
...
class Watcher {
  constructor(vm, key, updater) {
    this.vm = vm
    this.key = key
    this.updater = updater

    // watchers.push(this)
    // 保存Watcher引用，放到静态变量里
    Dep.target = this
    // 放进去立刻读取，触发defineReactive中的get
    this.vm[this.key]
    Dep.target = null
  }

  update() {
    this.updater.call(this.vm, this.vm[this.key])
  }
}
```

完成

项目地址[https://github.com/YongMaple/my-vue](https://github.com/YongMaple/my-vue)
