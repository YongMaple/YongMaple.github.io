---
layout: _post
title: webpack项目提示Invalid Host header
date: 2018-06-24 19:58:03
tags:
  - 报错
  - webpack
---

[原文地址](https://blog.csdn.net/bodhiye/article/details/78523333)

在build目录中的webpack.base.config.js中添加如下webpack-dev-server配置：
```
devServer: {
    disableHostCheck: true,
},
```