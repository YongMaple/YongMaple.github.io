---
title: iOS9 使用Universal Links在微信中打开App
date: 2017-12-13 17:30:10
tags:
  - iOS
---

参考地址[http://www.jianshu.com/p/738ac2b8865d](http://www.jianshu.com/p/738ac2b8865d)

下载部分组件

```html
<template>
  <div
    :class="['fixed',isIphonex && !fringeDirection ? 'isX' : '']"
    v-iphonex
    v-show="show"
  >
    <img
      src="../../images/cancel.png"
      alt="关闭"
      class="close"
      @click="show = false"
    />
    <img src="../../images/logo.png" alt="logo" class="logo" />
    <div class="slogan">
      <p>差旅无忧</p>
      <p>让忙碌的生活更美好</p>
    </div>
    <button class="download" @click="toDownload">立即下载</button>
  </div>
</template>
<script>
  import utils from '@/common/utils.js'
  export default {
    data() {
      return {
        show: true,
        isIphonex: false,
        fringeDirection: '',
        schemaUrl: 'tcbiz://home/gotoHome',
        iphoneDownUrl: 'https://itunes.apple.com/app/id1282893090',
        txappcenterUrl:
          'http://a.app.qq.com/o/simple.jsp?pkgname=com.businesstravel&fromcase=40002',
        universalLinks: 'http://travelbusinessapi.17u.cn/action',
      }
    },
    mounted() {
      let _this = this
      this.$iphonex.$on('init', (v) => {
        _this.isIphonex = v
      })
      this.$iphonex.$on('rotate', (v) => {
        _this.fringeDirection = v
      })
    },
    methods: {
      isWeixin() {
        //判断是否是微信
        let ua = navigator.userAgent.toLowerCase()
        if (ua.match(/MicroMessenger/i) == 'micromessenger') {
          return true
        } else {
          return false
        }
      },
      toDownload() {
        // 微信无法直接打开App
        if (this.isWeixin()) {
          if (utils.isIOS9()) {
            window.location = this.universalLinks
          } else {
            window.location = this.txappcenterUrl // 应用宝地址
          }
        } else {
          let _this = this
          // 如果是iOS，
          if (navigator.userAgent.match(/(iPhone|iPod|iPad);?/i)) {
            let loadDateTime = new Date()
            window.setTimeout(function () {
              let timeOutDateTime = new Date()
              if (timeOutDateTime - loadDateTime < 5000) {
                window.location = _this.iphoneDownUrl //AppStore下载地址
              } else {
                window.close()
              }
            }, 25)
            window.location = _this.schemaUrl
          } else if (navigator.userAgent.match(/android/i)) {
            try {
              window.location = _this.schemaUrl
              setTimeout(function () {
                window.location = _this.txappcenterUrl //应用宝地址
              }, 500)
            } catch (e) {}
          }
        }
      },
    },
  }
</script>

<style lang="less" scoped>
  ...;
</style>
```

apple-app-site-association 文件

```json
{
  "applinks": {
    "apps": [],
    "details": [
      {
        "appID": "********",
        "paths": ["/action"]
      }
    ]
  }
}
```

注意点：

- 必须跨域！必须跨域！必须跨域！iOS9.2+必须跨域才能跳转，所以在实际开发中，使用`https://travelbusinessapi.17u.cn/apple-app-site-association`放 apple-app-site-association 文件，页面使用`http://travelbusiness.17u.cn/`
- apple-app-site-association 所在域名必须是 https 的
- paths 不可为\*
