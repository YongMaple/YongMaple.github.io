---
title: vue-loader升级v13.0.0导致报错
date: 2017-12-07 07:44:09
tags:
  - 报错
---

报错如下：

```
Error: render function or template not defined in component: anonymous
```

解决办法：  
修改 vue-loader.config.js，添加 esModule: false

```
module.exports = {
    preserveWhitespace: false,
    postcss: [
        require('autoprefixer')({
            browsers: ['> 5%']
        })
    ],
    esModule: false
}

```

#### esModule

- 类型: Boolean
- 默认值: undefined

是否导出兼容 esModule 的代码，默认情况下 vue-loader 会导出 commonjs 格式，像 module.exports = ....。当 esModule 设置为 true 的情况下，导出会变为 exports.\_\_esModule = true; exports = ...。适用于与 Babel 以外的 transpiler 互操作，比如 TypeScript。
