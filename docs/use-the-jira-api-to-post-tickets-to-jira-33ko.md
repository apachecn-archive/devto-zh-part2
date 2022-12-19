# 使用 JIRA API 发布到 JIRA 的机票

> 原文：<https://dev.to/jennifer/use-the-jira-api-to-post-tickets-to-jira-33ko>

*原贴于[jmkoni.com](https://www.jenniferkonikowski.com/blog/2018/9/20/use-the-jira-api-to-post-tickets-to-jira)T3】*

不久前，我开发了这个[超级基本的 Sinatra 应用程序](https://github.com/jmkoni/sinatra-jira-app)来发布去 JIRA 的门票。下面是一个用例:你的公司/团队中有一些非技术人员，他们需要能够给 JIRA 添加 bug。然而，他们没有将正确的信息输入到票证中。这个超级基础的 app 来了。要让它运行，你只需要更新。env 与您的 JIRA 用户名，密码和项目密钥。但是，我建议将其改为使用 [OAuth](https://developer.atlassian.com/server/jira/platform/oauth/) 。现在，这个表格非常简单，如果你决定使用它，我强烈建议你更新它来询问你想要的任何信息。只是别忘了更新 sinatra_jira.rb 里的 JSON！这个应用程序是完全开源的——出于任何原因，可以随意复制它的任何部分或全部。让我们深入一点，快速概述一下[辛纳特拉](http://sinatrarb.com/)是如何运作的。

首先，gem 文件是最小的。最重要的是我使用了 [dotenv](https://github.com/bkeepers/dotenv) ，这是一个非常有用的工具，可以帮助管理环境变量。环境文件。除此之外，rubocop、sinatra 和我们都在使用瘦服务器。