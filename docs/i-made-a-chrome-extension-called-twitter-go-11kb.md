# 我做了一个 chrome 扩展叫“Twitter Go”。

> 原文：<https://dev.to/hariharan_dev/i-made-a-chrome-extension-called-twitter-go-11kb>

在办公室劳累了一天后，当你正要出门回家时，你发现已经下了一个小时的倾盆大雨。你想起来了吗？我曾多次处于这种与外界隔绝的境地。为了避免这样的事情发生，并与世界保持联系，我开发了一个 Chrome 扩展，每当我打开一个新标签，它就会显示我的 Twitter feed 中最新的 20 条推文。听起来很酷吧？现在让我们来看看它的编码部分。所以我有了这个想法之后，就在找 chrome 扩展的教程。我发现这个由希夫曼创作的[播放列表](https://www.youtube.com/watch?v=hkOTAmmuv_4&list=PLRqwX-V7Uu6bL9VOMT65ahNEri9uqLWfS)非常有用。最初，我用模拟数据构建了一个虚拟扩展来完成前端部分。现在到了棘手的部分。为了从我的 Twitter 时间表中获取推文，我必须在 Twitter 开发者社区上用我的 Twitter 帐户设置一个应用程序。为此，我再次关注了由希夫曼教授的推特机器人教程。在获得了访问我的 Twitter 提要的 Twitter 凭证之后，现在我必须用我的凭证向 Twitter APIs 发出 HTTP 请求。为了解决访问我的 Twitter 的所有问题，我使用了 Shiffman 在 Twitter bot 教程中使用的这个 [Twit](https://github.com/ttezel/twit) 库。因此，在用我的凭证插入 Twit 并用真实数据的扩展设置它之后，当我在浏览器上尝试它时，Twit 无法检索数据，但当我在我的 node js 服务器上运行它时，它能够检索数据。所以经过一番挖掘，我发现[Twitter API 不支持 WebApps](https://stackoverflow.com/questions/35879943/twitter-api-authorization-fails-cors-preflight-in-browser) :(。我做的一个快速解决办法是，在我为另一个玩具项目制作的 node js 服务器上为我的 Twitter feed 设置一个 API。因此，为了完成这个扩展，我必须在后端设置一个 API 来从 Twitter 检索推文，并访问扩展中的 API 来显示推文。

这里是 [Github 链接](https://github.com/hariharan-dev/twitter-go-extension)到扩展。