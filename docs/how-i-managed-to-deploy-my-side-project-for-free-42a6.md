# 我如何设法免费部署我的副业项目

> 原文：<https://dev.to/binygal/how-i-managed-to-deploy-my-side-project-for-free-42a6>

[![](img/cdc708c1deb5b2aa3a31e9fef1c0b0e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nxVaC2FN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aq0g_AzJdxZgIOGyLuOlH8A.jpeg) 

<figcaption>身边项目都牛逼</figcaption>

在我最近一篇关于从做兼职项目中得到的教训的帖子之后，我被要求帮助一些人做他们的兼职项目。我经常被问到的一个问题是——“我怎么能只玩代码就免费发布我的副业项目”。我们中的许多人正在建设愚蠢的副业项目([就像这个](http://www.mypopchallenge.xyz))，我们不想把钱花在托管上。我认为你应该向全世界发布你的作品，所以我想出了这个免费的部署客户端-服务器 web 应用程序的指南。

### 都是名字的问题

[https://medium . com/media/0 c 65 a 3800 bace 6 e 2 a 0 AE 1a 58 e 28776 EC/href](https://medium.com/media/0c65a3800bace6e2a0ae1a58e28776ec/href)

想一想下面的情况。你从一个聚会回来，告诉你的室友关于你在那里遇到的他的朋友。你可以说“他很高，棕色眼睛，留着短胡须……”这可能要花几个小时……如果说“我在聚会上见过你的朋友鲍勃”不是更快吗？人类的大脑是为名字而生的。我们给事物命名，然后给人、地方和产品起别名。这就是为什么你想拥有一个域名而不是给别人你的 ip 地址(而且你必须承认，无论如何看到 ip 地址的链接都是可疑的)。你可以从 http://www.dot.tk/的免费域名开始，之后，当你的副业开始获得一些流量，甚至可能带来一些钱，你可以升级到付费域名。

### 静态资源需要静态托管

托管一个静态网站应该很容易。你应该能够把你的文件扔到云中，把一个域名指向那个地方，让其他人试用你的 webapp。Netlify 正是一个这样做的地方！他们的免费计划相当不错，允许您使用自定义域、SSL 和配置 Git repo 进行自动部署。它非常简单易用。你应该检查一下他们的[连续部署](https://www.netlify.com/docs/continuous-deployment/)。

### 服务器部署

[![](img/40088226921f3064fdcf23bb9d3921f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---vIhkl5---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A11DoUz1iZt4Fs6R_5SB_NA.jpeg)

虽然静态资源易于部署(并且托管成本低廉)，但服务器却完全不同。当没有人使用你的应用时，你的静态资源什么也不做，但是你的服务器保持运行。这就是为什么免费服务器解决方案很少且有限的原因。你真幸运——据我所知，有几个免费的服务器解决方案。

[Heroku](https://www.heroku.com/) 给你一个免费服务器。您可以拥有自己的自定义域，但是服务器会在 30 分钟不活动后进入睡眠状态(唤醒睡眠中的服务器需要时间),并且您的计算时间会受到限制。

[now.sh](http://now.sh) 给你无限的时间，但你不能有自己的自定义域——这意味着每次部署你都会有新的 url 指向你的服务器的新版本，要知道 now.sh 也会开源你的代码。

对于我的副业项目，我决定用 now.sh，但是我做了一些事情来让我的体验更好。

### 如何隐藏代码中的敏感部分

好，所以我没有隐藏我的部分代码。我没有尝试这样做，我不确定你现在是否允许这样做。即使你找到了方法。我在说我的密码。我可以开源我的代码，但是我不希望我的数据库连接字符串、我的产品电子邮件地址和它的密码被公开。解决方案是环境变量。在启动应用程序时传递这些密码，而不是将它们硬编码是一个你应该经常做的好习惯，但当涉及到开源时，这甚至更为关键。现在支持通过 it cli 工具传递环境变量，您需要使用这种能力来保护您的数据安全。

### 如何克服不断变化的服务器 url

正如我之前提到的，now.sh 在其免费服务中不支持自定义 URL。每次部署新版本的服务器时，您的服务器 url 都会更改，因此您需要相应地更改客户端。这样做的第一步是在客户机中有一个地方设置服务器 url。我倾向于使用一个公开服务器 url 的 config.js 文件。

```
const serverUrl = 'https://myserver.com';
const config = {
 SERVER\_URL: process.env.NODE\_ENV === "development" ? 
 "http://localhost:3000" : serverUrl 
};

export default config; 
```

正如您在上面的代码中所看到的，这对于改变环境是一个很好的实践，但是有一个地方来声明服务器 url 也是非常好的，所以以后很容易改变它。

现在我写了一个脚本来部署。我的剧本有三个部分。它会将服务器部署到 now.sh，它会获取 now 将发送回来的 url，然后它会更改 config.js 文件以指向新的服务器 url，最后它会将更改推送到我的 deploy 分支，这样我的客户端就会得到更新。我已经将这个脚本开源为一个 npm 包，你可以在这里[看到它](https://github.com/binygal/nownet-deploy)。(自述文件包含如何正确使用和配置该脚本的说明)。

我希望我的帖子能帮助你部署你的副业项目，我很想听听你如何使用我的脚本，或者在评论或 Twitter 上听听你的副业项目是什么。如果你对剧本或任何东西有任何问题，不要犹豫问我。我的推特 DM 箱开了。一如既往，您可以关注我，了解我在编程世界的下一次冒险。