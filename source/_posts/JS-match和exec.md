---
title: JS-match和exec
date: 2017-01-18 10:34:52
categories: 前端
tags:
    - JavaScript
    - JS
    - 基础
    - match
    - exec
    - 正则
---
举个栗子
```javascript
var order = 'is2'
var arr = order.match(/\d+/)
console.log(arr)
//["2", index: 2, input: "is2"]

var arr2 = /\d+/.exec(order);
console.log(arr2)
//["2", index: 2, input: "is2"]
```
值得注意的是match和exec返回的结果都是数组
