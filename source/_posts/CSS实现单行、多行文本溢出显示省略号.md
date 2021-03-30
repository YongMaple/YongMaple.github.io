---
title: CSS实现单行、多行文本溢出显示省略号
date: 2017-12-13 15:07:58
tags:
  - css
---

单行：

```css
overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;
```

多行：

```css
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
overflow: hidden;
```
