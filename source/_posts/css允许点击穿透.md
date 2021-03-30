---
title: CSS实现单行、多行文本溢出显示省略号
date: 2018-04-01 15:07:58
tags:
  - css
---

CSS 新属性 pointer-events：字面理解是点击鼠标事件，值分别是 auto 和 none。

当使用 pointer-events:none,表示它将捕获不到任何点击，而只是让事件穿透到它的下面。代码如下

```css
.foo {
  pointer-events: none;
}
```
