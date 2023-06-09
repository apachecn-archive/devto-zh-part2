# 用 Node.js 和 Code.xyz 构建一个三步翻译文本的电报机器人

> 原文：<https://dev.to/janeth/build-a-telegram-bot-that-translates-text-in-3-steps-with-nodejs-and-codexyz---by-steve-meyer-4ic3>

[**用 Node.js 和 Code.xyz 构建一个电报机器人，分三步翻译文本——作者史蒂夫·梅耶**](https://medium.com/@notoriaga/build-a-telegram-bot-that-translates-text-in-3-steps-with-node-js-and-code-xyz-fa9d6309b659)

[![](img/2dc73c0373894c84791aea9f97a775c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Md9jpioJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AXydtv7uLinvrvcy_sTpcng.png) 
电报是以隐私为重点的即时通讯和语音服务。碰巧的是，他们也有一个非常漂亮的界面来创建机器人。这一切都是通过与另一个名为“机器人父亲”的机器人对话，在应用程序中完成的。

本教程将带你创建一个可以将文本翻译成几种不同语言的电报机器人。翻译工作将由 IBM Watson 的语言翻译人员负责。Telegram bot API 和 Watson 将由标准库整合在一起。

如果您不熟悉标准库，我们结合了可扩展的计算层，以及自动生成的文档、身份验证、计费等。我们努力让任何人都能在几秒钟内将简单的 JavaScript 函数转化为生产就绪的 API。在我们的在线编辑器 Code.xyz 的帮助下，您可以在本地机器上进行零设置或安装，就可以发布到标准库。

**预先需要的东西**
1x 电报账户
≈5x 分钟

第一步:咨询父亲
今天是父亲女儿结婚的日子。你有机会要求一件礼物，他当然不会拒绝。你选择要求你自己的电报机器人。无论你使用 Telegram web 应用程序，还是手机各自的客户端，都可以进入搜索栏，输入“botfather”。可能有几个选项，请确保选择带有“已验证”复选标记的选项。

冒牌货！你想要蓝色支票的那个。
当你的对话开始时，输入`/newbot`，机器人父亲会询问你的名字。这不是机器人的用户名，所以它不必是唯一的。我选择了`notoriaga_test_bot.`接下来，僵尸父亲会询问用户名(即`@BotFather`)。人们会这样称呼你的机器人，所以它必须是独一无二的。我还是选择了`notoriaga_test_bot,`，但是你必须选择不同的。在这一步之后，机器人父亲将返回一个用来控制你的机器人的令牌。把它抄下来，我们马上就要用到。

现在我们需要为机器人启用内联模式。这使得人们可以在任何聊天中输入`@<bot-name>`来使用你的机器人，而不是像我们使用机器人父亲那样打开聊天。键入`/setinline`，然后点击你刚刚创建的机器人。机器人父亲会要求一些占位符文本。我选择了`Some text to translate…`,它是这样展示的—

[![](img/3fcd8d6aab4edf3c1d24706f29a14827.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lhmOcaPU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AHnJ9G40FDE4Qc-QcXh4TkQ.png)

**第二步:用标准库添加一些基本功能**

[![](img/23abd9f998b1a832a2ad10d529d0aac8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IhaWBwkm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AAsgq23ehbpI4ToT0lI80cw.png)
code . XYZ——标准库的免费 API 在线编辑器

在开始翻译之前，让我们先看一个基本的“hello world”示例。请访问 Code.xyz，这是标准库的在线代码编辑器。它预装了(几乎)让你的机器人运行所需的一切。您可以通过该链接或点击登录页面上的“特色 API 源代码”选项卡来找到代码。

加载后，打开`env.json`文件。你会看到四个不同的变量。现在，我们只关心前面的令牌`TELEGRAM_BOT_TOKEN.`。如果你因为某种原因找不到它，你可以在和机器人爸爸说话的时候用`/token`做一个新的。无论哪种方式，将令牌放入您的`env.json.`

现在转到`function/setup.js.`这个函数在运行时，将项目中的`functions/__main__.js`文件注册为所有 bot 内联查询的接收方。点击 run 后，您应该会收到 Telegram 的响应，表示一切正常—

[![](img/313e4e53ce0a3266a8e7990a00b0a77a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tw8Rhb87--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AbXV_YMz6VikJeTplHPZy4w.png)

现在回到电报给你的机器人一个尝试。键入`@<bot_name>` hello world，过一会儿会出现一个弹出窗口，显示 echo——

[![](img/78bc138a11cac06d1da6a1bb5a62a3ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h8olmjxO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AsQTQLUvoAD9aFpvFbd0ADw.png)

点击弹出菜单，然后—

[![](img/94790e3204ce404e19800497115a595d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UH8RTwKi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2ANKQn_fyJCO_0r543zwecvA.png)

成功！设置好基本功能后，让我们让机器人做一些更有用的事情。

**步骤 3:用 IBM Watson 翻译内联查询**

浏览您的 IBM Bluemix 仪表板(如果您没有帐户，可以创建一个帐户)。在您的仪表板上，点击右上角附近的`Create resource`。从左边的分类侧边栏点击`AI`，然后`Language Translator.`给你的服务起一个名字，然后点击`Create.`，现在你的服务页面应该有两个凭证了。第一种是`API Key.`简单地把它复制到你的`env.json`中作为`WATSON_TRANSLATE_KEY.`现在对于`Url,`你只对主机感兴趣，也就是说`https://`和`/language-translator/api.`之间的部分把主机复制到你的`env.json`中作为`WATSON_TRANSLATE_HOST.`

[![](img/56a70bc433d245851528e0af9a8c9894.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wt6sDR4d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AO-vwCnONuEY8AGbtzc5eVg.png) 
完整 env.json

有了这些新的环境变量，回到`functions/setup.js`并再次点击 run。现在，关键时刻到了。回到 Telegram 并输入`@<bot_name> hello-world again.`,这一次你会看到——

[![](img/a78a63a83524dd3b110d065f8607275f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qv82F6ZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AGqXea1i35MPjYPB7G56juw.png)

当然，点击一个就会把翻译输入到聊天中——

[![](img/2dded68ccc198322e17f55a4b7966703.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NBZG6iKc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2A5iCI34jlXdEW-lIWBJkZog.png)

**就这些！**
我希望这篇教程对您展示使用标准库组合不同的服务是多么容易有所帮助。要获得更多关于如何更好地使用标准库的灵感，您可以在这里查看该团队编写的更多指南。如果你有好的想法想要分享，请直接通过电子邮件联系我:[steve@stdlib.com](mailto:steve@stdlib.com)，或者在 Twitter 上关注我和标准图书馆团队。

Steve Meyer 是 Oberlin 学院的应届毕业生，也是标准图书馆的软件工程师。当他不编程的时候，你可以发现他在烤面包，或者玩蜘蛛侠。