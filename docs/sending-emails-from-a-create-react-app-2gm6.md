# 从创建-反应-应用程序发送电子邮件

> 原文：<https://dev.to/pepesenaris/sending-emails-from-a-create-react-app-2gm6>

<figure>[![](../Images/3b7f7bbfb5badb1725d39587035c84f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jn17k5tK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIiQuFZskyWATRJBMXGVXkQ.jpeg) 

<figcaption>【古巴哈瓦那摩洛三大巫师国王城堡】</figcaption>

</figure>

几天前，我们在[创新](http://ingenious.agency)内部开发的一个应用中增加了一个新的**通过电子邮件**邀请人们的功能。该应用程序包括一个带有协作文本编辑器的板和一些卡片，这些卡片补充了产品设计[咨询](http://ingsw.com/agency/)中使用的行为洞察方法。从技术角度来说，该应用程序是用 React 构建的，用 [create-react-app](https://github.com/facebook/create-react-app) 引导，Firebase 作为后端，托管在 Heroku 上。

我们需要通过发送带有版块 URL 的电子邮件来邀请人们作为版块的编辑。为了简单起见，我们首先尝试了 email js T1，这是一种无需关注任何服务器代码就可以发送电子邮件的服务。EmailJS 本来是一个很好的选择，但是我们需要对如何生成电子邮件有更多的控制，例如，不可能给电子邮件的 HTML 版本添加自定义字体。

作为中断最少的途径，我们选择建立一个小型 Express(节点)后端来服务我们的 React 应用程序，并发布一个负责发送电子邮件的端点。我已经找到了几个关于这个主题的教程，但大多数都已经过时或不完整，所以想分享一下我们的解决方案，以防它对你也有好处。

如果您喜欢深入研究代码，请查看最后的演示链接。

第一项任务是通过以下步骤将我们的客户端专用 React 项目转变为完整的 express 应用程序:

1.  从应用中移除 create-react-app-buildpack，并设置默认的 [heroku/nodejs buildpack](https://devcenter.heroku.com/articles/nodejs-support#activation) 。
2.  将根 React app 文件移动到子目录中，例如 *react-ui* 。
3.  复制 *server/index.js* 、 *package.json* ，以及 _。gitignore_ files 来自 buildpack [示例 repo](https://github.com/mars/heroku-cra-node#switching-from-create-react-app-buildpack) 的项目根目录。
4.  提交并推进到 Heroku 原点。

为了保持 React SPA 正常运行，我们需要使用 static express 中间件来服务它的所有资产。之后，我们可以处理任何请求来执行定制动作，比如发送电子邮件，使用常见的 **get(…)** 、 **post(…)** 快速处理程序。最后，我们可以将任何非 API 请求重新路由到 SPA 入口点，这样客户端路由就可以继续按预期工作。检查下面的代码: