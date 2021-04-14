---
title: Vue2源码解析
date: 2021-04-13 17:09:57
tags:
  - Vue
  - 手撕源码
---

### 获取 Vue

项目地址：[https://github.com/vuejs/vue](https://github.com/vuejs/vue)

当前版本号：2.6.12

### 安装依赖

```bash
npm i
npm i roll-up -g
```

使用`npm run dev`就会在`dist`目录下生成编译后的`vue.js`

### 文件结构

```
.
├── BACKERS.md
├── LICENSE
├── README.md
├── benchmarks
├── dist          发布目录
├── examples      范例，里面有测试代码
├── flow          针对flow的类型声明（类似ts）
├── package.json
├── packages      核心代码之外的独立库
├── scripts       构建脚本
├── src           源码
│   ├── compiler          编译器相关（渲染函数、SFC的编译器、dom编译器）
│   ├── core              核心代码
│   │   ├── components      通用组件如keep-alive
│   │   ├── config.js
│   │   ├── global-api      全局API
│   │   ├── index.js
│   │   ├── instance        构造函数等
│   │   ├── observer        响应式相关
│   │   ├── util
│   │   └── vdom            虚拟DOM相关
│   ├── platforms           平台
│   │   ├── web
│   │   └── weex            基本凉凉了
│   ├── server
│   ├── sfc
│   └── shared
├── types         ts类型声明
└── yarn.lock
```

需要源码映射，所以在 package.json 中添加`--sourcemap`

`"dev": "rollup -w -c scripts/config.js --sourcemap --environment TARGET:web-full-dev",`

本文项目地址：[https://github.com/YongMaple/vue](https://github.com/YongMaple/vue) 内含测试用代码`/examples/test/`

术语解释：

- runtime: 仅包含运行时，不包含编译器
- common: cjs 规范，用于 webpack1
- esm: ES 模块，用于 webpack2+
- umd: universal module definition，兼容 cjs 和 amd，常见直接用在浏览器

例：

- `/dist/vue.common.js` 中间加 common 的，给服务端使用的，在 nodejs 环境执行的
- `/dist/vue.esm.browser.js` esm，给打包工具使用的，如 webpack。
- browser，给支持 type=module 这种模块化方式导入的浏览器
- `/dist/vue.js` global，同时兼容 amd 和 cjs(commonjs)的格式。umd 的打包方式
- `/dist/vue.runtime.js` runtime，运行时，不含编译器，template 中加入字符串模板这类操作会报错

### `src/platforms/web/entry-runtime-with-compiler.js`

从 package.json `dev`中找到`/scripts/config.js`

`rollup -w -c scripts/config.js --sourcemap --environment TARGET:web-full-dev`命令中传入了环境变量`web-full-dev`

在`/scripts/config.js`中查找，可以在配置中找到入口文件`web/entry-runtime-with-compiler.js`

文件地址前有个 resolve 方法

```js
const resolve = (p) => {
  // 拆分/
  // web/entry-runtime-with-compiler.js
  const base = p.split('/')[0] // web
  if (aliases[base]) {
    return path.resolve(aliases[base], p.slice(base.length + 1))
  } else {
    return path.resolve(__dirname, '../', p)
  }
}
```

以下是`aliases`的内容

```js
const path = require('path')

const resolve = (p) => path.resolve(__dirname, '../', p)

module.exports = {
  vue: resolve('src/platforms/web/entry-runtime-with-compiler'),
  compiler: resolve('src/compiler'),
  core: resolve('src/core'),
  shared: resolve('src/shared'),
  web: resolve('src/platforms/web'),
  weex: resolve('src/platforms/weex'),
  server: resolve('src/server'),
  sfc: resolve('src/sfc'),
}
```

现在找到了入口文件的位置`src/platforms/web/entry-runtime-with-compiler.js`

```js
const mount = Vue.prototype.$mount
Vue.prototype.$mount = function (
  ...
}
```

源码中这段是为了扩展$mount，为了解析 el、template 选项

问题 1：为什么可以不用写`$mount`

答：如果设置了 el，挂载的宿主就指定为 el

```js
const app = new Vue({
  el: '#app',
  // template: '<div>template</div>
  template: '#app',
  render(h) {
    return h('div', 'render')
  },
})
```

问题 2：el、template、render 同时出现，哪个优先级高

答：render > template > el

![注意看注释](./Vue2源码解析/1.png)

### `src/platforms/web/runtime/index.js`

找到当前文件中的 Vue 引入`import Vue from './runtime/index'`

进入这个文件看一下

```js
// install platform patch function
// 安装平台特有的补丁函数，未来用于组件更新
Vue.prototype.__patch__ = inBrowser ? patch : noop

// public mount method
// 实现了$mount
Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && inBrowser ? query(el) : undefined
  // 挂载组件：把当前组件(this)挂载到el上
  // 流程： mountComponent 执行this的render， 得到vnode， _update(vnode)转换成真实dom， _update内部调用的是__patch__
  return mountComponent(this, el, hydrating)
}
```

_Vue 在执行挂载时：执行渲染函数得到虚拟 dom，再执行更新函数，将其转换为真实 dom_

继续找 Vue，当前文件的引入为`import Vue from 'core/index'`

此处 core 是`src/core`的别名

### `src/core/index.js`

主要做了初始化全局 API

```js
// 初始化全局API
// Vue.use directive components mixin 等
initGlobalAPI(Vue)
```

继续找 Vue，当前文件的引入为`import Vue from './instance/index'`

### `src/core/instance/index.js`

这个文件中

- 声明了 Vue 的构造函数
- 声明了 Vue 的各种实例方法($set、$watch 等等)

```js
// 声明Vue的构造函数
function Vue(options) {
  if (process.env.NODE_ENV !== 'production' && !(this instanceof Vue)) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}

// 声明Vue各种实例方法
initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)

export default Vue
```

这里`_init`从何而来，是在`initMixin()`中

### `src/core/instance/init.js`

![注意注释](./Vue2源码解析/2.png)

![initLifecycle](./Vue2源码解析/5.png)

内部就是声明了各种和他有关系的组件，父、祖、子等

就是实例属性的初始化

![initEvents](./Vue2源码解析/3.png)

在使用自定义事件时，类似这种写法`<Child @my-click="onClick"></Child>`

回调函数（onClick）在父组件中声明的，所以用在initEvents中使用`_parentListeners`

`updateComponentListeners`是事件的监听

事件的监听和派发都是组件本身

例如：

```js
child.$emit('my-click')
child.$on('my-click', listeners)
```

![initRender](./Vue2源码解析/4.png)

在render中处理`$slots`和`$scopedSlots`这个比较好理解，在渲染前肯定是要把内部插槽先解析

`$_c`和`$createElement`就是 render(h)中的那个 h

`$_c`给编译器生成的渲染函数去使用

`$createElement`给用户生成的渲染函数去使用

问：new Vue 的时候都发生了什么？

答：首先构造函数的初始化会得到一个 Vue 的实例，我们给他传入 options，我们会得到一个组件实例，在内部会做根实例的初始化

1. 初始化时会做：当前实例的关键属性初始化，例如$parent,$root,$slots,$scopeSlots 等等
2. 同时对当前自定义组件的自定义事件的监听
3. 派发一些生命周期钩子，beforeCreate、created
4. 在这两个钩子中间，对组件的状态进行初始化，比如 data/props/methods/computed/watch，对他们进行数据响应式处理
