---
title: Vite2工程化
date: 2021-03-30 16:18:49
categories: 前端
tags:
---

#### Vite 是什么

Vite 是一个开发构建工具，开发中它利用浏览器**native ES Module**特性按需导入源码，预打包依赖。
特点：

- 启动快
- 更新快

#### 创建

```bash
npm init @vitejs/app
```

这里选择 vue

#### 将资源引入为 URL

服务时引入一个静态资源会返回解析后的公共路径：

```js
// 解析为地址
import logo from './assets/logo.png' // 输出/src/assets/logo.png
```

使用这个路径

```html
<img :src="logo" />
```

可以打开 Chrome Network，看 App.vue，可以看到里面的地址是被解析后的公共路径

![](./Vite2工程化/1.jpg)

#### 设置别名

vite.config.js：

```js
import { resolve } from 'path'
export default defineConfig({
  ...
  resolve: {
    alias: {
      '@': resolve('./src'),
      'comps': resolve('./src/components')
    }
  }
})
```

App.vue

```js
import HelloWorld from 'comps/HelloWorld.vue'
import Logo from '@/assets/logo.png'
```

启动时要用`npm run dev`，使用`vite`启动控制台会报错如下：

```
[vite] Failed to resolve module import "@/assets/logo.png". (imported by /src/App.vue)
```

style 中同样可以使用别名来引入图片等

```css
.logo {
  background-image: url(@/assets/logo.png);
  width: 200px;
  height: 200px;
}
```

#### 全局样式

在 main.js 里引入

新建`/src/index.css`，把`App.vue`中的`#app {}`移入`index.css`，依然生效

#### scoped 与 module 样式

使用 scoped 时，元素会被强制加上`data-v-xxxx`，选择器的深度发生一些变化

使用 module 时，class 会 hash

两者相差不大，相对而言，module 可能更好一些

scoped 写法：

```html
<template>
  ...
  <div class="logo" />
  ...
</template>
<style scoped>
  .logo {
    background-image: url(@/assets/logo.png);
    width: 200px;
    height: 200px;
  }
</style>
```

module 写法：

```html
<template>
  ...
  <div :class="$style.logo" />
  ...
</template>
<style module>
  .logo {
    background-image: url(@/assets/logo.png);
    width: 200px;
    height: 200px;
  }
</style>
```

![scoped](./Vite2工程化/2.png)

![module](./Vite2工程化/3.png)

#### 加载模块化css

约定，在名称与css之间加上module，例如：`App.module.css`

新建`src/App.module.css`，将`.logo {}`移入，改写`App.vue`

```html
<template>
	<img alt="Vue logo" :src="Logo" />
	<div :class="classes.logo" />
	<HelloWorld msg="Hello Vue 3 + Vite" />
</template>

<script setup>
import HelloWorld from 'comps/HelloWorld.vue'
import Logo from '@/assets/logo.png'
// 加载模块化css
import classes from './App.module.css'
</script>
```

#### 使用less/sass

`npm i less -D`

然后就可以直接用了，sass同理

#### postcss

只要在项目中添加`postcss.config.js`就可以了

#### TS 整合

默认整合 ts，可以直接使用，`<script lang="ts">`即可，需要固定 ts 版本的话，在`package.json`的`devDependencies`中直接添加`"typescript": "4.1.5"`即可

tsconfig.json 可以自行配置后放入项目即可

#### 代理

`vite.config.js`中添加如下代码：

```js
export default defineConfig({
  ...
  server: {
    proxy: {
      "/api": {
        target: "***",
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\api/, ""),
      },
    },
  },
  ...
})
```

#### 数据 mock

安装依赖

```bash
npm i mockjs -S
npm i vite-plugin-mock -D
```

引入插件，`vite.config.js`中添加如下代码：

```js
import { viteMockServe } from 'vite-plugin-mock'
export default defineConfig({
  ...
  plugins: [ viteMockServe({}) ]
  ...
})
```

项目下创建`mock`文件夹，文件夹下就可以创建 mock 数据了，例如：

```js
export default [
  {
    url: '/api/users',
    method: 'get',
    response: (req) => {
      return {
        code: 0,
        data: [{ name: 'foo' }, { name: 'bar' }],
      }
    },
  },
]
```

#### 代码规范

使用`eslint + prettier`规范代码

添加如下依赖：

```json
{
  "devDependencies": {
    "@typescript-eslint/eslint-plugin": "^4.15.2",
    "@typescript-eslint/parser": "^4.15.2",
    "@vue/eslint-config-prettier": "^6.0.0",
    "@vue/eslint-config-typescript": "^7.0.0",
    "@vuedx/typescript-plugin-vue": "^0.6.3",
    "eslint": "^7.20.0",
    "eslint-plugin-prettier": "^3.3.1",
    "eslint-plugin-vue": "^7.6.0",
    "prettier": "^2.2.1"
  }
}
```

未完待续
