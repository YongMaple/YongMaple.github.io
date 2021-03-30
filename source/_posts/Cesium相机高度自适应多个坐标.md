---
title: Cesium相机高度自适应多个坐标
date: 2020-08-26 15:07:58
tags:
  - Cesium
---

获取东南西北经纬度，使用 Rectangle.fromDegrees 构建矩形

```js
let west, south, east, north
for (let i = 0; i < arr.length; i++) {
  const item = arr[i]
  if (!west || item.assetIpLng < west) west = item.assetIpLng
  if (!south || item.assetIpLat < south) south = item.assetIpLat
  if (!east || item.assetIpLng > east) east = item.assetIpLng
  if (!north || item.assetIpLat > north) north = item.assetIpLat
  _this.$emit(
    'addPoint',
    {
      lon: item.assetIpLng,
      lat: item.assetIpLat,
    },
    []
  )
}
```

再 flyTo

```js
this._viewer.scene.camera.flyTo({
  destination: Cesium.Rectangle.fromDegrees(west, south, east, north),
})
```
