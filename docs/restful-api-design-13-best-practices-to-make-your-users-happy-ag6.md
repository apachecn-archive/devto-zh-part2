# RESTful API 设计:让用户满意的 13 个最佳实践

> 原文：<https://dev.to/florimondmanca/restful-api-design-13-best-practices-to-make-your-users-happy-ag6>

自从 HTTP 协议存在以来，Web 服务就一直存在。但是特别是自从云计算出现以来，它们已经成为让客户与我们的数据进行交互的一种非常常见的方式。

当 SOAP APIs 还在的时候，我还没有足够幸运成为一名开发人员。所以我主要了解的是 **REST** ，一种用于构建 API 的基于资源的架构风格。

一两年来，我一直致力于构建或使用 API 的项目。

我见过的大多数 API 都声称是“**RESTful**”——也就是说，符合 REST 架构的原则和约束。

然而，他们中的一些人给了 REST 一个非常非常坏的名字。

[https://www.youtube.com/embed/nSKp2StlS6s](https://www.youtube.com/embed/nSKp2StlS6s)

状态代码的错误使用、纯文本响应、不一致的模式… **我已经全部看过了**(或者至少是一大堆)。所以我决定写下我认为的设计 REST API 时的一些**良好实践。**

## 免责声明

我没有权威说下面的做法 100%符合圣息原则(如果真有这种东西！).我从构建和使用各种 API 的经验中收集了这些信息。

同样，我也不会假装已经掌握了 REST API 设计！这是一门**手艺**——你练习得越多，你就会做得越好。

我会曝光一些代码片段作为“糟糕设计的例子”。如果它们看起来像你写的东西，没关系！唯一重要的是我们一起学习。我希望这篇小小的文章能帮助我们做到这一点。

下面是设计让用户满意的 REST APIs 的技巧、建议和推荐。

## 1。学习应用于 REST 的 HTTP 基础知识

如果你要构建一个设计良好的 REST API，你最好了解 T2 HTTP 协议的基础知识。我真的相信**这将帮助你做出更好的设计决策**。

我发现 MDN web docs 上 HTTP 的[概述是这方面的一个很好的读物。但是，就 **REST API 设计**而言，这里有一个 TL；**的博士 HTTP 应用于 REST** :](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)

*   HTTP 有**动词**(或方法):GET、POST、PUT、PATCH、DELETE 是最常见的。
*   REST 是**面向资源的**，一个资源用一个 **URI** : `/newspapers/`来表示。
*   **端点**是动词和 URI 的组合，例如`GET: /articles/`。
*   端点可以被解释为对资源的**动作。例如，`POST: /articles/`可能表示“创建新文章”。**
*   在高层次上，**动词映射到 CRUD 操作** : `GET`表示`Read` , `POST`表示`Create` , `PUT`和`PATCH`表示`Update`,`DELETE`表示...嗯，`Delete`。
*   响应的状态由其**状态代码**指定:`1xx`表示信息，`2xx`表示成功，`3xx`表示重定向，`4xx`表示客户端错误，`5xx`表示服务器错误。

当然，您可以使用 HTTP 协议为 REST API 设计提供的任何东西，但是我认为这些是您需要记住的基本内容。

## 2。不要返回纯文本

尽管 REST 架构风格没有强制要求，但大多数 REST APIs 都使用 JSON 作为数据格式。

然而，返回包含 JSON 格式字符串的主体是不够的。你需要**指定`Content-Type`头也要**！必须将其设置为值 **`application/json`** 。

这对于**编程客户端**(例如，某人或服务通过 Python 中的`requests`库与您的 API 交互)尤其重要——他们中的一些依赖于这个头来正确解码响应。

**专业提示**:你可以用火狐很容易地验证一个回复的`Content-Type`。它内置了一个漂亮的显示来显示`Content-Type: application/json`的响应。🔥

<figure>

[![](../Images/5140b49488611fa27d1ab7363627d1e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EKt9dIJI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/0c738101-95ff-444d-b894-a866d3d9f63b.png)

<figcaption>In Firefox, "Content-Type: text/plain" looks… plain.</figcaption>

</figure>

<figure>

[![](../Images/e8a43dc1bb9ce0288ad3731d8fbe0293.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M_bAKekG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/bf8c0ed7-d1f5-42dc-ba77-e4b260ef536a.png)

<figcaption>"Content-Type: application/json" detected! Look how pretty and functional this is. 🕺</figcaption>

</figure>

## 3。在 URIs 避免使用动词

如果你已经理解了基本知识，你现在就会知道把动词放在 URI 中是不明智的。

这是因为 **HTTP 动词应该足以描述在资源**上执行的动作。

假设您想要提供一个端点来生成和检索一篇文章的横幅图像。我将注意到`:param`是 URI 参数的占位符(比如 ID 或 slug)。您可能想创建这个端点:

```
GET: /articles/:slug/generateBanner/ 
```

Enter fullscreen mode Exit fullscreen mode

但是`GET`方法在语义上足以说明我们正在检索(“获取”)一个横幅。所以，我们就用:

```
GET: /articles/:slug/banner/ 
```

Enter fullscreen mode Exit fullscreen mode

类似地，对于创建新文章的端点:

```
# Don't
POST: /articles/createNewArticle/
# Do
POST: /articles/ 
```

Enter fullscreen mode Exit fullscreen mode

HTTP 动词所有的东西！

## 4。使用复数资源名词

可能很难决定资源名词应该用复数还是单数形式。

应该用`/article/:id/`(单数)还是`/articles/:id/`(复数)？

我建议使用复数形式。

为什么？因为它非常适合所有类型的端点。

我同意`GET /article/2/`很好，但是`GET /article/`呢？我们得到的是系统中唯一的一篇文章，还是所有的文章？

为了防止这种暧昧，**让我们保持一致**(人生忠告！)而且处处用复数:

```
GET: /articles/2/
POST: /articles/
... 
```

Enter fullscreen mode Exit fullscreen mode

## 5。在响应正文中返回错误详细信息

当一个 API 服务器处理一个错误时，这是很方便的(也是推荐的！)将 JSON 主体中的**错误细节**返回给**帮助用户调试**。特别的荣誉，如果你包括哪些领域受到了错误的影响！

```
{  "error":  "Invalid payload.",  "detail":  {  "surname":  "This field is required."  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 6。请注意状态代码

这个*超级重要*。如果从这篇文章中你需要记住一件事，那就是:

> 您的 API 可能做的最糟糕的事情是返回一个带有`200 OK`状态代码的错误响应。

简直就是糟糕的语义。相反，**返回一个有意义的状态码**，它正确地描述了错误的类型。

不过，您可能会想，“但是我按照您的建议在响应正文中发送了错误细节，这有什么问题吗？”

我给你讲个故事吧。

我曾经不得不使用一个 API，它为每个响应返回`200 OK`，并通过一个`status`字段:
指示请求是否成功

```
{  "status":  "success",  "data":  {}  } 
```

Enter fullscreen mode Exit fullscreen mode

所以即使状态是`200 OK`，我也不能绝对肯定它没有处理我的请求。

事实上，API 可以返回如下响应:

```
HTTP/1.1 200 OK
Content-Type: text/html

{
    "status": "failure",
    "data": {
        "error": "Expected at least two items in list."
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

(是的——它也返回 HTML 内容，因为为什么不呢？)

因此，在我读取`data`之前，我必须检查状态代码和 ad-hoc `status`字段，以绝对确保一切正常。

这种设计是一个真正的禁忌，因为**它破坏了 API 和他们的用户之间的信任**。你开始担心 API 可能在骗你。

所有这一切都令人极度不安。你应该怎么做？

**利用状态码，仅使用响应体提供错误详情**。

```
HTTP/1.1 400 Bad Request
Content-Type: application/json

{  "error":  "Expected at least two items in list."  } 
```

Enter fullscreen mode Exit fullscreen mode

## 7。一致地使用状态代码

一旦你掌握了状态码，你应该努力坚持使用它们**。**

 **例如，如果您选择一个`POST`端点在某处返回一个`201 Created`，那么对每个`POST`端点使用相同的状态代码。

为什么？因为用户不应该担心*哪个端点上的哪个方法会在什么情况下返回哪个状态码*。

所以要保持一致，如果你偏离了常规，**在某个有大标志的地方记录下来。**

一般来说，我坚持如下:

```
GET: 200 OK
POST: 201 Created
PUT: 200 OK
PATCH: 200 OK
DELETE: 204 No Content 
```

Enter fullscreen mode Exit fullscreen mode

## 8。不要嵌套资源

REST APIs 处理资源，检索资源的列表或单个实例非常简单。但是当你处理**相关资源**时会发生什么呢？

假设我们想要检索特定作者的文章列表—带有`id=12`的作者。基本上有两种选择。

第一种选择是在`authors`资源下**嵌套**资源`articles`，例如:

```
GET: /authors/12/articles/ 
```

Enter fullscreen mode Exit fullscreen mode

有些人推荐它，因为它确实代表了作者和他们的文章之间的一对多关系。

然而，**现在已经不清楚**你在请求什么样的资源了。是作者吗？是文章吗？

还有[平的比嵌套的](https://www.python.org/dev/peps/pep-0020/#id3)好，所以一定有更好的办法……而且有！

我的建议是**使用查询字符串**直接过滤`articles`资源:

```
GET: /articles/?author_id=12 
```

Enter fullscreen mode Exit fullscreen mode

这显然意味着:“获取作者#12 的所有文章”，对吗？👍

## 9。优雅地处理尾随斜线

URIs 是否应该有一个尾随者并不是一个真正的争论。简单地选择一种方式或另一种方式(即，有或没有尾随斜线)，坚持下去，如果客户使用错误的约定，那么**优雅地重定向客户。**

(我必须说我自己也不止一次为这个感到内疚。🙈)

故事时间！一天，当我正在将一个 REST API 集成到一个项目中时，我不断地在每个单独的调用中收到`500 Internal Error`。我使用的端点看起来像这样:

```
POST: /entities 
```

Enter fullscreen mode Exit fullscreen mode

我很生气，不知道自己做错了什么。

最后，结果是服务器失败了，因为我少了一个斜杠！所以我开始用

```
POST: /entities/ 
```

Enter fullscreen mode Exit fullscreen mode

之后一切都很好。🤦‍♂️

API 没有修复，但是希望你能为你的用户防止这种问题。

大多数 web 框架都有一个选项，可以优雅地重定向到被跟踪或未被跟踪的 URL 版本。找到并激活该选项。

## 10。利用 querystring 进行过滤和分页

很多时候，简单的端点不足以满足复杂的业务案例。

您的用户可能希望检索满足特定条件的项目，或者一次检索少量项目以提高性能。

这正是**过滤**和**分页**的目的。

通过**过滤**，用户可以指定返回的项目应该具有的属性。

**分页**允许用户检索数据集的**部分。最简单的一种分页是**页码分页**，由 a `page`和 a `page_size`决定。**

现在的问题是:**如何在 RESTful API 中整合这些特性？**

我的回答是:**使用 querystring** 。

我认为很明显为什么应该使用 querystring 进行分页。看起来应该是这样的:

```
GET: /articles/?page=1&page_size=10 
```

Enter fullscreen mode Exit fullscreen mode

但是对于过滤来说可能不太明显。起初，您可能会想到这样做，只检索已发表文章的列表:

```
GET: /articles/published/ 
```

Enter fullscreen mode Exit fullscreen mode

设计问题: **`published`不是资源！**相反，它是你正在检索的数据的一个特征。这种事情应该放在**的**里。

所以最终，用户可以检索“包含 20 个条目的已发表文章的第二页”，如下所示:

```
GET: /articles/?published=true&page=2&page_size=20 
```

Enter fullscreen mode Exit fullscreen mode

很明显，不是吗？

## [11](#11-learn-the-difference-between-raw-401-unauthorized-endraw-and-raw-403-forbidden-endraw-)。了解`401 Unauthorized`和`403 Forbidden`的区别

在处理 API 中的安全错误时，很容易混淆错误是与**认证**还是**授权**(也称为许可)有关——这在我身上经常发生。

以下是我的备忘单，让你知道我根据具体情况在处理什么:

*   用户是否没有提供身份验证凭据？它们无效吗？👉`401 Unauthorized`。
*   用户的身份验证是否正确，但他们没有访问资源所需的权限？👉`403 Forbidden`。

## 12。好好利用`202 Accepted`

我发现`202 Accepted`是`201 Created`的一个非常方便的替代品。它基本上意味着:

> 我，服务器，已经理解了你的要求。我还没有创建资源，但这很好。

我发现有两种情况`202 Accepted`特别适合:

*   如果资源将作为未来处理的结果而创建，例如在作业完成后。
*   如果资源已经以某种方式存在，但是这不应该被解释为错误。

## 13。使用专门针对 REST APIs 的 web 框架

作为最后一个最佳实践，让我们来讨论这个问题:**你如何在你的 API 中实现最佳实践？**

通常，您希望创建一个快速的 API，以便一些服务可以相互交互。

Python 开发人员会使用 Flask，JS 开发人员会使用 Express，他们会实现一些简单的路由来处理 HTTP 请求。

这种方法的问题是**一般来说，框架的目标不是构建 REST API 服务器**。

例如，Flask 和 Express 是两个非常通用的框架，但是它们不是专门用来帮助你构建 REST APIs 的。

因此，您必须采取**额外的步骤**来在您的 API 中实现最佳实践。大多数时候，**懒惰或者时间不够意味着你不会努力**——留给你的用户一个古怪的 API。

解决方法很简单:**使用合适的工具完成工作**。

<figure>

[![](../Images/ed342cbfd552019bb3b56167760ae980.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HhPeysNy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://edm00se.io/assets/images/post_images/right-tool-for-job-jekyll/scottySays.jpg)

<figcaption>Holy sayings from good lad Scotty.</figcaption>

</figure>

各种专门用于构建 REST APIs 的语言中出现了新的框架。**它们可以帮助您轻松地遵循最佳实践，而不会影响工作效率。**

在 Python 中，我发现的最好的 API 框架之一是 [Falcon](https://falconframework.org) 。它使用起来和 Flask 一样简单，非常快，非常适合在几分钟内构建 REST APIs。

<figure>

[![](../Images/d43d4f170a3d1ca6c2fb3988af2c7172.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1gzAPXFV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encrypted-tbn0.gstatic.com/images%3Fq%3Dtbn:ANd9GcRZl9VueEJaJP9Wea2r53lnQmp_DCJL4S3HNR-oi7lXG63QQMAw)

<figcaption>Falcon: Unburdening APIs for over 0.0564 centuries.</figcaption>

</figure>

如果你是一个 Django 类型的人，那么你可以选择 Django REST 框架。它不那么直观，但是非常强大。

在 NodeJS 中， [Restify](http://restify.com) 似乎也是一个不错的选择，尽管我还没有尝试过。

我强烈建议您尝试一下这些框架！它们将帮助您构建漂亮、优雅和设计良好的 REST APIs。

## 让我们给 REST 起个伟大的名字吧！

我们都应该努力让 API 成为一种享受。希望这篇文章能帮助你学习一些技巧和技术来构建更好的 REST APIs。对我来说，这可以归结为**好的语义**、**简单**和**常识**。

REST API 设计更像是一门手艺。如果你对以上几点有不同的看法，我很乐意听听。

与此同时，保持他们伟大的 API 来了！💻

## 保持联系！

如果你喜欢这篇文章，你可以在 Twitter 上找到我，获取更新、公告和新闻。🐤**