---
layout: _post
title: VueCli中代理websocket
date: 2020-09-29 19:58:03
categories: 前端
tags:
  - websocket
---

vue.config.js

```js
devServer: {
    host: '0.0.0.0',
    port: '9007',
    proxy: {
        '/socket': {
            target: `ws://${baseUrl}`,
            ws: true,
            pathRewrite: { '^/socket': '' },
            changeOrigin: true,
        },
    }
}
```

页面中

```js
let socket = new WebSocket(
  'ws://' + location.host + '/socket/wx-cyberspace/websocket/attack'
)
```

nginx 关键配置

```
http {
	map $http_upgrade $connection_upgrade {
		default upgrade;
		''      close;
	}

	server {
        listen       8083;
        server_name  localhost;

		location ^~ /socket {
            proxy_pass http://localhost:8080/;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_read_timeout 120s;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
        }

	}

}
```
