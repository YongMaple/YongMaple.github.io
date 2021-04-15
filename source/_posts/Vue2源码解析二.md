---
title: Vue2源码解析二
date: 2021-04-15 16:00:05
tags:
---

目录：

- [Vue2 源码解析](https://yongmaple.com/2021/04/13/Vue2源码解析/)
- [Vue2 源码解析二](https://yongmaple.com/2021/04/13/Vue2源码解析二/)

本文项目地址：[https://github.com/YongMaple/vue](https://github.com/YongMaple/vue) 内含测试用代码`/examples/test/`

### 异步更新队列

先了解一下`Event Loop`

推荐阅读：

- [https://segmentfault.com/a/1190000016278115](https://segmentfault.com/a/1190000016278115)
- [https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/?utm_source=html5weekly](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/?utm_source=html5weekly)

![Event Loop](./Vue2源码解析二/1.png)

- Task Queue 宏任务队列
- Microtask Queue 微任务队列
- Call Stack 调用栈
- 浏览器端的宏任务：setTimeout、setInterval、requestAnimationFrame、I/O、UI rendering
- 浏览器端的微任务：Promise、Object.observe、MutationObserver

简单总结下浏览器下的 Event loop 流程：

1. 先走完全局同步任务
2. 然后走完当前所有的微任务
3. 执行 UI rendering
4. 走队列第一个宏任务
5. 走完当前所有的微任务
6. 执行 UI rendering
7. 如此循环，如果在微任务执行期间，出现了新的微任务，直接跟在这次微任务队列的末尾（在下次宏任务之前执行）
