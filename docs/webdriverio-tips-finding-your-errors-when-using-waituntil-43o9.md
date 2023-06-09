# webdriverIO 提示:使用 waitUntil 时发现错误

> 原文：<https://dev.to/intricatecloud/webdriverio-tips-finding-your-errors-when-using-waituntil-43o9>

如果你正在加载一个页面，并且想要确保一些元素在前进之前显示出来，你会倾向于使用`browser.waitUntil()`。虽然它确实完成了工作，但它会保留错误，直到测试超时。

在这个例子中，我想使用`waitUntil`来检查多个元素是否可见

```
browser.waitUntil(function() {
  return doesNotExist.$$('#elem-1').isVisible() 
    && browser.$$('#elem-2').isVisible()
}) 
```

Enter fullscreen mode Exit fullscreen mode

下面是我在测试中运行它时看到的情况:

```
☁  wdio-tips  wdio

F

0 passing (15.30s)
1 failing

1) a testsuite1 runs:
Failed: Promise was rejected with the following reason: doesNotExist is not defined
running firefox
error properties: Object({ details: undefined, type: 'WaitUntilTimeoutError', shotTaken: true })
Error: Promise was rejected with the following reason: doesNotExist is not defined
    at waitUntil(<Function>) - index.js:312:3 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的关键是，在它因错误而放弃之前，测试时间为 15.3 秒。`waitUntil`以一个时间间隔(默认为 500 毫秒)运行您的功能，总共 10 秒钟的超时时间。这意味着这个函数在这 10 秒钟内已经运行了 20 次，但是只有当它超时时，你才会在最后看到错误消息。

令人烦恼的是获得反馈需要花费大量的时间，但是一旦你看到错误消息，你可以修复它，并仔细检查其余的语法错误，这样你就不会花时间等待反馈。我认为这就是使用同步 API 使用`browser.waitUntil`的问题所在。

* * *

上周，我开始整合之前使用 Nightwatch 构建的测试套件，并让它与 webdriverIO 一起工作。虽然我喜欢 webdriverIO 的所有功能，如使用测试运行器和 REPL 时的同步代码，但我想分享一些在文档或快速搜索中很难找到的东西。

以防你错过了...这个星期的每一天，我都在张贴我在建立 webdriverIO 时学到的一件事。点击这里查看我以前的帖子:

*   [用$$。(选择器)vs browser.elements(选择器)](https://www.intricatecloud.io/2018/11/webdriverio-tips-using-selector-vs-browser-elementsselector/)
*   [使用 browser.debug()帮助调试您的测试](https://www.intricatecloud.io/2018/11/webdriverio-tips-using-browser-debug-to-help-debug-your-tests/)
*   [从项目列表中获取文本](https://www.intricatecloud.io/2018/11/webdriverio-tips-get-text-from-a-list-of-items/)