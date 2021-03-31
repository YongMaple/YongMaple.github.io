---
title: 在mac上使用android模拟器通过chrome调试webview内页面
date: 2021-02-09 14:23:05
tags:
  - android模拟器
  - webview
---

#### 准备工作
1. 钉钉dev App [下载地址](http://download.alicdn.com/wireless/dingtalk/latest/rimet_10006337.apk?spm=ding_open_doc.document.0.0.4f077391n1eV6r&file=rimet_10006337.apk)
2. 网易mumu模拟器（不使用VirtualBox等违规软件）
3. adb工具包
• 检查是否安装过 `adb devices`
• 通过brew安装 `brew cask install android-platform-tools`
• 检查不通过的话，重启一下 `adb kill-server && adb server && adb shell`
4. android开发工具(只为了android的驱动，如果可以连接，那就不用装了) [下载地址](https://developer.android.com/)
5. chrome浏览器
#### 操作
1. 在mumu中安钉钉dev.apk，拖进去就行
2. 在mumu中打开`开发者模式`，并启用`usb调试`
3. 在mumu文件管理中点击左上角图标，再点击左下角设置图标，选择常规设置-访问模式，选择超级用户访问模式
4. 在mumu文件管理中搜索hosts，添加mac的ip地址和local.alipay.net 如：`30.38.33.35       local.alipay.net`
5. 打开钉钉dev，前往要调试的页面
6. 打开chrome，输入`chrome://inspect`
7. 选择对应页面调试
#### 可能存在的问题
1. 如果手机打开页面后，inspect中仍然看不到，就重启adb  `adb kill-server && adb server && adb shell`