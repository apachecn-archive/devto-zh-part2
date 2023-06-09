# 用 Contentful 和 Twilio 运行一个无服务器的选举活动

> 原文：<https://dev.to/robertsvensson/running-a-serverless-voter-campaign-with-contentful-and-twilio-4332>

* * *

波利政客希望通过投票当选。她与 Selly 名人合作，一起开展短信选民活动。

本文将解释如何将 [Contentful](https://www.contentful.com/why-contentful/?utm_campaign=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio&utm_medium=referral&utm_source=devto&utm_content=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio) 和 [Twilio 可编程 SMS API](https://www.twilio.com/sms/) 与 Python 的 Flask 微框架集成。这篇文章还将强调如何在 Contentful 中设置 webhooks，以及如何将其部署到 [AWS Lambda](https://aws.amazon.com/lambda/) 以使该应用程序真正无服务器——所有这些都是为了让 Polly Politician 获得尽可能多的选票。

注意:本文假设你有 [Contentful](https://www.contentful.com/sign-up/?utm_campaign=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio&utm_medium=referral&utm_source=devto&utm_content=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio) 、 [Twilio](https://www.twilio.com/try-twilio) 和 [Amazon AWS](https://aws.amazon.com/free/) 的用户账户——使用它们各自的免费层就可以了。

### 我们将建造什么

我们正在开发一个 Python Flask 应用程序，它将通过短信向潜在的选民发送诸如“为 Polly 投票——她是最棒的”之类的竞选信息。

这将通过使用 [Contentful 内容交付 API](https://www.contentful.com/developers/docs/references/content-delivery-api/?utm_campaign=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio&utm_medium=referral&utm_source=devto&utm_content=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio) 从 Contentful 抓取内容来工作，然后连接到 Twilio 的[可编程 SMS API](https://www.twilio.com/sms/) 来获得交付的消息。

我们还将配置 webhooks，以确保每当竞选经理发布新的竞选消息时，该消息将被发送到 Twilio 的[可编程 SMS API](https://www.twilio.com/sms/) 并随后交付给选民。

通过阅读本文，您将学会如何:

*   从知足开始
*   使用内容丰富的 Python SDK
*   整合 Contentful 和 Twilio
*   在 Contentful 中设置 webhooks
*   使用 Zappa 部署到 Amazon AWS

### 安装依赖项，设置一个最小的 Flask app

我们从安装 Flask、Contentful 和 Twilio 的包开始，如下所示:

```
pip install flask contentful twilio 
```

Enter fullscreen mode Exit fullscreen mode

为了确保我们已经调试就绪，并指出我们即将创建的 Flask 应用程序，我们将运行:

```
export FLASK_DEBUG=ON
export FLASK_APP=app.py 
```

Enter fullscreen mode Exit fullscreen mode

flask 应用程序将有一个名为 **runVoterCampaign()** 的函数。因此，我们的 Flask 应用程序的初始版本将如下所示:

```
from contentful import Client as contentfulClient
from twilio.rest import Client as twilioClient
from flask import Flask
app = Flask(__name__)
@app.route("/")
def runVoterCampaign():
    return "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

### 心满意足的入门

简而言之- [Contentful 就是内容基础设施](https://www.contentful.com/r/knowledgebase/content-infrastructure/?utm_campaign=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio&utm_medium=referral&utm_source=devto&utm_content=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio)。因此，如果你有文本、图像或任何其他类型的数字资产想要提供给你的用户，你可以使用 Contentful 通过 Contentful 的 WebApp 和 API 调用来编辑、管理和交付内容。

Contentful 将你的数据存储在所谓的空间中。您可以将空间想象成一个特定于项目的桶，您可以用属于您的项目的各种资产来装满它。

#### 创建空间

当你创建免费的内容丰富的帐户时，你将被要求创建一个空间。虽然有一些例子可以帮助您开始，但是我们将创建一个名为**的空白空间来获得更多的投票**。

[![image alt text](img/873fb2d15d565e5feaf2e201d24c3f72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E8Rzw6EL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/4DnnUw0L5uAi64cwW2yowG/3f215a4265ec1fbd3b626b7c0dd92ce5/1_twilio_contentful.png)

#### 创建内容类型

有了内容丰富的空间，下一步就是创建适合该空间的内容类型。

为了实现这个功能，我们需要创建两种内容类型:voterMessage 和 voterNumbers。

让我们从创建 voterMessage 内容类型开始:

[![image alt text](img/51df87d4c9f88ad341ecff05097008bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VIT645Yu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/6R1LKYKK2cwWqCiYSkAaiq/b76fde8c931f15d9dc88ab0543997cb8/2_twilio_contentful.png)

创建内容类型后，我们需要添加一个字段来保存我们的信息:

[![image alt text](img/40964353191e36cd816c8e41cccc8f85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xRK8PXYF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/4hhV7DUGhqCEOGGMsCkg4a/7a6c919de1941da7bb85012e663ad575/3_twilio_contentful.png)

我们将使用单个文本字段:

[![image alt text](img/20cb94316c8a123d18d0d4f5a8372974.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NfvYRz16--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/CyG29aiPGoEM4GQcqymUE/c14ebc5426dbb859900c62d5dee93869/4_twilio_contentful.png)

并将其命名为 messageToVoters，如下所示:

[![image alt text](img/5ecc6f0aee4fd6f2349f942c667c0381.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jXP_raKW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/5D4UGyTHZSs4Oi88wqIImQ/e5209b427cf3d53f940ff1fa1cec1e3d/5_twilio_contentful.png)

内容类型就绪后，让我们通过选择**Content/Add entry/voter message**来添加将要发送给潜在投票者的消息。

Polly political 想要传达的信息是——“Selly 名人支持 Polly political——你也应该支持”，所以让我们补充一下:

[![image alt text](img/be51e7a0ceff03dff2f0090b216d4957.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jzWgsD81--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/1kWHO1ImoYEYmsUmCwya0y/13c7937c0f2d95c794fc120ee04bae46/6_twilio_contentful.png)

一旦我们点击绿色的发布按钮，Polly 的选民信息将获得一个唯一的条目 ID。我们将在通过 [Contentful 的](https://www.contentful.com/developers/docs/references/content-delivery-api/?utm_campaign=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio&utm_medium=referral&utm_source=devto&utm_content=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio) [内容](https://www.contentful.com/developers/docs/references/content-delivery-api/) [交付 API](https://www.contentful.com/developers/docs/references/content-delivery-api/?utm_campaign=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio&utm_medium=referral&utm_source=devto&utm_content=devto-running-a-serverless-voter-campaign-with-contentful-and-twilio) 或 CDA 检索条目时使用这个条目 ID。

要查看条目 ID，请点按“简介”按钮:

[![image alt text](img/e85174e57d0adae44448571263d2afd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZFRnpKZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/3a09Sj3Qc8Q2kcGAKIYmaa/00211ab37dba74956afba11bdda97f1d/7_twilio_contentful.png)

我们的词条是**ykt 2v 3 iyqwwaekwskg 0a**。

### 将我们的应用程序连接到 Contentful

要针对 CDA 进行身份验证，我们需要创建一个 API 密钥，方法是导航到**空间设置**和 **API 密钥**:

[![image alt text](img/cc72a0c8386396eb9ac42b1b612c7d57.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p1OPaA-_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/4I7aQNew3uSI8AuUqCWg4k/4fbdcd477bd2d2ebef0ca75ccc6c2bab/8_twilio_contentful.png)

然后点击**添加 API 密匙**并给你的密匙起一个合适的名字:

[![image alt text](img/635dc599a8fb8fc24a5cb86d54dcd12a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ARayBJuq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/69u6Z4BQPeiMosOsq604os/e606d6c9724a0d2daa03fefdd36ef255/9_twilio_contentful.png)

现在我们有了 API 键，让我们创建一个名为 **getVoterMessage()** 的函数，利用这个 API 键从 Contentful 获取内容。

```
def getVoterMessage():
    SPACE_ID = '1476xanqlrah'
    CONTENT_DELIVERY_API_KEY = 'dae35c068382427841441c61a6370d57101ba94a0f6e6e7cb22ca655c16b7f67'
    ENTRY_ID = 'Ykt2v3ixYQwwAekwSKg0A'
    client = contentfulClient(SPACE_ID, CONTENT_DELIVERY_API_KEY)
    entry = client.entry(ENTRY_ID)
    return entry.message_to_voters 
```

Enter fullscreen mode Exit fullscreen mode

注意，Contentful 的 Python SDK 会将字段名转换成 snake case。这就是为什么我们的 **messageToVoters** 字段在上面的代码片段中被称为 **entry.message_to_voters** 。打着 PEP 8 合规的旗号。

如果我们临时改变我们的 Flask 路径来响应内容，我们将得到如下内容:

```
@app.route("/")
def runVoterCampaign():
    voterMessage = getVoterMessage()
    return voterMessage 
```

Enter fullscreen mode Exit fullscreen mode

我们会得到:

[![image alt text](img/ab85dcadacf81e21b29cb81ae5248444.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JY7eTFbE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/2HEmDOaZ04e8ewsmYK0yay/c8c9bef2ed146a519a0fb2dab2bb0593/10_twilio_contentful.png)

下一步是将我们的应用程序连接到 Twilio 的可编程 SMS API。

### 连接到 Twilio 的可编程 SMS API

因此，我们编写了函数 **getVoterMessage()** 来从 Contentful 获取我们的投票者消息。让我们编写另一个函数来接收这条消息，并以 SMS 的形式发送出去:

```
def sendMessageToVoter(voterMessage):
    ACCOUNT_SID = 'AC046db3e3b2dd0……..'
    AUTH_TOKEN = '88572d13934ee1a5……….'
    client = twilioClient(ACCOUNT_SID, AUTH_TOKEN)
    client.api.account.messages.create(
        to="+15552395",
        from_="+4915375998263",
        body=voterMessage) 
```

Enter fullscreen mode Exit fullscreen mode

为了执行这个函数，我们将把 Flask 路径改为如下:

```
@app.route("/")
def runVoterCampaign():
    voterMessage = getVoterMessage()
    sendMessageToVoter(voterMessage)
    return 'Message sent to voter' 
```

Enter fullscreen mode Exit fullscreen mode

该功能执行几秒钟后，接收方将看到以下短信:

[![image alt text](img/fdab9706667ea7bb85f56eb1849bbf53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5lgyCdEe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/1cBc9pSu4k8QM2acwAAioi/6a117b98e6eb15940c5359fe48de7153/11_twilio_contentful.png)

有了应用程序的核心功能，让我们通过将它部署到 AWS lambda 来使我们的应用程序无服务器化。

### 使用 Zappa 将我们的本地应用部署到 AWS Lambda

AWS Lambda 允许您在不提供或管理服务器的情况下运行代码。到目前为止，我们只在本地运行我们的 Flask 应用程序。

为了将应用程序部署到 AWS，我们将使用 [Zappa](https://www.zappa.io/) 。

像这样安装 Zappa:

```
pip install zappa 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将运行 **zappa init** 来生成一个配置文件:

```
 Welcome to Zappa!

Zappa is a system for running server-less Python web applications on AWS Lambda and AWS API Gateway.
This {% raw %}`init`{% endraw %} command will help you create and configure your new Zappa deployment.

Let's get started!
Your Zappa configuration can support multiple production stages, like 'dev', 'staging', and 'production'.

What do you want to call this environment (default 'dev'): 

AWS Lambda and API Gateway are only available in certain regions. Let's check to make sure you have a profile set up in one that will work.

Okay, using profile default!

Your Zappa deployments will need to be uploaded to a private S3 bucket.
If you don't have a bucket yet, we'll create one for you too.
What do you want to call your bucket? (default 'zappa-w57kc2zrw'): 
It looks like this is a Flask application.
What's the modular path to your app's function?
This will likely be something like 'your_module.app'.
We discovered: app.app
Where is your app's function? (default 'app.app'): 
You can optionally deploy to all available regions in order to provide fast global service.
If you are using Zappa for the first time, you probably don't want to do this!

Would you like to deploy this application globally? (default 'n') [y/n/(p)rimary]: 

Okay, here's your zappa_settings.json:

{
    "dev": {
        "app_function": "app.app",
        "aws_region": "us-east-1",
        "profile_name": "default",
        "project_name": "twiliocontentfu",
        "runtime": "python3.6",
        "s3_bucket": "zappa-w57kc2zrw"
    }
}

Does this look okay? (default 'y') [y/n]: 
```

Enter fullscreen mode Exit fullscreen mode

现在运行 **zappa deploy dev** 将应用程序部署到 AWS Lambda:

```
 Calling **deploy** for stage **dev**..
Creating twiliocontentfu-dev-ZappaLambdaExecutionRole IAM Role..
Creating zappa-permissions policy on twiliocontentfu-dev-ZappaLambdaExecutionRole IAM Role.

Warning! Your project and virtualenv have the same name! You may want to re-create your venv with a new name, or explicitly define a 'project_name', as this may cause errors.

Downloading and installing dependencies..

 - sqlite==python36: Using precompiled lambda package

Packaging project as zip.

Uploading twiliocontentfu-dev-1523880262.zip (16.2MiB)..

100%|███████████████████████████████████████| 17.0M/17.0M [00:32<00:00, 362KB/s]

Scheduling..

Scheduled twiliocontentfu-dev-zappa-keep-warm-handler.keep_warm_callback with expression rate(4 minutes)!

Uploading twiliocontentfu-dev-template-1523880329.json (1.6KiB)..

100%|██████████████████████████████████████| 1.64K/1.64K [00:01<00:00, 1.30KB/s]

Waiting for stack twiliocontentfu-dev to create (this can take a bit)..

100%|████████████████████████████████████████████| 4/4 [00:10<00:00,  2.61s/res]

Deploying API Gateway..

**Deployment complete**!: https://bz9zie872.execute-api.us-east-1.amazonaws.com/dev 
```

Enter fullscreen mode Exit fullscreen mode

部署成功，我们的 Flask 应用程序现在可以在 hx XPS://BZ 9 zie 872 . execute-API . us-east-1 . Amazon AWS . com/dev 上无服务器运行。

下一步是在 Contentful 中配置 webhooks，这样每当发布新的活动消息时。

### 在 Contentful 中设置 webhooks

我们希望设置一个 webhook，这样每当活动消息发生变化，活动经理按下发布按钮时，就会有一个 HTTP POST 请求发送到我们的无服务器 AWS Lambda 应用程序，该应用程序又会发送一条文本消息。

要配置网页挂钩，我们将导航至**空间设置**和**网页挂钩**:

[![image alt text](img/31568de59a9122cb8d20eae9050e057b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cige1Wly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/4H9wD5jC5ayWK64KySqOKg/8cffe3da16a437dffb4cc28e168204d8/12_twilio_contentful.png)

然后点击**添加 webhook** 。

我们将在 hx XPS://BZ 9 zie 872 . execute-API . us-east-1 . Amazon AWS . com/dev 配置 webhook 的目标为我们的 AWS lambda 函数。

此外，我们将设置 webhook 来触发我们点击发布按钮:

[![image alt text](img/b7d5e88c20d790a1ea34e71346db6e1d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dleEyMRj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/4xJ79ZpyMU8EYc04yGacWa/ca8278741c0d74f16a5f21d4de0bcfad/13_twilio_contentful.png)

现在的想法是，当我们改变我们的活动信息，点击发布。更新后的信息将通过手机短信发送给选民，只需按下一个按钮。但是在这之前，我们需要返回到我们的函数 runVoterCampaign。

Contentful 的 webhooks 作为 **HTTP POST** 发送。这意味着我们必须添加 **POST** 方法支持:

```
@app.route("/", methods=['GET', 'POST'])
def runVoterCampaign():
    voterMessage = getVoterMessage()
    sendMessageToVoter(voterMessage)
    return 'Message sent to voter' 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们使用 Zappa 更新我们的无服务器应用程序

```
zappa update dev 
```

Enter fullscreen mode Exit fullscreen mode

为了测试我们的新 webhook，我们将更新活动信息，如下所示:

[![image alt text](img/965279d487b82d319c2cd952dd0b9c1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XTssFK01--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/5iq8BIvzFYUwy4AKQWqAGS/bf97587bb92d811c38161869f64e61e1/14_twilio_contentful.png)

点击绿色的大发布按钮，很快我们的手机就会收到以下消息:

[![image alt text](img/ca4abef54d14984c6a307ddb7aef5c43.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1Dmn0Lqf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/1vj1YUpy8wwSui0w42EgkK/e7656e38b165a7dfc137b2d289b2afe9/15_twilio_contentful.png)

是啊！！！它工作了。很简单吧:)

### 总结

没有太多的努力，我们已经建立了一个解决方案，从 Contentful 获取数据，并通过 Twilio 的可编程 SMS API 将其发送给用户——所有这些都是在无服务器的情况下运行的。

我们还看到了如何配置 webhooks 来触发对 Contentful 存储的内容所做的更改。Webhooks 反过来调用 AWS Lambda 函数。

在本文中，我们构建了一个 Flask 应用程序。现在，将代码部署到 AWS Lambda 可能会异常复杂——但使用 Zappa 可以让将 Flask 应用程序发送到云变得前所未有的简单。

我们所创建的逻辑流程如下所示:

[![Logic flow chart](img/b7cfc5e66f404ebf2d34c4093a8db848.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UHn3AQBW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/1QWmCEzEGwESEqwk2KAmE4/ee4c53c046794270427c569af457897a/16_twilio_contentful.png)

### 前方是什么？

无服务器是未来的发展方向。再也不用担心服务器打补丁了，更不用说保持数千台服务器正常运行的负担了。走向无服务器意味着你可以专注于代码，而不是维护。