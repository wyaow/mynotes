如何使用telnet命令，cmd或者powershell执行
1.telnet 127.0.0.1 8080
2.按下Ctrl + ] 键盘， 然后咋按回车键。
3.鼠标右键可以复制
4.按回车发送




1.发送单纯的HTTP1.1请求
GET /rest/v1/healthcheck HTTP/1.1
Host: 127.0.0.1:8080
Connection: keep-alive

抓个包看下，的确没问题，如下






2.协议升级，HTTP1.1升级到websocket

可以参照：https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Upgrade
GET /websocket HTTP/1.1
Host: 127.0.0.1:8080
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Key: LXzbfbJiKUgBazORJVUhGw==
Sec-WebSocket-Version: 13



3.协议升级，HTTP1.1升级HTTP2，这里只介绍h2c（即HTTP2, 不是HTTP2+TLS）
GET / HTTP/1.1
Host: 127.0.0.1:8080
Connection: Upgrade,HTTP2-Settings
Upgrade: h2c
HTTP2-Settings:

GET / HTTP/1.1
Host: 127.0.0.1:8080
Connection: Upgrade,HTTP2-Settings
Upgrade: h2c
HTTP2-Settings: QUFFQUFCQUFBQ***************
