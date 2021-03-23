---
title: ES6-Object.is
date: 2017-01-19 15:07:35
categories: ES6笔记
tags:
    - ES6
---
Object.is相当于全等又有些区别
```javascript
console.log(Object.is({},{}) , {} === {} ); //fasle false
console.log(Object.is(NaN,NaN) , NaN === NaN);  //true false
console.log(Object.is(+0,-0) , +0 === -0);  //false true
```
