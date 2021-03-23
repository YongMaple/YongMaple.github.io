---
title: ES6-Array
date: 2017-01-18 10:20:04
categories: ES6笔记
tags:
    - ES6
---
### Array.from
类似数组的数据结构转换为数组   
可以把带有length属性的对象和实现了iterator接口的对象转换为数组
```javascript
var arr = Array.from({
'0':'qq 740534248',
'1':'jimmy',
'length':2
},function(item,index){
 return item + '---' + index
});
//必须是这种样子的对象，其他的不行
console.log(arr);

var set = new Set(['me','book','leo']);
console.log(Array.from(set));
```
querySelectorAll查询出的类数组也能转换
```html
<ul>
    <li>item1</li>
    <li>item2</li>
    <li>item3</li>
    <li>item4</li>
    <li>item5</li>
</ul>

<script>
    var list = document.querySelectorAll('li');
    console.log(list)  //NodeList[5]
    console.log(Array.from(list));  //ArrayList[5]
</script>
```
### Array.of
用来构建数组，防止Array(7)这种情况发生
```javascript
Array.of(7);       // [7]
Array.of(1, 2, 3); // [1, 2, 3]

Array(7);          // [ , , , , , , ]
Array(1, 2, 3);    // [1, 2, 3]
```
### find
找到符合函数的第一个值
```javascript
var arr = [  22,33,44,55 ];

var value = arr.find(function(value,index,arr){ //值，索引，原数组
   return value > 33;
});

console.log(value); //44
```

### findIndex
找到符合函数的第一个值的索引
```javascript
var arr = [  22,33,44,55 ];

var value = arr.findIndex(function(value,index,arr){ //值，索引，原数组
   return value > 33;
});

console.log(value); //2
```
IndexOf比较
```javascript
var arr2 = [22,NaN];

console.log(arr2.indexOf(NaN))  //-1
console.log(arr2.findIndex(function(v){
   return Object.is(NaN,v);
}));    //1
```
### copyWithin
copyWithin(position,start,end);
要被替换的开始位置，用于替换的起始位置，用于替换的结束位置（不包含）   
也可以用负数，代表从后往前数第几个，如-3代表正数第4个
```javascript
ar arr = [1,2,3,4,5,6,7];

//arr.copyWithin(1,5,7);  //[ 1, 6, 7, 4, 5, 6, 7 ]
arr.copyWithin(2,-3,6); //[ 1, 2, 5, 6, 5, 6, 7 ]
console.log(arr);
```
### fill
填充数组   
fill(value,start,end)   
用来填充的值，被填充的起始位置，被填充的结束位置
```javascript
var arr = [1,2,3,4,5,6,7];
//arr.fill(10)
console.log(arr)    //[10,10,10,10,10,10,10]
arr.fill(10,2,5)
console.log(arr)    //[1,2,10,10,10,6,7]
```
