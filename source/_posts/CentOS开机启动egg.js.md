---
title: CentOS开机启动egg.js
date: 2018-04-01 15:07:58
categories: 工具
tags:
  - egg.js
  - CentOS
---

```
vim /etc/rc.d/rc.local
```

添加

```
cd /xdx/xdx-api && npm run start
```
