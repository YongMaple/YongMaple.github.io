---
title: Vue3中app.config.globalProperties在build后指向错误
date: 2020-09-29 07:44:09
categories: 报错
tags:
  - Vue3
  - 报错
---

在 Vue2 中，我们这样在全局挂载

```js
import moment from 'moment'
Vue.prototype.$moment = moment
```

在 Vue3 中，我们需要这样挂载

```js
import dayjs from 'dayjs'
app.config.globalProperties.$dayjs = dayjs
```

在引用时

```js
import { getCurrentInstance } from 'vue'
export default {
  setup() {
    const { ctx } = getCurrentInstance()
    ctx.$dayjs()
  },
}
```

上述方式在 dev 中没有问题，但是 build 之后就会报错
应该改为：

```js
import { getCurrentInstance } from 'vue'
export default {
  setup() {
    const { proxy } = getCurrentInstance()
    proxy.$dayjs()
  },
}
```
