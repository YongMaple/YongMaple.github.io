---
title: 编写高质量JavaScript代码的68个有效方法（译）
date: 2017-03-29 13:20:09
categories: 翻译
tags: 
    - 翻译
---
### 1.了解JavaScript
JavaScript的设计让我们觉得很熟悉。语法让人联想到Java和许多脚本语言中常见的构造（例如：函数，数组，字典和正则表达式）。JavaScript看起来似乎是一个任何有点编程经验或新手程序员都可以快速上手的语言。因为它语言中的核心概念很少。

##### 知道你正在使用的JavaSctipt版本
和大多数成功的技术一样，JavaScript随着时间的推移而发展。从最初作为Java在网页互动编程上的补充，最终取代了Java成为Web开发上主流的编程语言。因其受欢迎程度，1997年形成了国际标准，官方命名为ECMAScript。目前已经有了很多实现了ECMAScript标准的JavaScipt版本。

1999年定稿的第三版ECMAScript标准（通常称为ES3），依然是目前使用最广泛的JavaScript版本。后一代标准是2009发布的第5版（或者叫ES5），ES5引入了许多新功能，并且将一些之前广泛支持却没有被指定的特性标准化。由于ES5还没有被普遍支持，所以我将在本书中标注出哪些语句或块中使用了ES5。

除了标准版本之外，还有很多非标准的特性，受到一些JavaScript实现的支持。例如,许多JavaScript引擎支持一个`const`关键字来定义变量,然而ECMAScript标准并没有定义任何关于常量的语法或行为。而且,在不同实现中`const`的行为也是不同的。在某些情况下,阻止常量变量更新:

```JavaScript
const PI = 3.141592653589793;
PI = "modified!";
PI;  // 3.141592653589793
```
还有一些实现中，则直接将`const`是为`var`：

```JavaScript
const PI = 3.141592653589793;
PI = "modified!";
PI;  // "modified!"
```
