---
title: JS父元素hover显示子元素且移入子元素不隐藏
date: 2021-04-25 15:16:40
categories: 前端
tags:
  - 前端
  - React
---

关键方法如下：

<!-- more -->

```js
let hoverTime
const handleMouseEnter = (index) => {
  if (!props.hoverInfo) return
  clearTimeout(hoverTime)
  setHoverIndex(index)
  if (props.onHoverChange) props.onHoverChange(index)
}
const handleMouseLeave = () => {
  if (!props.hoverInfo) return
  hoverTime = setTimeout(() => {
    setHoverIndex(-1)
  }, 200)
}
```

原理：

通过`setTimeout`延迟隐藏，给用户时间从父元素移动到子元素，移入元素后清空定时器
