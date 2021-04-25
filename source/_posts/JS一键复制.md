---
title: JS一键复制
date: 2021-04-25 15:13:47
categories: 前端
tags:
  - 前端
---

一键将数据存在剪切板上

代码如下：

<!-- more -->

```js
const handleCopy = (obj) => {
  // 清除上一次的
  const removeDom = document.getElementById('copyInput')
  if (removeDom) removeDom.remove()
  const text = document.createElement('textarea')
  text.id = 'copyInput'
  const str = `经度：${obj.lon}
纬度：${obj.lat}
时间段：${obj.time}`
  text.value = str
  const container = document.getElementById('lbsUserListContainer')
  container?.appendChild(text)
  text.select()
  document.execCommand('copy')
  text.style.display = 'none'
  message.success('复制成功')
}
```

注意：

`text.style.display = 'none';`隐藏需要在`document.execCommand('copy');`后，不然无法复制
