---
title: JS-sort
date: 2017-01-18 10:37:54
categories: JS基础
tags:
    - JavaScript
    - JS
    - 基础
    - sort
---
```javascript
var arr=new Array(1,8,7,6);
arr.sort(function(a,b){
    return b-a
});
console.log(arrSimple2.join());
```
**解释**：a,b表示数组中的任意两个元素，若return > 0 b前a后；reutrn < 0 a前b后；a=b时存在浏览器兼容  
**简化一下**：a-b输出从小到大排序，b-a输出从大到小排序。
```javascript
var objectList = new Array();

function Persion(name, age) {
    this.name = name;
    this.age = age;
}
objectList.push(new Persion('jack', 20));
objectList.push(new Persion('tony', 25));
objectList.push(new Persion('stone', 26));
objectList.push(new Persion('mandy', 23));
//按年龄从小到大排序
objectList.sort(function(a, b) {
    return a.age - b.age
});
for (var i = 0; i < objectList.length; i++) {
    console.log('age:' + objectList[i].age + ' name:' + objectList[i].name);
}
```
也可以对对象的属性进行排序
