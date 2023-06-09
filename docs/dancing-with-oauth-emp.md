# 与 OAuth 共舞:循序渐进指南

> 原文：<https://dev.to/anabella/dancing-with-oauth-emp>

# 与 OAuth 共舞:循序渐进的课程

大多数时候，我试图学习新的东西并付诸实践，我很快就开始觉得自己迷失在无数的舞蹈动作中。我拼命想找到正确的做事方式，但并不真正明白发生了什么，也不明白我怎么会出现在房间的错误一边…

只是试着做些事情直到成功。

也许是因为我的学习过程的方式，或者也许指南和教程是针对更有经验或技术的人。但是，当我思考完这个问题后，我总是觉得应该有一个简单的指南来理解关键概念，并使它们更容易应用到项目中。

所以这一次，我决定停止许愿，用我最后学到的东西，自己去创造。

那就是 OAuth 2.0。

## OAuth 是什么？

先说基础:OAuth 代表**开放授权**。这是一个应用程序或网站可以从另一个网站访问私人用户数据的过程。

这另一个网站通常只作为可信的身份提供者。它向请求应用程序提供一些关于你的基本信息，以便应用程序可以创建一个档案。这样，你就不必填写无聊的注册表格和处理另一个密码📋

你已经使用这个至少无数次了，事实上你每次点击“用脸书/谷歌/ GitHub / …”的时候都会用到它。接下来，你会看到一个同意屏幕，上面显示你允许*that-hot-new-app.com*阅读(有时，写下)你的(比如说)脸书个人资料中的哪些信息。之后，由于 that-hot-new-app.com 的*信任脸书提供的身份，他们可以使用收到的数据在他们的数据库上为你创建一个档案。*

that-hot-new-app.com 和脸书的交流通常到此结束。这就是为什么如果你在脸书改变你的个人资料图片，它不会在整个互联网上改变。他们只是从来没有回到脸书，并要求更新数据。

### 当木琴节奏开始演奏时…

构建这种机制还有另一个目的，一个更有潜力的目的:使用身份提供者作为服务提供者(以一种持续的方式)。这意味着定期与 it 部门沟通，为您的用户提供增强的功能。

一个很好的例子是[relieve](https://www.relive.cc/)，这是一种与不同运动跟踪应用程序连接的服务，可以创建您跑步或骑行的地球视图视频。每次你完成一项活动，review 都会提示你创建一个视频。如果你同意，他们会处理它，并通知你什么时候可以在社交媒体上炫耀…我的意思是分享🏅

这两种用法在技术上真的没有区别。这就是为什么你应该谨慎使用你的社交媒体或谷歌/Gmail 账户登录的原因。

这听起来可能很可怕，但真的没什么好害怕的。请记住，您正在授权*that-hot-new-app.com*访问您的信息，这些信息在同意屏幕上有详细说明，可能会重复出现。注意你授予的权限，并确保当你不再信任别人时知道如何禁用它们。

例如，如果你正在使用你的谷歌帐户访问 that-hot-new-app.com，但不想再允许，只需进入你的[谷歌帐户设置](https://myaccount.google.com/security#connectedapps)并禁用他们的访问。

所有主要的身份提供者都对此提供控制。

## 上街前打扮一下

在你登陆*that-hot-new-app.com*甚至点击“用你的 FavoriteIdentityProvider 登录”之前，有人——可能是开发者——必须在提供商的网站上创建一个应用程序。

这是一种注册*that-hot-new-app.com*的方式，以便以后，提供商知道谁在请求私人数据。

在这一步，开发人员将设置一些关于应用程序的信息，如应用程序的名称或网站，以及最重要的重定向 URI。提供商(如谷歌或脸书)将使用这个来联系请求应用程序，并告诉他们用户说是💍

[![I promise you won't have to write it by hand, we pride ourselves on our paperlessness.](img/6fa9c3fa2840bf68f06beea80f90b137.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7fSA7mCQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kh9u69ycgjtw6ws12vae.jpg)

一旦应用程序注册，提供商将给予 that-hot-new-app.com 的*一个`clientId`和一个`clientSecret`用于他们之间的通信。它们的工作有点像注册应用程序的用户名和密码。*

[![You'll get the clientID and clientSecret right after you click on Save application](img/fb55b31221bf7962b053a84787786a98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vaM-SvOZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9035kpogozrtpzz8qkcd.jpg)

将应用程序信息(重定向 uri、clientId，尤其是 clientSecret)保存在安全的位置，不要与陌生人共享，这一点非常重要。如果有人获得访问权限，他们可以代表你向提供商请求私人用户数据，然后利用这些数据作恶！

我们不想那样。

### 手放在腰上或肩上

除了设置所有这些事情之外，我们的开发人员还必须找出提供商允许访问什么类型的数据，以及这些数据是如何分段的。

这些“段”被称为*范围*，它们定义了访问权限，通常分为读/写类别。

例如，that-hot-new-app.com 可以请求`profile:read`和`contacts:read`范围。这意味着他们可以读取提供商分配给“个人资料”和“联系人”部分的任何信息，但他们不能更改这些信息。其他内容将无法访问，例如您的帖子或您喜欢的内容。

好吧，现在为了让事情变得简单，让我们假设*that-hot-new-app.com*是一个与 [Typeform](http://typeform.com/) 整合的网站，这是一个创建漂亮和智能表单的服务，也是我工作的公司🤷。你肯定想了解现在最热门的事情，而且要快，所以在他们的网站上，你点击“用 Typeform 登录”,就可以直接开始行动了。下一步是什么？

这里有一个自制的，有机的，不含胆固醇的图表，可以作为整个事情的地图。这看起来可能有点复杂，但是不要担心，接下来我们将检查每一步。

[![Colorful notes bring joy to my heart](img/6d5dc9ccc472eab3ae9bb040ae936772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E2GjynCH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/evjdy0cctvtupj6gjnwd.png)

## 授权:OAuth 舞蹈的第一步

所以，你采取主动，点击“用 Typeform 连接”。在这里，*that-hot-new-app.com*(从现在开始是 THNA，因为我已经厌倦了写破折号分隔的单词)将把您发送到 Typeform 的 authorize 端点(/oauth/authorize)并提供:

*   他们的客户端 Id(记住，那是 THNA 的用户名)
*   他们期望的范围(或访问权限)
*   并且他们再次重定向 URI(当我们设置整个事情时，Typeform 已经知道了，但是我们再次发送它作为额外的安全层)

该 URL 将类似于:

```
https://api.typeform.com/oauth/authorize?client_id=yourClientId&scope=accounts:read+forms:read+results:read 
```

Enter fullscreen mode Exit fullscreen mode

Typeform 将使用此信息生成一个同意屏幕，您可以在其中查看您授权 THNA 查看和执行的内容。

[![Consent screen generation flow](img/d61605a9c4334cab4840a543cdd77bad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8FNcNC6R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g5x0f32xi7ekrlsjycvi.jpeg)

一旦你已经**通读了你同意**的内容，并愉快地点击“允许”，Typeform 将把你发送到带有临时的重定向 URI，就像这样:

```
https://that-hot-new-app.com/auth/redirect?code=xxxXXXxxxXXXxxx 
```

Enter fullscreen mode Exit fullscreen mode

## Token:到 tangOAuth 需要 2 个💃

所有这些来来回回的感觉就像有人带你去跳探戈，对吗？

OAuth 舞蹈的第二步是当 THNA 收到代码，并将其交换为 OAuth 令牌。

因此，在这个阶段，THNA 有一个临时代码，代表他们要求的一切，可以把它看作是一种加密的方式说:

> “嘿，还记得我吗？我是 THNA，这个用户说我可以看看他们的形式和主题。当你准备好了，请给我一个令牌，这个重定向 URI。”

作为知道所有步骤并且没有冒犯任何人的回报，THNA 将得到一个闪亮的 OAuth 令牌✨，它可以用它来代表用户(就是你！)并在他们需要时随时索要授权数据。

## 和我在一起，和我一起摇摆

从现在开始，在 THNA 为你做的每一个输入表单的请求中，他们必须包含一个带有访问令牌的授权头。有了它，Typeform(或任何其他提供者)可以识别:

*   谁在要求数据(在这种情况下，THNA)
*   关于谁的数据(你！)
*   还要确保他们有正确的权限访问这些数据(只有您同意的数据)。

## 准备舞池👯

所以现在你知道了 OAuth 舞蹈技术的所有步骤和旋转，你应该准备好创造你自己的编舞，我的意思是，整合，让互联网成为一个更好的地方。

* *你真实的画*

* * *

如果你正在寻找更深入的 OAuth 2.0 实践经验，我强烈推荐网络忍者的 PassportJS 教程。它涵盖了你设置这个(在 NodeJS 应用程序中)需要知道的所有基础知识，理论/实践比例非常平衡。这是我理解这一切的转折点。

[https://www.youtube.com/embed/sakQbeRjgwg](https://www.youtube.com/embed/sakQbeRjgwg)