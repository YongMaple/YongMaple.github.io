---
title: css裁切不规则图形
date: 2020-08-28 15:07:58
tags:
  - css
---

使用 clip-path 属性，简单的可以用 polygon 属性画不规则多边形，复杂的可以用 svg 的 path 处理

```html
<div class="box"></div>
<svg height="0" width="0" preserveAspectRatio="xMinYMin meet">
  <defs>
    <clipPath id="svgPath">
      <path
        d="M215,100.3c97.8-32.6,90.5-71.9,336-77.6
          c92.4-2.1,98.1,81.6,121.8,116.4c101.7,149.9,53.5,155.9,14.7,178c-96.4,54.9,5.4,269-257,115.1c-57-33.5-203,46.3-263.7,20.1
          c-33.5-14.5-132.5-45.5-95-111.1C125.9,246.6,98.6,139.1,215,100.3z"
      ></path>
    </clipPath>
  </defs>
</svg>
```

```css
.box {
  width: 500px;
  height: 500px;
  background-color: red;
  /* clip-path:polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%); */
  clip-path: url('#svgPath');
}
```
