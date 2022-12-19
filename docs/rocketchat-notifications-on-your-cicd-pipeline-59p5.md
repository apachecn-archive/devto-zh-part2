# CI/CD 渠道上的 RocketChat 通知

> 原文：<https://dev.to/jadolg/rocketchat-notifications-on-your-cicd-pipeline-59p5>

嘿，你好:)
我是[火箭队的忠实粉丝。聊天](https://rocket.chat/)。我甚至写了[我自己的 REST API 包装器](https://github.com/jadolg/rocketchat_API)和 Python 一起使用，并说服了我公司的每个人从 Slack 迁移过来。由于我在一家软件公司工作，我们很快就开始用它做很多事情。我们甚至有一个机器人在组织中设置提醒:)
我们喜欢我们所做的工作的一点是持续集成。我们是一个非常注重质量的团队，我们喜欢一切都自动化。我想做`git push`并且一切都应该自动开始构建、测试、测量和部署，当然，我想在我出色的聊天应用程序上有通知，这样我的团队甚至不用打开我们的 CI 工具也能知道发生了什么。我知道有很多 CI 工具，但是在我的团队中更流行的是 gitlab-ci。我个人是从 jenkins 换来的，因为我喜欢 gitlab 与 Docker 开箱即用的工作方式，我发现 sintax 更加清晰和漂亮，因此我将使用 gitlab-ci 符号来编写我的示例。
因此，从技术上讲，你可以只使用 curl 向 RocketChat 推送通知，但这可能会很冗长。首先，您必须登录到您的聊天服务器，保存令牌，然后发布您想要的消息，例如，全部使用 *curl* 。
根据 REST API 文档，您将像这样登录

```
curl http://localhost:3000/api/v1/login \
     -d "username=myusername&password=mypassword" 
```

这将导致这样的响应:

```
{  "status":  "success",  "data":  {  "authToken":  "9HqLlyZOugoStsXCUfD_0YdwnNnunAJF8V47U3QHXSq",  "userId":  "aobEdbYhXfu5hkeqG",  "me":  {  "_id":  "aYjNnig8BEAWeQzMh",  "name":  "Rocket Cat",  "emails":  [  {  "address":  "rocket.cat@rocket.chat",  "verified":  false  }  ],  "status":  "offline",  "statusConnection":  "offline",  "username":  "rocket.cat",  "utcOffset":  -3,  "active":  true,  "roles":  [  "admin"  ],  "settings":  {  "preferences":  {}  }  }  }  } 
```

您需要保存用于发布消息的值 *authToken* 和 *userId* 。
在此之后使用*卷曲*发布消息也不难:

```
curl -H "X-Auth-Token: 9HqLlyZOugoStsXCUfD_0YdwnNnunAJF8V47U3QHXSq" \
     -H "X-User-Id: aobEdbYhXfu5hkeqG" \
     -H "Content-type:application/json" \
     http://localhost:3000/api/v1/chat.postMessage \
     -d '{ "channel": "#general", "text": "This is a test!" }' 
```

用它制作一个脚本应该很容易，但是因为我想练习一些*围棋*，我决定制作[这个应用程序](https://github.com/aleph-engineering/rocketchat-notification)，因为我喜欢*围棋*的方式，它几乎在任何地方都可以工作，这看起来是个好主意，事实也确实如此。
所以现在的想法稍微简单一点:
你只需要下载发布的应用程序(或者自己编译)并按照帮助中所示执行。

```
wget https://github.com/aleph-engineering/rocketchat-notification/releases/download/1.4.1/rocketchat-notification -P /usr/bin/ && chmod +x /usr/bin/rocketchat-notification 
```

和

```
rocketchat-notification -c general -u user -p ${ROCKET_PASSWORD} -s https://myserver.rocket.chat -m "hello from terminal" 
```

很简单，对吧？
现在我们用在一个句子里:)
在我的 gitlab-ci(。gitlab-ci.yml)我会这样做:
首先，让我们把我们想要作为环境变量使用的用户的密码放在 gitlab 上，这样它就不会持久保存在我们的文件上，从而提高我们的安全性:
这是在 CI/CD
[![gitlab_CI](../Images/fc17f58f86cc07177b94349cb23f0feb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vHKp8kQ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kb6mg5y3i3sgzmhilyoc.png) 
下的项目设置中完成的，让我们在`.gitlab-ci.yml`中写入类似这样的内容:

```
stages:
- test

image: docker:stable-dind

test_nitofication:
  stage: test
  before_script:
  - wget https://github.com/aleph-engineering/rocketchat-notification/releases/download/1.4.1/rocketchat-notification -P /usr/bin/ && chmod +x /usr/bin/rocketchat-notification
  script:
  - rocketchat-notification -c general -u gitlab -p ${ROCKET_PASSWORD} -s https://yourserver.rocket.chat -m "hello from CI" 
```

提交并推送您的更改，然后等待通知:)
[![notification](../Images/1444cd49258b7d3a3f7b70240085704f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zFwP1gTe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5puovnncfjl7k44wnfhy.png) 
您想将命令的输出发送到您的聊天吗？没问题。您总是可以运行 rocketchat-notification 来读取标准输入，如下所示:

```
docker ps | rocketchat-notification -c general -u gitlab -p ${ROCKET_PASSWORD} -s https://yourserver.rocket.chat 
```

使用`-code true`标志，您的文本将出现在聊天的代码框中。
就这样。我希望你会觉得这很有趣和实用，并享受你的持续集成带来的乐趣。