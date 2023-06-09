# GitHub 页面上的启动 CliX: DynamoDB & React 前端

> 原文：<https://dev.to/kayis/startup-clix-dynamodb--react-front-end-on-github-pages-18ol>

在这个周末，我在 React 中设置了一个[前端，并用 DynamoDB 添加了一些数据存储功能。](https://kay-is.github.io/startup-clix/)

## 前端

我在 [GitHub 页面](https://pages.github.com/)上使用了 [create-react-app](https://github.com/facebook/create-react-app) 和 [Bootstrap](https://getbootstrap.com/) 来快速完成工作。

我印象深刻。大约 10 分钟就搞定了一切。

1.  安装`create-react-app`
2.  初始化新的 React 应用程序
3.  修改生成的`package.json`,使其适合 GitHub 页面
4.  添加引导 HTML 模板
5.  运行`build`命令
6.  从`.gitignore`中删除`build`目录
7.  提交更改
8.  推送至 GitHub

GitHub 页面的`package.json`:

```
{  "name":  "startup-clix",  "version":  "0.1.0",  "private":  true,  "dependencies":  {  "react":  "^16.3.2",  "react-dom":  "^16.3.2",  "react-scripts":  "1.1.4"  },  "homepage":  "https://kay-is.github.io/startup-clix/front-end/build",  "scripts":  {  "start":  "react-scripts start",  "move-html":  "mv build/index.html ../index.html",  "build":  "react-scripts build && npm run move-html",  "test":  "react-scripts test --env=jsdom",  "eject":  "react-scripts eject"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我只是在 GitHub 页面的`homepage`属性中添加了我的`build`目录的 URL。

并在`build`命令后添加了一个`mv`，所以新的`index.html`会出现在根目录中。

这只是第一个版本，一个朋友已经告诉我应该先在手机上做，哈哈。

嗯，应该不是什么大问题，我想。

## DynamoDB

在我发现了 [AWS 步骤函数](https://aws.amazon.com/de/step-functions/)的局限性之后，我必须准备好一个新的数据存储。幸运的是 [AWS SAM](https://github.com/awslabs/serverless-application-model) 允许定义[简单的 DynamoDB 表](https://github.com/awslabs/serverless-application-model/blob/develop/versions/2016-10-31.md#awsserverlesssimpletable)。

通过 DynamoDB 的[条件写入](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithItems.html#WorkingWithItems.ConditionalUpdate),我可以更好地同步玩家请求。

新的游戏线程将如下运行:

1.  玩家连接存储在 DynamoDB 表中
2.  当足够多的玩家加入时，启动状态机执行
3.  然后通过 Pusher 发送事件(游戏:开始，游戏:结束，回合:开始，回合:结束等。)给客户。
4.  玩家通过 HTTP 向 Lambda 函数发送他们的成品，并存储起来
5.  每轮结束时，状态机 Lambda 函数会检查玩家是否输了
6.  如果剩下一个或更少的玩家，游戏结束，如果剩下更多的玩家，这一轮重复

## 接下来

将前端转换为移动。

让用户发布自己的产品。

正确设置状态机，以便按时分发游戏事件。