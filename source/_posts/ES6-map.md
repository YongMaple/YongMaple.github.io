---
title: ES6-map
date: 2017-01-18 10:21:15
categories: ES6笔记
tags:
    - ES6
---
## 是什么？
map是键值对的类数组

## 可以用来做什么？
对象中的key必须是`string`，map中key可以是任意值
```javascript
let user = {
    name : 'jiayongfeng',
    age : 28,
    'english name':'jimmy'
}
let map = new Map([
    [user,'shuai'],     //key可以是对象
    ['qq','740534248']
])
```

## 怎么用？
```javascript
let map = new Map()
map.set('name','jiayongfeng')   //赋值
map.set('age',28)
console.log(map.get('age'))     //取值
console.log(map.size)           //查看大小
map.delete('age')               //删除
map.has('name')                 //判断有无
map.clear()                     //清空
//值得注意NaN在map和set是全等的
console.log(NaN === NaN)    //false
map.set(NaN,'1111');
map.set(NaN,'2222');
console.log(map.get(NaN));    //2222

for(let o of map){              //遍历
    console.log(o)
}
let keys = map.keys()           //获取所有的key
let values = map.values()       //获取所有的value
let entries = map.entries()     //获取所有的键值对
for(let o of entries){
    console.log(o)              //等同于上面的遍历
}


var _this = {
    des : 'shuai'
};
map.forEach(function (k, v) {       //遍历，同时可以传入this
    console.log(this.des,k, v);
},_this);
```
