---
title: CentOS设置redis开机启动
date: 2018-04-01 15:07:58
tags:
  - redis
  - CentOS
---

```
vim /etc/rc.d/rc.local
```

在文件末尾追加

```
/usr/local/redis-3.0.3/src/redis-server /usr/local/redis-3.0.3/redis.conf
```

[原文](https://blog.csdn.net/lishirong/article/details/53101513)

[参考](https://blog.csdn.net/wzq793957419/article/details/55102783)
