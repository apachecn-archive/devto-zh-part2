# webdriverIO 提示:使用$$(选择器)与 browser.elements(选择器)

> 原文：<https://dev.to/intricatecloud/webdriverio-tips-using-selector-vs-browserelementsselector-3c36>

上周，我开始整合之前使用 Nightwatch 构建的测试套件，并让它与 webdriverIO 一起工作。虽然我喜欢 webdriverIO 的所有功能，如使用测试运行器和 REPL 时的同步代码，但我想分享一些在文档或快速搜索中很难找到的东西。

本周的每一天，我都将发布一件我在开始使用 webdriverIO 时学到的事情。这是今天的小费

## 用$$。选择器与浏览器.元素(选择器)

我在测试中处理的第一个用例之一是找到特定 CSS 类的所有元素，然后对它们做一些事情。[这些文件似乎表明](http://webdriver.io/api/utility/%24%24.html)`browser.$$(selector)`是`browser.elements(selector)`的简写，它们*似乎*是同义词，除了一个小的区别:

`$$(selector)`将返回你可以采取行动的项目，如
上的`.click()``.elements(selector)`返回一个 WebElement JSON 对象([在 github 问题上看到更多关于它的信息](https://github.com/webdriverio/webdriverio/issues/2728))——但它基本上是一个 JSON 对象，包含一个 ID(看起来像一个十进制数)。

如果您尝试使用类似于`browser.elements(selector)[0].click()`的东西点击一个组中的第一个元素，您可能会得到类似于`Can't call click on undefined`的错误，因为它不返回一个数组，而是返回一个包含一个名为 value 的键的对象，该键具有这些元素的 id。

这里有一个`$$(selector)`
的值的例子

```
> browser.$$(".link-gray-dark")
[ { ELEMENT: '0.15821111822631395-1',
    'element-6066-11e4-a52e-4f735466cecf': '0.15821111822631395-1',
    value: { ELEMENT: '0.15821111822631395-1' },
    selector: '.link-gray-dark',
    index: 0 },
  { ELEMENT: '0.15821111822631395-2',
    'element-6066-11e4-a52e-4f735466cecf': '0.15821111822631395-2',
    value: { ELEMENT: '0.15821111822631395-2' },
    selector: '.link-gray-dark',
    index: 1 },
    .... 
```

Enter fullscreen mode Exit fullscreen mode

现在比较一下`browser.elements(selector)`和

```
> browser.elements(".link-gray-dark")
{ sessionId: '651d1e513eb87326a67969d65bbd597c',
  value:
   [ { ELEMENT: '0.15821111822631395-1',
       'element-6066-11e4-a52e-4f735466cecf': '0.15821111822631395-1',
       selector: '.link-gray-dark',
       value: [Object],
       index: 0 },
     { ELEMENT: '0.15821111822631395-2',
       'element-6066-11e4-a52e-4f735466cecf': '0.15821111822631395-2',
       selector: '.link-gray-dark',
       value: [Object],
       index: 1 }, 
```

Enter fullscreen mode Exit fullscreen mode

在很大程度上，我可以用`$$(selector)`做任何我想做的事情，因为我通常会尝试找到一组元素，点击它们或者在其中一些元素上输入文本。

您可能希望使用`browser.elements(selector)`的一个原因是，如果您需要使用 webdriver 协议方法之一，如`elementIdClick(id)`或`elementIdName(id)`和`elementIdScreenshot(id)`(它将只截取您想要的元素的屏幕截图，而不是整个页面，非常简单！).Webdriver 为您提供了一个很好的 API，因此您通常不需要使用这些方法，但是如果您确实需要使用它们，那里有一些很好的特性。

### 重述

我更喜欢使用`$$(selector)`,因为它对你的大多数用例来说已经足够了。

如果你需要访问一些`elementId*`方法，比如 [`elementIdClick(id)`，它们显示在](http://webdriver.io/api/protocol/elementIdClick.html)文档的协议部分，然后使用`browser.elements(selector)`。