---
title: Atom使用eslint需要安装的包
date: 2017-01-17 16:39:41
categories: 工具
tags:
    - Atom
    - eslint
---
```javascript
"dependencies": {
    "eslint": "^3.13.1",
    "eslint-config-airbnb": "^14.0.0",
    "eslint-plugin-import": "^2.2.0",
    "eslint-plugin-jsx-a11y": "^3.0.2",
    "eslint-plugin-react": "^6.9.0"
  }
```
```npm
npm install eslint --save
eslint -v
eslint --init
```
```npm
npm install eslint-config-airbnb --save
```
```npm
npm install eslint-plugin-import --save
```
```npm
npm install eslint-plugin-jsx-a11y --save
```
```npm
npm install eslint-plugin-react --save
```
.eslintrc.js
```javascript
module.exports = {
    "extends": "airbnb",    //使用airbnb前端编码规范
    "installedESLint": true,
    "rules": {
        "indent": ["error", 4]  //缩进4个空格
    }
};
```
