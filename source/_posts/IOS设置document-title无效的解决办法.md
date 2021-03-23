---
title: IOS设置document.title无效的解决办法
date: 2017-02-04 16:54:52
categories:
    - 编程思考
tags:
    - title
    - ios
    - 手机端
---
## 问题原因：

因为浏览器首次加载页面初始化title后，就再也不监听 document.title的change事件。



## 解决方案：

修改title之后，给页面加上一个内容为空的iframe，随后立即删除这个iframe，这时候会刷新title。但是如果简单的这样设置，一般是会有闪动的，所以可以设置

```javascript
setTitle: function(title) {
    let $body = $('body')
    document.title = title
    let $iframe = $('<iframe src="/favicon.ico"></iframe>')
    $iframe.on('load', function() {
      setTimeout(function() {
        $iframe.off('load').remove()
      }, 0)
    }).appendTo($body)
  }
```
参考地址：[http://www.cnblogs.com/lihanying/p/6227192.html](http://www.cnblogs.com/lihanying/p/6227192.html)
