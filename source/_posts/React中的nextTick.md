---
title: React中的nextTick
date: 2021-03-29 15:57:09
tags:
  - React
  - AntV
  - nextTick
  - hooks
---

### 背景

在项目开发中，使用 AntV-L7 的 Draw-Control，需要实现在弹框中地图画圈框选范围。
使用了 hooks 的写法，在弹框出现后地图正常显示，点击画圈或其他绘图功能后，绘图的 canvas 只有约一半大小。
思考后认为应该是在`new DrawControl()`时，useState 还没执行赋值，错误范例如下：

```javascript
const handleDrawCircle = () => {
  setShowModal(true)
  scene = new Scene({
    id: 'drawCircleWrap',
    map: new GaodeMap({
      style: 'dark',
      center: [120.190494, 30.189643],
      pitch: 0,
      zoom: 4,
      token: GAODE_TOKEN,
    }),
  })
  scene.on('loaded', () => {
    drawControl = new DrawControl(scene, {
      position: 'topright',
      layout: 'horizontal',
      controls: {
        point: false,
        line: false,
        polygon: false,
        circle: true,
        rect: false,
        delete: true,
      }
    })
    scene.addControl(drawControl)
  })
}
```
按照以前写Vue的经验，只要用nextTick就能解决了，但是搜了一下，React中没有nextTick，但是有两种替代方式
1. `setTimeout(() => {}, 0)`
2. `useEffect(() => {}, [showModal])`
两种方式在我看来都不是很好看……最后还是决定用`setTimeout`，因为用`useEffect`还需要判断下showModal是否为true

如果用Class的写法，也可以这样`setState({},() => {})`
