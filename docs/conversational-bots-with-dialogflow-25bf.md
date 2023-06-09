# 带 Dialogflow 的对话机器人

> 原文：<https://dev.to/committedsw/conversational-bots-with-dialogflow-25bf>

[![](img/08e6ff5350cd9659685be5d012d8862a.png)T2】](///static/google-home-mini-0f63541f5ca4f55b776e67ed87a255ce-be74e.jpeg)

# 用 Dialogflow 构建对话机器人

对话机器人正变得越来越受欢迎，大多数手机、亚马逊 Alexa 和谷歌 Home 等设备以及 Slack 和 Facebook Messenger 等各种聊天应用程序中都有人工智能助手。然而，开发对话机器人会产生许多问题:

*   语音识别来理解用户的口头请求。
*   提取查询中包含的查询参数。
*   理解查询的意图以确定正确的操作。
*   为查询生成适当的响应。
*   与越来越多的辅助设备或聊天应用程序集成。

每个平台通常都有自己建议的开发聊天机器人的方法，例如 Slack 提供了各种 API，并建议使用 JS 库，如[“Botkit”](https://botkit.ai/)，而亚马逊提供了[“Alexa 技能工具包”](https://developer.amazon.com/alexa-skills-kit)和[“AWS Lambda 函数”](https://aws.amazon.com/lambda/)。理想情况下，我们不想为每个设备支持多个解决方案，那么我们如何编写一个对话机器人，同时与各种平台集成呢？

## 安静

Google 的 Dialogflow 平台为我们提供了合适的解决方案；处理查询解析、参数提取、意图检测，并提供与各种常见应用程序的轻松集成。

### 意图

Dialogflow 允许我们定义不同的意图，它将匹配传入的查询。我们定义了触发我们意图的短语示例，Dialogflow 将使用这些示例来训练用于查询识别的模型。提供的示例查询越多，查询识别就越准确。

我们还可以说明应该从每个请求中提取的参数。对于每个参数，我们指定其名称、类型和默认值。Dialogflow 为数字、日期或位置等常见类型提供了许多现有类型，同时还允许我们定义自己的实体类型。

[![](img/037aed71baef9bb979ea2b519eb2a3d3.png)T2】](///static/Intent-3537221a7fcc2749e3efb7a61e238fb6-59a72.png)

我们声明每个参数是否是必需的，并且我们还可以在用户没有提供所有必需的参数时提供提示。在上面的例子中，关于午餐的问题需要一个 FoodType 参数。

> 用户:“我该去哪里吃午饭？”
> 
> Bot:“有什么偏好吗？”
> 
> 用户:“一个汉堡”

在继续构建响应之前，Dialogflow 会利用我们提供的提示，以对话方式自动处理这些参数填充交换。

### 响应创建

一旦用户的意图被确定，我们需要为他们的请求提供一个响应。Dialogflow 允许我们在浏览器中提供基本的文本响应，针对不同的平台(如 Google Assistant 或 Slack)提供特定的响应。

[![](img/005b476871458a364d7463830393e5c4.png)T2】](///static/Response-ab784c4e3c1f734a176c6a85119d5388-5082f.png)

然而，我们可能希望提供一个定制的响应，为此 Dialogflow 提供了与 Firebase 云功能的集成。它再次允许我们完全在浏览器中编辑和部署该功能。Dialogflow 为我们提供了一个模板化的函数来进行编辑，在这个函数中，我们编写了我们的请求实现代码，并为 Dialogflow 提供了一个映射，说明了处理每个意图时要调用的函数。

对于每个意图处理程序，我们可以使用所提供的`request`来提取特定的参数并构建定制的响应。我们还可以检测请求源，以便构建特定于该平台的响应，例如向 Google Assistant 响应添加卡片，或者利用 Slack 的样式化响应。

```
exports.dialogflowFirebaseFulfillment = functions.https.onRequest((request, response) => {
  const agent = new WebhookClient({ request, response });

  function lunchHandler(agent) {
    var source = request.body.originalDetectIntentRequest ? request.body.originalDetectIntentRequest.source : undefined

    // Your code here - using request.body parameters to build a response

    switch(source){
        case 'slack': buildSlackResponse(agent); break;
        case 'google': buildGoogleAssistantResponse(agent); break;
        default: agent.add("Default response"); break;
    }

    // Add context for chained intents to use
    agent.setContext({ name: 'preference', lifespan: 2, parameters: { foodType: 'burger' }});
  }

  function buildSlackResponse(agent){
      // Building slack specific response
  }

  function buildGoogleAssistantResponse(agent){
      // Building Google Assistant cards
  }

  // Run the proper function handler based on the matched Dialogflow intent name
  let intentMap = new Map();
  intentMap.set('Lunch Intent', lunchHandler)
  agent.handleRequest(intentMap);
}); 
```

我们可能希望从我们的云函数中发出异步请求，调用 API 或从数据存储中请求数据来填充响应。在这种情况下，Dialogflow 允许我们从意图处理函数返回一个承诺。Dialogflow 将在处理代理响应之前等待承诺解决。

```
function lunchHandler(agent) {
    return Promise.resolve(someAsyncRequest()
      .then(function (res) {
        agent.add("Here is a service specific response")
      })
      .catch(function (err) {
        console.log(err.stack)
        agent.add('Sorry, the service is not available right now')
      })
    );
} 
```

### 集成

Dialogflow 的主要优势之一是其应用程序集成。Dialogflow 提供了与谷歌助手、Facebook Messenger、Slack、Twitter、Alexa、Skype 等的简单集成。在大多数情况下，这些集成的设置非常简单，很多情况下都是激活集成并提供两个服务通信所需的一组凭证。在每种情况下，Dialogflow 都提供了一组简单的步骤来设置集成。

[![](img/44aeb341bbc3f4131799dd448f4e4fb6.png)T2】](///static/Facebook-aa46daefb118bde939fc7bdaff80c8f1-ed1e1.png)

一旦建立了集成，来自该平台的任何查询请求都将被转发到 Dialogflow 进行处理并返回结果，来自每个平台的所有请求都由相同的底层服务处理。

### 包装完毕

Dialogflow 非常容易使用，提供基本响应的对话机器人可以完全在浏览器中创建，而不必下载或设置任何开发工具包，也不必编写一行代码。一旦用户需要产生复杂的响应，他们只需要任何编程知识。

它的许多集成看起来确实是它的主要优势，允许开发者用一个单一的服务支持广泛的客户提供了一个可维护的解决方案。每个请求都由相同的解析服务和参数提取来处理，让开发人员只关心生成合适的用户响应。

与大多数适合所有解决方案一样，Dialogflow 也有其缺点。许多应用程序在其聊天机器人中提供不同的功能，支持大量集成可能意味着无法利用这些平台特定的功能。例如，在撰写本文时，Slack 集成和对直接@Bot 提及的响应存在问题。

然而，Dialogflow 正在不断更新和改进，新功能正在逐步实现，因此这些问题可能会随着时间的推移而得到解决。

鸣谢:[“本·科尔德”](https://unsplash.com/photos/d6dxQwmxV2Q?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[“Unsplash”](https://unsplash.com/search/photos/google?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上的博客照片