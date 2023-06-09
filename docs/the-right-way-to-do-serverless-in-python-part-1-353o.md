# 用 Python 实现无服务器的正确方法(第 1 部分)

> 原文：<https://dev.to/adjohn/the-right-way-to-do-serverless-in-python-part-1-353o>

*注:本文作者是[迈克尔·拉弗斯](https://github.com/kolanos)T3】*

毫无疑问，你已经听说过，自 2013 年 11 月 AWS Lambda 发布以来，无服务器已经取得了突飞猛进的发展。虽然不必管理服务器、只为您实际使用的计算付费以及开箱即用的横向扩展的想法听起来很有吸引力，但您该从哪里开始呢？作为一名 Python 开发者，也许你听说过[无服务器框架](https://serverless.com)或者[Zappa](https://www.zappa.io)；要么是使用 Node.js(在无服务器的情况下)这样一个不熟悉的生态系统的前景令人望而生畏，要么可能是你不清楚这些工具实际上是做什么的，以及当你刚刚开始时它们是否有过度杀伤力。如果是这样的话，你来对地方了。在这篇博客文章中，我们将简要介绍一下适用于 Python 的无服务器技术，以及一些方便的技巧和窍门来帮助您入门。

## 我的应用/工作负载适合无服务器吗？

这可能是你需要问的关于无服务器的最重要的问题。虽然无服务器有很多优点，但它并不适合所有应用/工作负载。例如，AWS Lambda 函数调用的当前最大持续时间是五分钟。这意味着，如果您的应用程序/工作负载不能分成五分钟的小块，那么无服务器可能不是最佳选择。例如，如果你的应用程序使用 websockets，它与服务器保持一个持久的连接，在 AWS Lambda 上这个连接将被关闭，需要每五分钟重新建立一次。这还意味着您要为实际上只是保持套接字打开的工作负载支付所有计算时间。但是如果你的应用程序大部分是无状态的，比如 REST 或 GraphQL API，那么无服务器可能非常适合，因为 HTTP 请求很少超过 30 秒，更不用说 5 分钟了。

无服务器的真正优势在于工作负载，在这些工作负载中，您可能会遇到高峰和/或低活动时段。仔细想想，这种描述可能适用于您工作过的大多数应用程序/工作负载。当您的应用程序/工作负载达到峰值时，AWS Lambda 可以提供相当大的水平扩展，默认情况下开箱即可处理 1，000 个并发请求(这个限制可以提高)。当您的应用/工作负载处于低活动期时，您的计量器不会全速运行，这可以为您节省大量运营费用。想一想；大多数应用程序/工作负载都服务于一系列时区，那么为什么您要在客户熟睡时支付全价来运行您的应用程序/工作负载呢？

如果你仍然不确定你的应用/工作负载是否合适，这里有一个[方便的计算器](https://servers.lol)来比较 AWS Lambda 和 EC2。

## Python 2 还是 3？

在过去十年中，Python 生态系统经历了许多变化——最重要的是 Python 3 的发布和许多代码库从 Python 2.x 到 3.x 的过渡。在无服务器的世界中，这里的建议与任何其他 Python 项目一致:如果是新项目，请使用 Python 3.6。虽然 Python 2.7 很好地服务了我们许多人，但它将在 2020 年后[停止接收更新](https://pythonclock.org)。因此，如果您还没有开始向 3.6 过渡，您有两年的时间来这样做。

如果你有一个现有的 Python 2.7 项目，你很幸运，因为 AWS Lambda 支持 2.7，但是你应该认真考虑尽快将你的代码移植到 Python 3.6。这里有一个关于如何编写 Python2+3 兼容代码的超级有用的[备忘单](http://python-future.org/compatible_idioms.html)。和备忘单一样，这里的建议是 2.7/3.6 兼容。

## Web API 还是 Worker？

在我们讨论 Python 可用的无服务器工具之前，让我们更深入地了解一下我们的应用程序/工作负载。如果您有一个 web 应用程序，它为一个前端提供几个 web 资产(HTML、JavaScript、CSS、图像等)。)，那么你不应该通过你的 AWS Lambda 函数提供这些。那是*不是说你不能，只是说你不应该*。请记住，使用 AWS Lambda，您需要为函数运行的时间付费。把这个时间花在提供网络资产上没有多大意义。事实上，由于您的前端可能有许多 web 资产，这可能是一种相对昂贵的方式来完成原本简单的任务。为了提供网络资产，你应该看看 CDN(内容交付网络)。AWS 有一个专门为这个目的构建的服务叫做 CloudFront，这里是[关于如何在 S3 上使用它的指南](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/DownloadDistS3AndCustomOrigins.html)。

但这实际上只涵盖了你的网络应用的前端。也可能你的 app/工作负载根本没有前端。我们将这些工作负载分为两类:web API(REST、GraphQL 等。)，还有工人。您可能已经熟悉了 web API 的种类，AWS Lambda 允许我们使用一个名为 API Gateway 的服务来为它们提供服务(稍后会详细介绍)。另一个，我称之为“工人”，是你的应用程序可能需要做的所有其他任务，例如:发送电子邮件，上传文件，推送通知等。无服务器可以处理这种工作负载，甚至提供自己的 cron job like 服务来处理预定任务。

我定义这两个类别的原因是，虽然在无服务器环境中，这些工作负载可能看起来相似(或者无法区分，因为它们本质上都是功能)，但一些工具更擅长或专为其中一个而设计。在本指南中，我将指出这种情况。希望你已经在考虑应用程序的哪些部分将通过 web API 提供服务，哪些部分可以是在后台运行的工人任务，这样你就可以选择合适的工具来完成工作。

## 无服务器框架

毫无疑问，无服务器领域最大的亮点是[无服务器框架](https://serverless.com)，这是有充分理由的。它背后的团队投入了大量的时间和精力到开发者体验中，使它成为最直观和最容易使用的无服务器工具之一。它还提供了首屈一指的功能集，除了 AWS Lambda 和不断增长的插件生态系统之外，还支持多种云。对于 Python 开发人员来说，这似乎是一个显而易见的起点，当然也是一个很好的起点。

但是无服务器框架并不是没有一些 Python 特有的警告。对于初学者来说，Serverless 是用 Node.js 编写的，可能不合你的胃口。但是像任何好的工具一样，如果做得好，你甚至不会注意到它是用什么语言实现的，这里的例子当然也适用于无服务器。也就是说，你仍然需要[安装 Node 和 NPM](https://nodejs.org/en/download/) ，但是之后你只需要`npm install -g serverless`让无服务器 CLI 成为你的主要界面。您不需要了解任何 JavaScript，尽管默认情况下您的配置是 YAML。

让我们试一试。

```
npm install -g serverless 
```

可以使用`serverless`或简写`sls`来访问 CLI。我更喜欢`sls`，所以让我们创建一个项目:

```
mkdir ~/my-serverless-project
cd ~/my-serverless-project
sls create -n my-serverless-project -t aws-python3 
```

在这里，我创建了一个名为`my-serverless-project`的目录，并使用`sls create`创建了一个项目。我还用`-t aws-python3`指定了一个模板。无服务器捆绑了几个模板，这些模板会在你运行`sls create`时创建的`serverless.yml`中为你设置一些合理的默认值。在这种情况下，我将为 Python 3.6 指定 AWS 模板。如果你的项目是 Python 2.7，还有一个`aws-python2`。还有其他语言和云的模板，但这超出了本指南的范围。`-n my-serverless-project`指定了一个服务名，你可以把它改成你想命名的项目。一旦您运行这些命令，您应该在您的`my-serverless-project`目录中有一个`serverless.yml`。我们来看看内容:

```
cat serverless.yml 
```

正如你将看到的,`serverless.yml`是 YAML，它装载了几个有用的注释来解释配置的每个部分。在这一点上，我建议通读这些评论，因为它将有助于以后。完成后，让我们编写一个相当于 lambda 函数的 hello world:

```
def handler(event, context):
    return {"message": "hi there"} 
```

并在我们的`my-serverless-project`目录中保存为`hello.py`。名字`handler`就是 lambda 函数通常被引用的名字，你可以给你的函数起任何你喜欢的名字。现在我们有了一个函数，让我们通过将它添加到我们的`serverless.yml`来让无服务器知道它。首先打开你的`serverless.yml`，寻找`functions`部分。然后用以下内容替换该部分:

```
functions:
  hello:
    handler: hello.handler 
```

现在保存你的`serverless.yml`。为了部署这个功能，我们需要确保我们已经配置了 [AWS 凭证](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)。假设我们都准备好了，那么就运行下面的代码:

```
sls deploy 
```

部署需要一点时间。简而言之，以下是无服务器正在执行的步骤:

1.  基于`serverless.yml`创建云形成模板
2.  将 CloudFormation 模板和您的`hello.py`压缩到一个 zip 存档中
3.  创建一个 S3 存储桶并将 zip 存档上传到其中
4.  执行 CloudFormation 模板，包括配置一个 AWS Lambda 函数并将其指向 S3 zip 存档

您可以手动完成所有这些步骤，但是为什么要这样做呢？当我们的部署完成后，我们可以使用以下命令对其进行测试:

```
sls invoke -f hello 
```

这里的`-f hello`是我们在`serverless.yml`文件中指定的函数名。`sls invoke`命令调用该函数。函数调用是一个用来调用函数的时髦词。当我们调用这个函数时，它应该响应:

```
{"message": "hi there"} 
```

如果这就是你所看到的，那么恭喜你，你已经创建了你的第一个 AWS Lambda 函数。但是这并不是非常有用。？如果我们想做一些更有挑战性的事情，比如发出一个 HTTP 请求并返回结果。让我们创建一个名为`httprequest.py`的新文件，并添加以下内容:

```
import requests

def handler(event, context):
    r = requests.get("https://news.ycombinator.com/news")
    return {"content": r.content} 
```

更新您的`serverless.yml`的`functions`部分:

```
functions:
  hello:
    handler: hello.handler
  httprequest:
    handler: httprequest.handler 
```

并重新部署和调用我们的新功能:

```
sls deploy
sls invoke -f httprequest 
```

您现在应该会看到一个`ImportError`。这是因为没有安装`requests`。对于 AWS Lambda，我们需要将任何想要使用的库与我们的函数捆绑在一起。我们可以通过多种方式做到这一点，用`pip`我们可以运行:

```
# Don't run this just yet, keep reading
pip install requests -t . 
```

这将在我们的`my-serverless-project`目录中安装`requests`轮(及其依赖项)。但是有一个问题。AWS Lambda 运行在 64 位 Linux 上。因为`requests`带有编译的依赖项，如果你没有运行 64 位 Linux，你刚刚安装的这个`requests`将不能在 AWS Lambda 上运行。如果你运行的是 Mac OS，你会怎么做呢？还是 Windows？还是 FreeBSD？或者，嗯，你明白了。

拯救无服务器插件(和 Docker)。

令人欣慰的是，Serverless 附带了一个插件生态系统来填补空白，对于 Python 来说，带有编译代码的包恰好是这些空白之一。这里的相关插件被恰当地命名为[server less-python-requirements](https://github.com/UnitedIncome/serverless-python-requirements)，要安装它:

```
npm install -g serverless-python-requirements 
```

并将以下几行添加到您的`serverless.yml`的末尾:

```
plugins:
  - serverless-python-requirements 
```

现在这个插件立即启用了`requirements.txt`支持。因此，代替上面的`pip`行，我们现在只需要将一个`requirements.txt`文件添加到我们的项目目录中，下次我们运行`sls deploy`时，需求将被自动安装和捆绑。让我们现在就开始吧:

```
echo "requests" >> requirements.txt 
```

但是我们还没有解决我们的编译问题。为此，我们需要在我们的`serverless.yml`中添加一个`custom`部分。这一部分是我们放置自定义配置选项的地方，但也是插件寻找自己的配置选项的地方。我们新的`custom`部分应该是这样的:

```
custom:
  pythonRequirements:
    dockerizePip: true 
```

这是告诉`serverless-python-requirements`插件在将 Python 包打包到 zip 存档文件之前，先在 Docker 容器中编译它们，以确保它们是为 64 位 Linux 编译的。你还需要[安装 Docker](https://docs.docker.com/install/) 来完成这项工作，但是一旦完成，这个插件将自动处理你在`requirements.txt`文件中定义的依赖关系。很酷吧。

现在让我们重新部署和调用我们的功能:

```
sls deploy
sls invoke -f httprequest 
```

即使您运行的是 64 位 Linux，这也要干净得多，您不觉得吗？

在我们继续之前，你可能有几个问题。什么是`event`、`context`，这些对我有什么用？

AWS Lambda 函数是事件驱动的，所以当您调用一个函数时，您实际上在 AWS Lambda 中触发了一个事件。lambda 函数的第一个参数包含触发该函数的事件，它在 AWS Lambda 中表示为一个 JSON 对象，但传递给 Python 的是该对象的 dict。当你运行`sls invoke -f hello`时，传递给 lambda 函数的是一个空的 dict。但是如果是 API 请求，它将包含表示为 dict 的整个 HTTP 请求。换句话说，`event` dict 充当 lambda 函数的输入参数，lambda 函数返回的是输出。使用 AWS Lambda，您的输出也需要是 JSON 可序列化的。这里是[你可能会在 AWS Lambda 上看到的一些示例事件](https://docs.aws.amazon.com/lambda/latest/dg/eventsources.html)。

第二个参数是 AWS Lambda 上下文，它是一个 Python 对象，包含关于 Lambda 函数和当前调用的有用元数据。例如，每个调用都有一个`aws_request_id`,如果您想在日志中跟踪特定调用中发生了什么，这很有用。查看[这里的](https://docs.aws.amazon.com/lambda/latest/dg/python-context-object.html)以获得更多关于上下文对象的信息。您可能不需要马上担心上下文对象，但是您最终会发现它在调试时很有用。

那么这有什么用呢？好吧，如果你的应用/工作负载可以处理 JSON 可串行化输入并产生 JSON 可串行化输出，那么它可以直接插入 AWS Lambda。如果我们回到我们的两个工作负载类别，web API 和 worker，我们已经很好地实现了 worker 所需的功能。假设我们希望每十分钟运行一次`httprequest`函数，为了做到这一点，我们需要做的就是向`serverless.yml`中添加以下内容:

```
functions:
  httprequest:
    handler: httprequest.handler
    events:
      - schedule: rate(10 minutes) 
```

并重新部署:

```
sls deploy 
```

我们结束了。现在`httprequest`是每十分钟自动触发一次。如果你想要更细粒度的控制，你可以[指定一个特定的时间](https://serverless.com/framework/docs/providers/aws/events/schedule/)你的功能也应该被触发。您还可以使用社交网络、SQS 或其他 AWS 服务来构建更复杂的工作流程。

web API 呢？还记得之前我们讨论过事件并提到 HTTP 请求可以表示为一个事件吗？在 web APIs 的情况下，亚马逊的 API 网关服务将为我们触发事件。除此之外，API Gateway 将提供一个可以接收 HTTP 请求的主机名，将这些 HTTP 请求转换成一个事件对象，调用我们的 lambda 函数，收集响应并将其作为 HTTP 响应传递给请求者。这听起来可能很复杂，但谢天谢地，无服务器框架为我们抽象了大部分内容。让我们给我们的`serverless.yml`添加一个 HTTP 端点:

```
functions:
  webapi:
    handler: webapi.handler
    events:
      - http:
          path: /
          method: get 
```

这看起来很像我们之前计划的工人任务，不是吗？像在调度任务中一样，这里我们配置这个处理程序来处理`http`事件，并指定一个`path`和`method`。正如您可能已经猜到的，`path`是我们的 HTTP 请求路径，`method`是这个处理程序将要处理的 HTTP 方法。您还会注意到我们添加了一个新的`handler`，现在让我们在`webapi.py`中创建它:

```
import json

def handler(event, context):
    return {"statusCode": 200, "body": json.dumps({"message": "I'm an HTTP response"})} 
```

这里，我们的处理程序将从 API 网关接受一个事件，并用一个 JSON serializable dict 进行响应。在 dict 中，我们有两个键，第一个是`statusCode`，它是我们希望 API 网关响应的 HTTP 状态代码，第二个是响应的 HTTP 主体。您还会注意到我们也将`body`序列化到 JSON 中。我们这样做的原因是 API Gateway 希望 HTTP 响应主体是一个字符串。因此，如果我们希望我们的 web API 使用 JSON 进行响应，我们需要在将它返回给 API Gateway 之前对它进行序列化。这也意味着，如果你想支持其他形式的序列化，比如 XML、YAML 或其他——你也可以这样做。

现在让我们来部署它:

```
sls deploy 
```

一旦完成，无服务器框架将为我们提供一个端点:

```
endpoints:
 GET - https://XXXXXXXXXX.execute-api.us-east-1.amazonaws.com/dev 
```

您的端点看起来会有点不同。这里刚刚发生了什么？嗯，简而言之，Serverless 创建了我们新的 AWS Lambda 函数，然后配置了一个 AWS API 网关来指向这个 Lambda 函数。您在上面看到的端点是 API Gateway 提供的。

让我们来看看我们的终点:

```
curl https://XXXXXXXXXX.execute-api.us-east-1.amazonaws.com/dev 
```

您应该会看到以下响应:

```
{"message":  "I'm an HTTP response"} 
```

祝贺您，您已经创建了您的第一个无服务器 web API。现在你可能已经注意到，网关提供的 URL API 非常难看。如果能像`https://api.mywebapi.com/`这样可读性更强的就更好了。嗯，也有一个的[插件。](https://github.com/amplify-education/serverless-domain-manager)

## 打扫卫生

在这篇文章中，我们创建了三个 Lambda 函数和一个 API 网关。但是这些只是帮助您尝试无服务器的例子。你可能想清理一下，为此你只需运行:

```
sls remove 
```

无服务器框架会处理剩下的事情。

## 求和

在这篇文章中，我们回顾了 AWS Lambda 的一些基础知识，并使用无服务器框架创建了一个简单的 web API 和 worker。在[第 2 部分](https://dev.to/adjohn/the-right-way-to-do-serverless-in-python-part-2-3deg)中，我们涵盖了一些更高级的主题，包括专门为 Python 构建的两个无服务器框架的介绍: [Zappa](https://www.zappa.io) 和 [Chalice](https://github.com/aws/chalice) 。我们还展示了使用这些工具构建 web API 的速度，包括使用流行 web 框架的例子，如 [Django](https://www.djangoproject.com) 和 [Flask](http://flask.pocoo.org) 。