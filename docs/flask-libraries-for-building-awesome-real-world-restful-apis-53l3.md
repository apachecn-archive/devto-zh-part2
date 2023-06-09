# Flask 库用于构建令人敬畏的真实世界 RESTful APIs

> 原文：<https://dev.to/nickjj/flask-libraries-for-building-awesome-real-world-restful-apis-53l3>

**本文最初发布于 2018 年 3 月 27 日:[https://nickjanetakis . com/blog/flask-libraries-for-building-awesome-real-world-restful-APIs](https://nickjanetakis.com/blog/flask-libraries-for-building-awesome-real-world-restful-apis)**

* * *

当谈到用 Flask 构建 RESTful APIs 时，我们有很多选择可以使用哪些库，但我认为在我们开始选择库之前，我们需要分解一个典型的 API 需要做什么。

##### 归根结底，大多数 web APIs 都需要处理:

*   连接您的 API 端点
*   序列化和反序列化数据
*   验证用户输入
*   用户认证

使用 Flask 的部分吸引力在于，您可以灵活地挑选一些库来帮助解决您的具体问题。这很好，因为有一堆松散耦合的库可以很好地做一件事，一旦你知道了基础知识，你就可以很快地把它们粘在一起。

因此，当谈到用 Flask 构建 API 时，我并不太想找一个单独的库来做所有的事情，因为有可能一个库可以很好地完成大多数事情，但有几件事情做得很差。

本文不是要抨击各种 Flask API 库，也不是要号召任何人以不同的方式做事。这篇文章是关于组装一些高质量/维护良好的库，用 Flask 构建令人敬畏的真实世界 API 驱动的应用程序。

## 构建 API 的开发者特性列表

如果你问我在构建一个 API 时想在代码级别做什么，我会给你一个类似这样的列表:

*   明确表达时你可以合理要求的最少的样板文件
*   能够轻松地为 CRUD 操作设置 RESTful 端点
*   必要时快速轻松地添加非 RESTful 端点
*   与 Flask 保持一致，例如在`url_for()`中引用路线
*   轻松版本化和命名空间 API URLs
*   使用分组功能轻松分离和分类 API 端点
*   序列化数据库行时的白名单 SQLAlchemy 字段
*   以 WTForms 熟悉的方式验证字段
*   处理基于令牌的身份验证，该身份验证适用于标头和 cookies
*   易于添加“额外”功能，如坚持烧瓶标准的速率限制

在一个完美的世界里，将会有一个或多个库来完成以上所有的工作。不仅如此，那些图书馆应该是受欢迎的，并且维护得很好。

幸运的是，我上面列出的所有东西现在都可以用了，你甚至不需要写很多代码，因为有 3 个库可以帮我们大忙。

如果你需要一点时间来兴奋地双拳出击，那就去吧。我会等的。

## 连接您的 API 端点

让我们从解决如何连接 API 端点开始。

我非常喜欢使用[Flask-class](http://flask-classful.teracy.org/)。这是一个帮助我们轻松设置 RESTful API 路由端点的库，它还使得在我们需要时定义自定义路由变得非常容易。

如果你是一个老的 Flask 开发者，或者碰巧自己在研究 API 库，你可能会记得见过一个名为 Flask-classic 的库。Flask Classful 是同一个库，只是它维护得很好。原 Flask Classy 库已被原作者弃用。

我们的愿望清单中的所有路线都包括在内。与使用 Flask 内置的[可插拔视图](http://flask.pocoo.org/docs/0.12/views/)相比，它有很多好处。这是个极好的选择。

## 序列化和输入验证

我们需要处理的下一件事是序列化和输入验证。

烧瓶-棉花糖构建在一个非常流行的叫做[棉花糖](https://github.com/marshmallow-code/marshmallow)的 Python 库之上。

基本上，它处理 JSON 与 SQLAlchemy 或 Python 对象之间的相互转换。

例如，我们不能只通过 HTTP 发送原始的 SQLAlchemy 行。相反，我们首先将其转换为 JSON，然后将该 JSON 作为响应发送。这是这个图书馆的主要目的。

它还让我们建立这些称为模式的东西。他们帮助我们列出白名单并验证输入。例如，如果我们的一个字段需要是长度在 2 到 200 个字符之间的字符串，我们可以在我们的模式中设置它。

如果它碰巧没有通过验证，那么它将返回一个友好的错误消息。您可以将 Marshmallow 的验证与 WTForms 库进行比较。

## 用户认证

最后，我们的 API 需要某种方法来处理认证。在你的 Flask 冒险中，你可能听说过 Flask-Login？这是一个优秀的基于会话/ cookie 的登录库。

但是因为我们现在处理的是 API，所以使用基于令牌的认证来代替会话对我们最有利，因为不是所有的客户端都支持会话和 cookies。

因此，我们将使用一种流行的令牌格式，称为 JSON Web 令牌(JWT)。

[Flask-JWT 扩展的](http://flask-jwt-extended.readthedocs.io/en/latest/)库为我们处理令牌的创建，还处理从头部或 cookies(浏览器)读取令牌的底层细节，以及提供一些其他的好处，比如设置一个 current_user 和一些与认证相关的装饰器。

基本上，它相当于 Flask-Login，但是对于 jwt。真的很贴心。

## 将所有这些放在一起构建一个 API

这里的想法是，除了可能学习一些基本的 API 特定概念，在构建“常规”Flask 应用程序和“API 驱动”Flask 应用程序之间应该没有巨大的知识障碍。

这两种类型的应用程序都是 Flask 应用程序，所以假设你已经有了一些 Flask 的经验，你将能够重用很多你已经知道的东西。

所以当用 Flask 构建 API 时，这是我试图遵循的哲学，上面列出的库是我使用的。我对结果非常满意，因为样板文件很少，代码非常易于维护。

#### 学习构建 RESTful APIs 和使用 Flask 的 Web Sockets

你想要一个视频导游实施以上所有和更多吗？

成千上万的人注册了我的[用 Flask 构建 SAAS 应用课程](https://buildasaasappwithflask.com/?utm_source=devto&utm_medium=website&utm_campaign=flask-api)，我经常收到反馈，要求开设一门关于用 Flask 构建 RESTful APIs 的新课程。

我最近做了一件更好的事情。我把自己锁在办公室里 6 个星期，发布了 3 个小时的视频，讲述了如何用 Flask 构建 API 驱动的应用程序，现在任何人都可以免费使用我现有的用 Flask 构建 SAAS 应用程序课程。

你可以把它看作是我们构建第二个应用程序的主课程的配套，这个应用程序使用了本文中提到的 3 个库等等。

网络插座也是这个免费应用的一个重要组成部分。所有这些都已准备就绪，您可以在[使用 Flask 网站](https://buildasaasappwithflask.com/?utm_source=devto&utm_medium=website&utm_campaign=flask-api)构建 SAAS 应用程序上了解更多课程信息。

在主课程和这个额外的 API 应用程序之间，你将能够构建许多不同类型的 web 应用程序。你会成为一个烧瓶大师。