# 启动 Clix:带 AWS SAM 的推送器存在通道

> 原文：<https://dev.to/kayis/startup-clix-pusher-presence-channels-with-aws-sam-4li0>

在我列出了一些关于我想做的游戏的基本想法后，我终于有时间开始实施了。

## 发生了什么事

实际上不多...我已经坐了 8 个小时了。

Pusher 很容易上手，但是 AWS SAM & Lambda & API-Gateway 就没那么简单了。

## 项目设置

首先，我在 GitHub 上创建了一个 [repo，我将在其中发布我的游戏。](https://github.com/kay-is/startup-clix)

我把它分成后端和前端，后端是一个 AWS SAM 应用程序，前端目前是一堆垃圾。

您需要一个服务器来处理 Pusher 存在通道的认证，我需要它们来跟踪玩家并将事件分发给游戏中的所有玩家。

### 后端

目前它由 3 个λ函数组成。`back-ed/functions`目录中的 JavaScript 通过`template.yaml`连接起来。 [AWS SAM Local](https://github.com/awslabs/aws-sam-local) 用于部署。

*   每分钟都会被 CloudWatch 调用，并向唯一频道发布一条测试消息

这是带有 1 分钟速率调度事件的 Lambda 函数的外观:

```
 HelloFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: ./functions/hello
      Events:
        ScheduleOneMinuteEvent:
          Type: Schedule
          Properties:
            Schedule: rate(1 minute) 
```

Enter fullscreen mode Exit fullscreen mode

*   `pusherAuth`被推送客户端调用，以允许客户端加入呈现通道

这是一个 Lambda 函数在 API-Gateway POST 事件中的样子:

```
 PusherAuthFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: ./functions/pusherAuth
      Events:
        pusherAuthEvent:
          Type: Api
          Properties:
            Path: /pusherauth
            Method: post 
```

Enter fullscreen mode Exit fullscreen mode

这是我的简单验证码现在的样子:

```
// Loading and initializing Pusher server API
// only on a cold start
const Pusher = require("pusher");
const pusher = new Pusher({
  appId: process.env.APP_ID,
  key: process.env.APP_KEY,
  secret: process.env.SECRET_KEY
});

exports.handler = (event, context, callback) => {

  // the Pusher client doesn't send JSON, so I just crack it open manually
  const body = event.body.split("&");
  const socket_id = body[0].split("=")[1];
  const channel_name = body[1].split("=")[1];

  // some data Pusher will distribute to the clients on channel subscription
  const presenceData = {
    user_id: socket_id,
    user_info: {
      name: "Player " + Math.random()
    }
  };

  // checking if this is all good and well, hurr
  const auth = pusher.authenticate(socket_id, channel_name, presenceData);

  // API-Gateway wants its response it that format
  const response = {
    statusCode: 200,
    body: JSON.stringify(auth)
  };

  callback(null, response);
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   给客户端一个加入的通道，稍后这将获得更多的逻辑来检查哪些游戏是开放的，未满的，等待玩家等。

### 前端

这里没发生什么。得到一个 Bootstrap 主题，加载 Pusher 客户端并连接。

整个事情都在 Github 页面上托管。

## 问题

*   AWS SAM 的 CORS 设置似乎是一个巨大的痛苦，所以我到处使用[CORS](https://addons.mozilla.org/de/firefox/addon/cors-everywhere/)直到我得到这个修复。

*   需要对 Lambda 函数进行打包。目前我必须在打包前将`node_modules`目录复制到每个函数目录中。如果我可以简单地将它安装在`back-end`目录中，并且它会自动被复制，那就太酷了。

*   还需要一些辅助功能。目前整个 Lambda 体验都是非常基础的。