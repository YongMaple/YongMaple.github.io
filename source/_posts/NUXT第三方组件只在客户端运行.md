---
title: NUXT第三方组件只在客户端运行
date: 2020-08-11 10:30:55
tags:
  - 报错
---

报错：`navigator is no defined`
原因：`navigator`只在客户端存在
解决：`plugins`下新建`vue-range-component.js`，代码如下

```js
import Vue from 'vue'
import 'vue-range-component/dist/vue-range-slider.css'
import VueRangeSlider from 'vue-range-component'
Vue.use(VueRangeSlider)
nuxt.config.js内新增
  plugins: [
    { src: "@/plugins/vue-range-component", ssr: false},
  ],
```

ssr 设为 false 即可
