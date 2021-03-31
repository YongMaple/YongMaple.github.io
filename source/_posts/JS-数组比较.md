---
title: JS-数组比较
date: 2017-01-18 10:27:39
categories: 前端
tags:
    - JavaScript
    - JS
    - 基础
    - 数组
    - 比较
---
```javascript
var x = [1,2];
var y = [1,2];

console.log(x == y);     //false
console.log(x === y );   //false
```
javascript不能直接用==或者===来判断两个数组是否相等，无论是相等还是全等都不行。   
要判断数组是否相等，要先将数组转换为字符串，再作比较
```javascript
var x = [1,2];
var y = [1,2];
console.log(x.toString() == y.toString())
```
