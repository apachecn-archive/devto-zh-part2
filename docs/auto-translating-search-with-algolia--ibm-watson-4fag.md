# 使用 Algolia & IBM Watson 自动翻译搜索

> 原文：<https://dev.to/martyndavies/auto-translating-search-with-algolia--ibm-watson-4fag>

开箱即用，Algolia 支持多语言的[搜索，甚至在同一个索引上。如果你已经准备好了所有的翻译，那就太棒了，但是如果你没有呢？](https://www.algolia.com/doc/tutorials/full-text-search/multi-language-search/multilingual-search/)

众所周知，如果你有世界其他地方的用户，他们会感谢你为他们提供母语支持所做的任何努力。

我将向您展示如何使用 [IBM Watson 的语言翻译服务](https://www.ibm.com/watson/services/language-translator/)来自动翻译您的关键字搜索字段，并将翻译内容写回到您的索引中。

在这个例子中，我们将使用 NodeJS，但是无论您在服务器端使用什么语言，这个概念都适用。为了突出这一点，我在 GitHub 库中包含了一个用 Go 编写的例子[。](https://github.com/martyndavies/language-translation-algolia/blob/master/go/translate.go)

## 上下文

您真的可以决定要翻译哪个文本，但是对于这个示例，让我们假设我们有一个充满假日租赁属性的 Algolia 索引，并且初始对象看起来像这样:

```
{  "apartment_name":  "Maison Majestique",  "city":  "Toulouse",  "country":  "France",  "description_en":  "Three bedrooms and two bathrooms. Located 5 minutes walk to all major tourism areas.",  "objectID":  "60329230"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，所有的描述都是英文的，但是来自西班牙的流量越来越多。在我们的 Algolia 仪表盘上，我们看到越来越多的西班牙语搜索尝试。

因此，将描述从英语自动翻译成西班牙语的时候到了。

## 先决条件

对于所有这些例子，以及您可以带走和使用的脚本，我们使用的是 IBM Watson 语言翻译服务。

为了自己使用它，你需要[向 IBM Coud](https://console.bluemix.net/) 注册，然后[创建一个新的语言翻译实例](https://console.bluemix.net/catalog/services/language-translator)。

一旦准备好了，您就需要拿起凭证并把它们放在手边。不幸的是，IBM 控制台中的用户体验并没有想象中那么简单，所以为了帮助您，下面是您正在寻找的内容:

[![IBM Credentials](../Images/75adbdeaa96abc97490bf61e02732152.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--67hdu6E9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/2t1v332S2l1n/Image%25202018-05-02%2520at%25205.23.42%2520pm.png)

我们开始吃吧，好吗？

## 译者如何工作

IBM 为几乎每种语言都提供了一系列功能齐全的 SDK。然而，以前使用他们的 NodeJS SDK 的经验告诉我，在翻译服务的情况下，向他们的 API 发出一个标准的 HTTP 请求需要大约相同数量的代码，并且返回结果可能更快。

下面是一个使用 Axios 作为 NodeJS 中 HTTP 请求模块的翻译请求的例子:

```
var axios = require('axios');

axios({
  method: 'post',
  url: 'https://gateway.watsonplatform.net/language-translator/api/v2/translate',
  data: {
    text: 'I am text, please translate me', // The words to be translated
    source: 'en', // The language they are in
    target: 'es' // The language you want them to be
  },
  headers: { Accept: 'application/json' },
  auth: { username: "ibm_username", password: "ibm_password" }
})
.then(function(response) {
  console.log(response);
})
.catch(function(err) {
  console.log(err);
}); 
```

Enter fullscreen mode Exit fullscreen mode

从这个请求返回的响应包含一个对象数组，其中一个是您正在寻找的翻译文本。

不管出于什么原因，它嵌套得相当深，所以要真正提取文本字符串，您应该寻找这个:

```
...
function(response) {
  var translation = response.data.translations[0].translation
} 
```

Enter fullscreen mode Exit fullscreen mode

😐

无论哪种方式，代码量都不是很大。所以很快我们就开始翻译了。

那么，我们如何将这些信息带回阿尔戈利亚，并开始用它来打动我们的观众呢？

有许多不同的方法可以做到这一点，我们来看看其中的两种。

第一种是在索引后立即用新的翻译更新对象:

## 示例-使用 waitTask

Algolia 提供了一个名为`waitTask`的方法，允许你在执行下一行代码之前确保一个对象已经被索引。

您可以使用这种方式来触发对对象的操作，例如添加一个新字段，其中包含一个已翻译的字符串: