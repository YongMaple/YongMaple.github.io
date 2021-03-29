---
title: Retina视网膜屏幕适配
date: 2017-01-19 15:10:57
categories: 编程思考
tags:
    - 适配
    - 手机端
    - Retina
---
直接上代码：
<!--more-->
```html
<!DOCTYPE>
<html>
<header>
    <style>
        div {
            border: 1px solid #bbb;
        }
        .hairlines div {
            border-width: .5px;
        }
    </style>
</header>
<body>
    <div>

    </div>
</body>
<script>
    if (window.devicePixelRatio && devicePixelRatio >= 2) {
        var testElem = document.createElement('div');
        testElem.style.border = '.5px solid transparent';
        document.body.appendChild(testElem);
        if (testElem.offsetHeight == 1){
            document.querySelector('html').classList.add('hairlines');
        }
        document.body.removeChild(testElem);
    }
</script>
</html>

```
> window.devicePixelRatio是设备上物理像素和设备独立像素(device-independent pixels (dips))的比例。
公式表示就是：window.devicePixelRatio = 物理像素 / dips  

> offsetHeight = clientHeight + 滚动条 + 边框   

如果是retina屏，会先创建个div来测试浏览器是否能探测到0.5px，如果可以会才会使用0.5px的样式   

原文地址：[http://dieulot.net/css-retina-hairline](http://dieulot.net/css-retina-hairline)
