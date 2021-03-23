---
title: ES6-set
date: 2017-01-18 10:22:29
categories: ES6笔记
tags:
    - ES6
---
## 是什么？
set是类数组
## 可以用来做什么？
可以用来给数组去重
```javascript
let arr =  ['jiayongfeng','jimmy','jimmy']
let newArr = Array.from(new Set(arr))
console.log(newArr)
```
## 怎么用？
```javascript
let user = {name:'jiayongfeng',age:'28'}
let st = new Set()  
st.add(user)        //添加
st.delete(user)     //删除
st.has(user)        //判断有无
st.clear()          //清空
console.log(st.size)    //查看数量
for(let o of st){       //遍历
    console.log(o)
}
```
