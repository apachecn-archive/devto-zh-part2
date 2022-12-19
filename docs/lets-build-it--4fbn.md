# 让我们建造它

> 原文：<https://dev.to/emadmokhtar/lets-build-it--4fbn>

[![idea](../Images/3d5e88e818bdfaa93849adcc7557c898.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BCqcCgTp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.emadmokhtar.com/images/james-pond-185593-unsplash.jpg)

詹姆斯·庞德

# 发现

在多年开发 web 应用程序之后，我发现我的热情是构建 Web APIs。所以为什么我喜欢它，我喜欢它是因为我认为它让你的应用程序有能力与其他应用程序(桌面、移动应用程序等)集成。)、其他设备(亚马逊 Dot、Google Home 等。)、传感器(RaspberryPi、Arduino 等。)，以及机器人(Slack，Telegram，脸书 Messanger 等。).你能感受到 Web API 给你的应用带来的强大力量吗？

# 让我们建造一些东西

在我找到我的激情后，我开始寻找一个我喜欢的项目并不断发展它。发现没有[古兰经 taf seer/翻译](https://en.wikipedia.org/wiki/Tafsir)的 Web API。Quran Tafseer API 能为开发者做什么？如果你想为古兰经开发一个应用程序，并且你想为应用程序用户提供一个 taf seer/翻译，你需要从多个来源收集信息。我发现大多数现有的应用程序都做到了这一点，在此之后，应用程序开发人员将只为他们的应用程序提供这些信息，所以我想到为社区做这项工作，并作为 Web API 免费提供 Tafseer/Translation。

# 阶段

## 第一阶段

我使用 [Django](https://www.djangoproject.com/) 和 [Django REST 框架](http://www.django-rest-framework.org/)构建了模型和 API 端点。

## 第二阶段

我开始搜索我能得到 taf seer/翻译的来源，我找到了两个来源，一个来源提供。txt 文件，第二个不容易获得，但我使用了 [Scrapy](https://scrapy.org/) 来获得他们的 web 应用程序的 Tafseer/Translation。

## 第三阶段

在收集了 Tafseer/Translation 之后，我部署了应用程序，应用程序及其数据库部署在 Heroku 上，但是两天后 Heroku 给我发了一封电子邮件，说我达到了自由层的最大记录数，所以我将数据库移到了 AWS RDS。

# 未来

我未来的计划是让这个 Web API 快速、易用，并被全球许多开发人员使用。

# 帮助

如果您想提供帮助，请在您的社区中传播这些信息并给我您的反馈，如果您想为该项目做出贡献，请访问[如何做出贡献指南](https://github.com/emadmokhtar/tafseer_api#how-to-contribute-and-help-the-project)