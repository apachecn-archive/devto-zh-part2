# webdriverIO 提示:从项目列表中获取文本

> 原文：<https://dev.to/intricatecloud/webdriverio-tips-get-text-from-a-list-of-items-59ln>

有几种方法可以从元素列表中获取文本，但是在使用动态 webapp(其中的元素是从 DOM 中添加/删除的)时会遇到一些问题(比如 react 和 angular)。

最简单的方法应该是:

```
// get some of the headers on wikipedia.org
> browser.getText("#mp-topbanner > ul > li")
[ 'Arts',
  'Biography',
  'Geography',
  'History',
  'Mathematics',
  'Science',
  'Society',
  'Technology',
  'All portals' ] 
```

Enter fullscreen mode Exit fullscreen mode

我在使用`browser.getText(selector)`(以及`waitForVisible`)时经常遇到的一个例外是该死的无效参数错误。这里有一个问题的例子

```
> browser.url('http://webdriver.io')
> browser.getText('nav > ul > li')
[ 'I/O',
  'Home',
  'Developer Guide',
  'API',
  'Contribute',
  '',
  'API Version',
  '' ]
> browser.getText('nav > ul > li')
/Users/dperez/Documents/projects/tchdp/wdio-tips/node_modules/wdio-sync/build/index.js:357
            throw e;
            ^

Error: java.net.SocketException: Invalid argument
    at new RuntimeError (node_modules/webdriverio/build/lib/utils/ErrorHandler.js:143:12)
    at Request._callback (node_modules/webdriverio/build/lib/utils/RequestHandler.js:316:39)
    at Request.self.callback (node_modules/webdriverio/node_modules/request/request.js:185:22)
java.net.SocketException: Invalid argument
[chrome desktop #0-0] Error: An unknown server-side error occurred while processing the command. 
```

Enter fullscreen mode Exit fullscreen mode

对`getText`的第一次调用成功，随后立即运行的第二次调用出错。我认为错误消息来自 Selenium 服务器，它发回了一条关于无效参数的非常有用的消息。当您的选择器返回太多元素时，往往会发生这种情况。在上面的例子中，选择器返回 8/9 个元素。我也看到它即使有 3 个元素也不行，所以这里还有别的东西。

这里有一个解决方法。查询元素并手动循环它们。我发现这个不那么古怪了:

```
> $$("#mp-topbanner > ul > li").map(function(element){
    return element.getAttribute('innerText')
})
[ 'Arts',
  'Biography',
  'Geography',
  'History',
  'Mathematics',
  'Science',
  'Society',
  'Technology',
  'All portals' ] 
```

Enter fullscreen mode Exit fullscreen mode

### 重述

Selenium 可能不可靠，尽管 webdriverio 在使编写测试变得容易得多方面做得很好，但它最终还是要处理 webdriver API。如果你看到的是`SocketException: Invalid argument`，最好跳过`getText`的便利性，循环遍历你的元素。

* * *

上周，我开始整合之前使用 Nightwatch 构建的测试套件，并让它与 webdriverIO 一起工作。虽然我喜欢 webdriverIO 的所有功能，如使用测试运行器和 REPL 时的同步代码，但我想分享一些在文档或快速搜索中很难找到的东西。

以防你错过了...这个星期的每一天，我都在张贴我在建立 webdriverIO 时学到的一件事。点击这里查看我以前的帖子:

*   [用$$。(选择器)vs browser.elements(选择器)](https://www.intricatecloud.io/2018/11/webdriverio-tips-using-selector-vs-browser-elementsselector/)
*   [使用 browser.debug()帮助调试您的测试](https://www.intricatecloud.io/2018/11/webdriverio-tips-using-browser-debug-to-help-debug-your-tests/)