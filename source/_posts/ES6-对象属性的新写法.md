---
title: ES6-对象属性的新写法
date: 2017-01-19 15:07:22
categories: ES6笔记
tags:
    - ES6
---
```javascript
class A {
    constructor() {
        this.name = 'leo';
    }

    getName() {
    }
}

class B extends A{
    constructor(){
        super();
        this.age = 22;
    }

    getAge(){

    }

    [Symbol('fullname')](){

    }
}

B.prototype.getClass = function () {

};

var b = new B;

// Object.keys 能够得到自身的可枚举的属性,但得不到原型链上的属性.
// 得不到 Symbols 属性
console.log(Object.keys(B.prototype));

// Object.getOwnPropertyNames(b) 能够得到自身属性,
// 包括不可枚举的属性,但得不到原型链上的属性.
// 得不到 Symbols 属性
console.log(Object.getOwnPropertyNames(B.prototype));

// 可以得到自身的 Symbols 属性,包括不可枚举的属性.
console.log(Object.getOwnPropertySymbols(B.prototype));

// 可以得到自身和继承的原型链上的属性,但必须是可枚举属性.
for(let key in b){
    console.log(key);
}
// 判断是否可枚举，enumerable为true可枚举
console.log(Object.getOwnPropertyDescriptor(B.prototype,'getAge'));
```
