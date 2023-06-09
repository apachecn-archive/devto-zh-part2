# 对芯片大喊大叫(第一部分)

> 原文：<https://dev.to/washingtonsteven/yelling-at-silicon-part-1-2eef>

近来，各种声控家庭助手在日常生活中的使用越来越多。它从 Siri 开始，谷歌随后推出了他们的助手。Alexa 出现了，甚至 Cortana 也尝试了一下。

这些助手有一套很好的特性和功能，可以帮助你自动化你的生活:设置定时器，播放音乐，开灯关灯，设置恒温器等。都很有帮助！然而，不管有多少功能，永远不会有一个通用的解决方案。

所以是时候做一个了。

*跟着我目前在[Github](https://github.com/washingtonsteven/thorndike)T3】中的想法玩下去*

## 一款公交追踪 app

我典型的早晨包括一些平常的事情:醒来，躺在床上 20 分钟查看 Twitter，匆匆吃完早餐/冲完澡/穿好衣服准时出门。其中一部分包括暂停整个过程，因为我摆弄着我的手机，从一个公交车跟踪应用程序(在这种情况下是 [Nextbus](https://nextbus.com) )中调出预测。如果我可以在烤百吉饼或系鞋带时向手机(或谷歌主页)询问我家附近的最新公交车，那就简单多了。

首先，我构建了一个与 next bus API 接口的快速函数。它接受一个停止名，并返回一个包含我需要的预测数据的对象:

```
// Nextbus needs a stopId to look up predictions
// We have an object that maps stopNames to stopIds
const stops = {
  'harvard':'55555'
  // other stops here...
}

exports.getPredictions = stopName => {
  const stopId = stops[stopName];

  const nextbusURL = `http://webservices.nextbus.com/service/publicXMLFeed?command=predictions&a=mbta&stopId=${stopId}`;

  // The Nextbus API is an XML api, so we use the xml2js package
  // to convert that to JSON
  // Then we have a parseSchedule function to clean up that
  // JSON, only returning the results we want
  return request(nextbusURL)
    .then(xml => xml2js(xml))
    .then(json => parseSchedule(json))
}

const parseSchedule = json => {
  const cleanedUp = [];

  // Take `json` and extract only the info we care about
  // In the end, cleanedUp will be a multidimensional array of sentences, like:
  // [
  //   ['The next 77 to Arlington Heights at Harvard is arriving in 5 minutes'],
  //   ['The next 96 to Medford Square at Harvard is arriving in 8 minutes']
  // ]

  return cleanedUp;
} 
```

Enter fullscreen mode Exit fullscreen mode

*如果你在跟踪 [Github repo](https://github.com/washingtonsteven/thorndike) ，你会注意到我只得到每个路线/方向组合的第一个预测。稍微移动一下这个函数，我们可以得到每条路线的多组预测，我们可以将它们附加到内部数组*

## 造句- `filter`和`join`

在我们可以将这些数据发送给任何语音助手之前，我们需要制作实际的、可口述的句子。

来自`parseSchedule`的`cleanedUp`对象是一个多维数组。第一个维度是一组路线(公交车号)，第二个维度是该路线的一组预测。因为这些是在一个数组中，所以我们可以使用一些新的 ES6 函数将它解析成每条路线的一组句子。

注意:没有预测的路线将显示为空数组，所以我们必须`filter`将它们取出。没有值的预测将显示为空字符串，我们也可以过滤掉它们(甚至对两者使用相同的`length`检查！)

在实际的回购中，我将它设置为一行程序，但它在下面进行了扩展/注释:

```
exports.stringifyPredictions = (results, joiner = ". ") => {
  let resultsMessage = results.map(predictionList => {

    // predicitonList is a set of predictions for a route
    // Filter out the empty ones (empty strings), then join them together
    return predictionList.filter(l => l.length > 0).join(joiner)
  })

  // Filter and join again on the set of routes
  // Filtering out empty routes (which show up here as empty arrays)
  resultsMessage = resultsMessage.filter(l => l.length > 0).join(joiner);

  return resultsMessage;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使其可上网

为了工作，Google Assistant 必须能够通过 web 访问我们的服务，我们将返回我们希望它说的内容作为 JSON 响应。我使用 [Express](https://expressjs.com) 建立一个服务器来监听来自 Google 的查询，并托管在 Heroku 上。

```
const app = express();
app.post('/thorndike', (req, res) => {

  // See below for the contents of googleAction.js
  require('./googleAction')(req, res);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 与 Google Assistant 对话-在 Google 和 DialogFlow 上的操作

现在我们有了获取预测句子的功能，我们需要连接 Google Assistant 来响应我们的声音，并通过我们的服务来获取预测集。为此，我们使用来自`npm`的 [`actions-on-google`](https://www.npmjs.com/package/actions-on-google) 模块以及[对话流](https://dialogflow.com)。

在进入代码之前，我们需要在 Google 开发者控制台和 Dialogflow 上设置我们的应用程序。Dialogflow 是 Google Assistant 和我们的应用程序之间的粘合剂，它从 Google Assistant 接收解析后的语音输入，并将通过 webhook 调用我们的应用程序(他们称之为“实现”)作为响应。[我使用这份文件来完成所有的设置](https://developers.google.com/actions/dialogflow/first-app)。它回顾了 Google Assistant“对话”的一些概念，并遍历了 Dialogflow web 控制台。

所述设置的一部分是做出服务可以响应的“动作”。这可以是标识正在发生的事情的任何类型的字符串；我们只需要记住当我们开始编码我们的反应时。另一个重要的部分是“论点”，它是口语短语中可以变化的一部分。我们可以提取该文本，并使用它来通知我们正在寻找哪辆公交车。例如“嘿谷歌，附近有什么公交车**哈佛**？”这两个都将被发送到我们的请求处理程序，这是我们将要处理的下一段代码！

## 用`actions-on-google`响应请求

[`actions-on-google`模块](https://www.npmjs.com/package/actions-on-google)的工作原理是解析从 Google Assistant/Dialog flow 收到的请求，并根据发送的动作调用一个函数。这是通过建立一个连接动作字符串和函数的映射来实现的。从那里我们只需要调用`getPredictions` / `stringifyPredictions`来得到我们的预测句子，然后把它传递给`actions-on-google`给我们的`tell`函数！

```
// googleAction.js

const App = require('actions-on-google').DialogflowApp;
const { getPredictions, stringifyPredictions } = require('./getPredictions');

// This is the action we set up in DialogFlow
const ASK_ACTION = 'ask_stop_by_name';

// We set up a parameter called stop_name, that is filled in
// when the voice input is parsed.
const STOP_NAME_ARGUMENT = 'stop_name';

module.exports = (request, response) => {
  const app = new App({request, response});

  let actionMap = new Map();
  actionMap.set(ASK_ACTION, app => {

    // getArgument() is a function from 
    // `actions-on-google` / `DialogflowApp` that can parse
    // out the argument from the voice input.
    let stopName = app.getArgument(STOP_NAME_ARGUMENT);

    getPredictions(stopName)
      .then(results => stringifyPredictions(results))
      .then(resultsMessage => {

        // stringifyPredictions returns 'false' if a stop exists, but has no buses.
        if (resultsMessage === false) {

          // app.tell literally makes Google Assistant speak!
          app.tell(`There don't seem to be any buses at ${stopName}`);
        }

        // any other falsy value means that the app doesn't know about that stop
        else if (!resultsMessage) {
          app.tell(`Sorry, I don't have information for ${stopName}`);
        }

        // We got some predictions!
        else {
          app.tell(resultsMessage);
        }
      });
  });

  // The App, built based on the request/response we received,
  // will take in our map, figure out the action based
  // on what Dialogflow sent us, and then call the
  // relevant function.
  app.handleRequest(actionMap);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 与谷歌助手交谈

现在我们已经设置好了我们的 web 应用程序(并托管了它，webhook 也放入了 Dialogflow ),我们可以开始与我们新的 Google Assistant 应用程序对话了！只需设置一些东西，就能让与谷歌的交流变得更好。

### 重命名您的应用程序

在设置 Dialogflow 时，它在谷歌控制台的[动作中创建了一个应用。我们可以在这里登录并更新应用程序信息，如应用程序名称和发音，这样我们就可以通过名称与我们的服务进行对话。我把我的车叫做“桑代克下一辆车”(一会儿再谈命名)。](https://console.actions.google.com/)

此屏幕用于输入您的应用程序元数据，以便发布到 Google Actions Marketplace 供所有人使用。然而，我目前还没有计划对此进行公开发布；我只想用我的账户。但由于我们正在有效地准备一个公共应用程序(只是不发布它)，我们有一些限制。

*   有许多必填字段不适用，大多与营销形象和文案相关。您可以将这些内容留空，您的信息将会保存，但是每次您返回进行更新时都会收到警告
*   由于这意味着公开发布，你的应用程序的名称必须是唯一的(也不能与一个已建立的品牌相关，因为谷歌会提示你证明你是代表该品牌工作，而你可能不是)。这意味着你的应用程序的名字可能必须是一个别扭的短语，而不是一个单词。
*   由于我们没有“发布”我们的谷歌助手应用程序，她会不断告诉我们，我们正在使用我们应用程序的“测试版本”。哦好吧。

### 在浏览器中测试

编辑应用程序信息后，您可以点击“测试草稿”在浏览器中测试响应。你会在左边看到一个看起来像 Google Assisant Android 的屏幕，在右边看到关于你的测试的各种数据。在这里，你可以键入你期望的语音输入，然后看看谷歌助手如何响应。

[![Testing Thorndike Next Bus](img/75a76102bf031b4c946505d4b9ad5da4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9DI4eAtM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s1k58y2rqskfdt4xpd05.png) 
*成功了！*

### 用声音测试

现在一切都部署好了，你实际上可以和 Google Assistant 对话了(只要你登录的是你制作应用程序时使用的那个 Google 帐户),然后按照上面看到的类似对话进行操作。只要说“嘿，谷歌，说话”，你就可以开始了。

### 感觉笨重？是啊，有点像。

Google works 上的 was 操作是通过“对话”的概念实现的。这对于与人工智能对话来说非常好。然而，事实上，你必须检索第二个“助手”来获取信息(而不是来自谷歌助手本身的信息)，感觉就像是这个过程中的一个小问题。最后，它向一个可以缩短的过程添加了一个额外的调用/响应“嘿，Google，获得对 X bus 的预测。”走“对话”路线有点长，但它有完全不用手的好处。

## 未来

这种增强谷歌助手的方式非常适合与人工智能建立对话，以与不“标准”的数据源进行交互。只需做一点工作，将数据塑造成一个句子，然后当谷歌用一些语音输入敲你的服务器时，给出一个响应。然而，有时这些对话可能只是为了获取一点数据而显得笨拙。如果有另外一种获取数据的方法就好了，只需一句话，甚至按一下按钮…

哦，等等，还有呢！在后续文章中，我将把同一个公交追踪应用程序连接到 [IFTTT 的谷歌助手集成](https://ifttt.com/google_assistant)，这将允许我们以多种不同的方式访问我们的数据，而不是谷歌的对话。