---
title: Vue和React设计思路
date: 2021-03-29 07:44:09
tags:
---
#### 发展历程
- Vue1 
  只有响应式，没有vdom，所有的都是通知的机制
- React15
  Class Component
- Vue2
  引入了vdom
- React16
  引入了Fiber和Hooks
- Vue3 & React17

#### Vue3 & React17
- Vue OptionApi
  1. 优点：结构清晰，好理解，数据去data，方法去methods等，易学。
  2. 缺点：就是反复横跳，在处理一个逻辑时，需要在template、data、methods等反复操作。mixins有个问题，可能命名冲突，同时mixins时this是个黑盒，里面有什么是不知道的，ts支持差
- Vue CompositionApi
  1. 缺点：难看，不如Option简单。return很蛋疼，return也要反复横跳
  2. 优点：
    - 可以做tree-shaking，比如没有用到computed，代码build的时候就会删掉Vue3里面的computed的代码
    - 组件可以任意拆分 方便组合 逻辑都是函数，组合优于继承
    - 命名冲突了只要:起个别名就行了
- 渐进式更新ref api
  - setup写在script，解决return等冗余
  - 方言：`let x = ref(0)` => `ref: x = 1`使用时可以不用写`x.value`
- composition和hooks的区别
  - 语法上比较像，但底层设计上区别很大
  - hooks中例如useState每次render都会执行，所以有严格的顺序要求，不能写在if里面
  - composition后续靠的是响应式通知
  - 都采用组合的形式，函数式的组件是未来
- vue 响应式 + vdom
  - 响应式： 数据变了通知你
  - vdom： 数据变了你不知道哪里变了，需要算一次diff，才知道变化
  - vue1只有响应式，项目大了之后，响应式对象太多，导致卡顿
- react纯vdom
  - vdom树太大，diff时间超过16.6ms，会导致卡顿
- React Class => hooks

#### JSX 和 Template


未完待续


