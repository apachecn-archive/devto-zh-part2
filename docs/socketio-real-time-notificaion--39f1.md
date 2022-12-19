# 插座。IO 实时通知

> 原文：<https://dev.to/sudoshreyas/socketio-real-time-notificaion--39f1>

我试图实现的是实时通知。

相应地起作用的事物有

1.能够发送请求
2。服务器获取数据，因此数据库更新
3。通知是实时的

我面临的问题是:
一旦用户连接到一个特定的房间，比如说 localhost:3000/user/user123，那么 user123(已登录的用户)就能够获得请求的通知，但是一旦我改变了房间，比如说我访问了 localhost:3000/通知就消失了。

我希望它正在做的:
我希望无论房间或网站中的任何页面，用户都应该得到通知。

下面是代码:
服务端-[https://pastebin.com/cAAKwMXj](https://pastebin.com/cAAKwMXj)T3】客户端-【https://pastebin.com/yeVgP5vW】T4