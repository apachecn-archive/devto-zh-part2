# 启动 Clix: ESLint & Winning with Pusher 应用程序状态

> 原文：<https://dev.to/kayis/startup-clix-eslint--winning-with-pusher-application-state--923>

[昨天](https://dev.to/kayis/startup-clix-cleanup--fighting-with-pusher-application-state-2ilc)我打了，今天我赢了！

今天是关于运行跨站点请求和修复我的 Pusher 应用程序状态问题。

## CORS

不知何故，我无法让 **CORS** 和 [AWS 山姆](https://github.com/awslabs/serverless-application-model)一起工作。它似乎仍然需要大摇大摆地工作，即使他们在几个版本前的[新闻稿](https://github.com/awslabs/serverless-application-model/releases/tag/1.4.0)中写道，CORS 是固定的。我的印象是固定意味着不再需要招摇了，哈哈。

好了，我没时间在这里浪费了，我现在用的是 **JSONP** 。它只能做 GET 请求，但我认为这已经足够了，我不打算在一个请求中发送太多数据。

在前端，我使用 [fetch-jsonp](https://github.com/camsong/fetch-jsonp) 和 Pushers [JSONP transport](https://pusher.com/docs/authenticating_users#jsonp_auth_endpoints) 来处理 auth 请求。

在后端，我扩展了我小小的 *kappa* 库，将主体封装在一个 jsonp 回调函数中，如果在`queryStringParameters`中存在的话。

```
exports.handler = (event, context, callback) =>
  lambda(event, context)
    .then(r => {
      if (r) {
        if (r.body) r.body = JSON.stringify(r.body);

        const jsonpCallback = event.queryStringParameters.callback;
        if (jsonpCallback) r.body = `${jsonpCallback}(${r.body})`;
      }

      callback(null, r);
    })
.catch(callback); 
```

Enter fullscreen mode Exit fullscreen mode

因此，现在整个事情没有浏览器扩展也能工作了，耶！

## 推杆应用状态

像每一个好的开发者一样，我真的不知道为什么它现在还能工作。:D

玩笑归玩笑，我传入`pusher.get()`的回调得到了三个参数，`error`、`request`和`response`。

我总是试图记录`error`来找出发生了什么，但是 Pusher 在`response.body`中有一个错误描述。嗯，当我终于找到这些，修复只是一个使用正确的问题`param`

## ESLint

我已经在用[更漂亮的](https://prettier.io/)，还加了 [ESLint](https://eslint.org/) ，因为

*打包- >部署- >出错- >打开 CloudWatch*

只是没有削减它了，:D

90%的错误是变量未定义或错误的`const`覆盖。

我是一个前端人员，切换到浏览器并刷新以查看错误的痛苦可能还没有大到足以使用 ESLint，直到现在。

如果我以后遇到更多的`undefined`错误，也许我会添加[流](https://flow.org/)。

## 通道加入逻辑

我有一个基本的频道加入逻辑。

如果不存在通道或者所有通道都已满，则创建一个新的通道 ID 并发送给客户端，否则客户端将获得一个现有的通道 ID。目前每队只允许两名球员，这使得测试更加简单:)

同样的事情也在`/pusherauth`端点进行检查，所以如果你有一个频道，但在你加入之前它已经满了，你不能，哈哈。

这是一个竞赛条件，如果有人有好的解决方案:让我听听。

## 接下来

我将研究 AWS 步骤函数来模拟服务器上的游戏状态。虽然我已经检查过它有我需要的所有功能，现在我要学习 API。