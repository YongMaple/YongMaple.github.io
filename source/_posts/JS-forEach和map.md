---
title: JS-forEach和map
date: 2017-01-18 10:38:49
categories: JS基础
tags:
    - JavaScript
    - JS
    - 基础
    - forEach
    - map
---
第1个是遍历的数组内容；   
第2个是对应的数组索引;   
第3个是数组本身   
```javascript
[].forEach(function(value, index, array) {
  // ...
});
```
举个栗子
```javascript
[1,2,3,4,5].map(function(v, i){return v > 3;});
//[false, false, false, true, true]
[1,2,3,4,5].forEach(function(v, i){return v > 3;});
//undefined
```
**map()方法**：   
若这个回调函数有返回值，map()方法会产生一个新数组，这个新数组由原数组元素对应的回调函数的返回值构成。   
若没有返回值，则新数组的所有元素为undefined。   
**forEach方法**：   
仅仅为每个数组元素执行回调函数。   

再举一个
```javascript
var result = [1, 2, 3, 4, 5].map(function(v, i) {
    return v+1
});
console.log(result);    //[2, 3, 4, 5, 6]
[1, 2, 3, 4, 5].forEach(function(v, i) {
    console.log(v,i)    //1 0
});
```
