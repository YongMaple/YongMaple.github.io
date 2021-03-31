---
title: JS-arguments
date: 2017-01-18 10:29:30
categories: 前端
tags:
    - JavaScript
    - JS
    - 基础
    - 参数
---
```javascript
function test(a,b) {
    console.log(arguments);         //["123"]
    console.log(arguments.length);  //1
    console.log(test.length);       //2
}
test('123')
```
通过arguments可以获取传入的参数，function.length可以获取本该传入的参数
