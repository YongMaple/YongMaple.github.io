---
title: hexo中图片组件hexo-asset-image的使用
date: 2021-03-16 17:30:10
tags:
---

hexo中直接通过`npm i hexo-asset-image`下载的组件由于没人维护，目前无法直接使用。
根据[https://blog.csdn.net/xjm850552586/article/details/84101345](https://blog.csdn.net/xjm850552586/article/details/84101345)的代码.
我fork了原`hexo-asset-image`项目，在npm上发布了`hexo-assets-images`组件。

### 使用
```
npm install hexo-asset-image --save
```

`hexo new post [title]`后，会在_posts文件夹下生成同名文件夹
在需要插入图片的地方写`![logo](logo.jpg)`，将插入`[title]`文件夹下logo.jpg.