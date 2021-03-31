---
title: 用JS获取translate3d中的数值
date: 2021-02-09 14:21:42
categories: 前端
tags:
  - translate3d
---

代码如下：

<!-- more -->

```js
// 获取DOM
const scrollContainer = document.getElementsByClassName(
  'viewer-container__viewer-image-container'
)[0]
const imgDom = scrollContainer.childNodes[0]
// 获取transform
const translates = document.defaultView.getComputedStyle(imgDom, null).transform //  matrix(a,b,c,d,e,f)
// 分割matrix矩阵，需要先清除多出来的一个)
const split = translates.replace(')', '').split(',')
// 获取y轴值
const y = Number(split[split.length - 1])
// 获取x轴值
const x = Number(split[split.length - 2])
```
