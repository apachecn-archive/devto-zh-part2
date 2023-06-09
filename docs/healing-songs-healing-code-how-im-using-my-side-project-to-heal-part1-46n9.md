# 治愈的歌曲，治愈的代码。我如何利用我的兼职项目来疗伤(第一部分)

> 原文：<https://dev.to/gluseppe/healing-songs-healing-code-how-im-using-my-side-project-to-heal-part1-46n9>

上个月是糟糕的一个月。我不会透露细节，但这是一个糟糕的月份。我和我的一个朋友谈到了这件事，就在我收到他的消息的第二天

> “嘿，伙计，这是一首让你感觉很好的歌”

谢谢你法比奥。他是对的。好吧，没持续多久，但是我感觉好了一段时间。我强烈相信音乐(和编码)的治愈特性；)
[他给我发了这首歌](https://open.spotify.com/track/3zcYin7vFTtrjBFQN4yP1J?si=vSglktL-TGSCf8LPJ-JQ7A)如果你感兴趣的话。不要问我为什么会成功。刚刚成功了。但是我想要更多。

所以我想

> “我应该得到更多这样的歌曲”

我想收集尽可能多的治疗歌曲。当然，我也不想通过 iMessage 或 Whatsapp 收到它们。我希望潜在的任何人都可以联系到我，给我发送这些歌曲之一，然后与每个人分享播放列表。

所以，我打开了 vs-code，创建了一个新项目，目标是拥有最简单的代码

*   在 spotify 目录中查找歌曲
*   储存被选中的，也许还附有信息

由于我现在主要使用 Python，我将我的项目设置如下

*   它使用 Flask 来展示搜索功能，并使用一个分支来接收所选的歌曲
*   它使用 pandas 在一个. csv 文件中加载和写入列表
*   它使用语义用户界面和一个简单的。js 文件来处理前端部分:一种自动完成表单和一条感谢消息，以防您发送歌曲

在编码的第一个晚上之后，我能够使用本地服务器与 spotify APIs 进行交互，并从查询字符串中获得跟踪结果。这已经让我痊愈了。我知道这只是一个小项目中的一小步，但它确实有效。它正在工作。这一切如何继续将在本“日记”的下一部分。下一部分将介绍如何设置 flask，以及如何用 python 与 spotify apis 进行交互。

同时，如果你愿意，你已经可以在这里把你的治愈歌曲发给我了:)
[https://healify.it](https://healify.it)

并将您的意见发送给我，以改进页面
谢谢< 3