---
title: ES6-Object.assign
date: 2017-01-19 15:07:47
categories: 前端
tags:
    - ES6
---
Object.assign将可以枚举的属性赋值给对象
<!--more-->
```javascript
var obj = {};
var skey = Symbol('testname');
var obj2 = Object.assign(obj, {name: 'leo'}, {age: 30},{[skey]:'ok good'});

console.log(obj === obj2, obj);
console.log(obj[skey]);

const DEFAULT_OPTIONS = {
    name: 'leo'
};

function test(opts) {
    let options = Object.assign({}, DEFAULT_OPTIONS, opts);
    console.log(options);
}

test();

test({name:'zengliang'});
```
