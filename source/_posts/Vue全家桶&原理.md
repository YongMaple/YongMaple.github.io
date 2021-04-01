---
title: Vue全家桶&原理
date: 2021-04-01 19:47:56
tags:
---

#### 准备工作

创建一个新的项目，`vue create vue-study`，选择 vue2，添加 vue-router 和 vuex，` vue add router``vue add vux `

#### vue-router

Vue router 是 Vue.js 官方的路由管理器。它和 Vue 的核心深度集成，让构建单页面应用易如反掌

##### 核心步骤

```js
// src/router/index.js
// 使用vue-router插件
import VueRouter from 'vue-router'
Vue.use(VueRouter)
// 创建Router实例
const router = new VueRouter({
  routes
})
// src/main.js
// 在根组件上添加该实例
import router from './router'
new Vue({
  router,
  render: h => h(App)
}).$mount('#app')
// src/App.vue
// 添加路由视图
<router-view/>
// 导航
<router-link to="/">Home</router-link>
<router-link to="/about">About</router-link>
this.$router.push('/')
this.$router.push('/about')
```
##### 目标
- 实现一个插件
  - 实现VueRouter类
    - 处理路由选项
    - 监控url变化，hashchange
    - 响应这个变化
  - 实现install方法
    - $router注册（this.$router.push）
    - 两个全局组件（router-link,router-view）

##### 实现
为了检测后续成果，直接在`src/router/`下新建一个`vuerouter.js`，并将`src/router/index.js`中的`import VueRouter from 'vue-router'`改为`import VueRouter from './vuerouter'`。如果实现了目标，可以直接查看效果。

要实现一个Vue插件，要给当前类实现一个静态的`install`方法，`install`方法将来会被vue调用，该函数接收Vue构造函数。
```js
class VueRouter {}

VueRouter.install = function(_Vue) {

}

export default VueRouter
```

为了组件中能够使用`this.$router.push()`，需要挂载路由器实例$router
需要实现`Vue.prototype.$router = router`
router在`/src/router/index.js`的`const router = new VueRouter({ routes })`中传入
但是`Vue.use(VueRouter)`时会立刻调用`install`方法，在install执行时router实例还不存在
所以需要使用`Vue.mixin`
```js
let Vue
class VueRouter {}
// install.call(VueRouter, Vue) install调用时，如此传入_Vue
VueRouter.install = function(_Vue) {
  
  Vue = _Vue
  Vue.mixin({
    beforeCreate() {
      // 这些代码延迟到了组件实例化的时候才执行，这时就可以获取到组件选项了
      // 这些代码会在所有组件中都执行，但是只有在根实例中才有router，所以只在根实例中才执行如下代码
      if (this.$options.router) {
        Vue.prototype.$router = this.$options.router
      }
    }
  })
}

export default VueRouter
```

此时如果运行项目，会报错`Unknown custom element: <router-link>`和`Unknown custom element: <router-view>`

注册全局组件`router-view`和`router-link`


未完待续