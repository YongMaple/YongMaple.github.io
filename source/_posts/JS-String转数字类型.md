---
title: JS-String转数字类型
date: 2017-01-18 10:24:43
categories: JS基础
tags:
    - JavaScript
    - JS
    - 基础
    - 转换
---
### 需要将string转成数字时，如果需要保留小数，只要用+就行了
```javascript
var str = '123'
console.log(parseInt(str))
//转整数时没问题
var str1 = '123.45'
console.log(+str1)
//只要用+就行了
```
