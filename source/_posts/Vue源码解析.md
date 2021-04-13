---
title: Vue源码解析
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

### 入口

从 package.json `dev`中找到`/scripts/config.js`

`rollup -w -c scripts/config.js --sourcemap --environment TARGET:web-full-dev`命令中传入了环境变量`web-full-dev`

在`/scripts/config.js`中查找，可以在配置中找到入口文件`web/entry-runtime-with-compiler.js`

文件地址前有个resolve方法

```js
const resolve = p => {
  // 拆分/
  // web/entry-runtime-with-compiler.js
  const base = p.split('/')[0]  // web
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

const resolve = p => path.resolve(__dirname, '../', p)

module.exports = {
  vue: resolve('src/platforms/web/entry-runtime-with-compiler'),
  compiler: resolve('src/compiler'),
  core: resolve('src/core'),
  shared: resolve('src/shared'),
  web: resolve('src/platforms/web'),
  weex: resolve('src/platforms/weex'),
  server: resolve('src/server'),
  sfc: resolve('src/sfc')
}
```

现在找到了入口文件的位置`src/platforms/web/entry-runtime-with-compiler.js`

扩展了$mount

未完待续