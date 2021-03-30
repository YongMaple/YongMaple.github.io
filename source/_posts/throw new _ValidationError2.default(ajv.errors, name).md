---
title: throw new _ValidationError2.default(ajv.errors, name)
date: 2020-08-11 15:07:22
tags:
  - 报错
---

### 错误如图

![](http://okmneu7zl.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-12-11%20%E4%B8%8B%E5%8D%887.43.40.png)

```bash
tc-travel-frontend-activity@ build /Users/jimmy/work/tc-flight/project/tc-travel-frontend-activity
> rimraf dist && npm run build:client && npm run build:server


> tc-travel-frontend-activity@ build:client /Users/jimmy/work/tc-flight/project/tc-travel-frontend-activity
> cross-env NODE_ENV=product webpack --config build/webpack.client.config.js --progress --hide-modules

/Users/jimmy/work/tc-flight/project/tc-travel-frontend-activity/node_modules/schema-utils/dist/validateOptions.js:40
    throw new _ValidationError2.default(ajv.errors, name);
    ^

false
```

### 原因

webpack 版本升级导致

### 解决

修改`webpack.client.config.js`

```js
vueConfig.loaders = {
  // stylus: ExtractTextPlugin.extract({
  //   loader: 'css-loader!stylus-loader',
  //   fallbackLoader: 'vue-style-loader' // <- this is a dep of vue-loader
  // }),
  less: ExtractTextPlugin.extract({
    loader: 'css-loader!less-loader',
    fallbackLoader: 'vue-style-loader',
  }),
}
```

为

```
vueConfig.loaders = {
    // stylus: ExtractTextPlugin.extract({
    //   loader: 'css-loader!stylus-loader',
    //   fallbackLoader: 'vue-style-loader' // <- this is a dep of vue-loader
    // }),
    less: ExtractTextPlugin.extract({
        use: "css-loader!less-loader",
        fallback: "vue-style-loader"
    })
}
```
