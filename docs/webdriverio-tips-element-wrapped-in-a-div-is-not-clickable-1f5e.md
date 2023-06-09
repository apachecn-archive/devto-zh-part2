# webdriverIO 提示:包装在 div 中的元素是不可点击的

> 原文：<https://dev.to/intricatecloud/webdriverio-tips-element-wrapped-in-a-div-is-not-clickable-1f5e>

当你试着点击一个按钮时，你是否遇到过“元素在某点不可点击”的错误？如果您的按钮上出现了一个微调器，或者它是一个自定义按钮(比如一个 div 按钮，带有内部样式元素)，您可能会看到这种情况。在这些情况下，如果您希望 selenium 能够做到这一点，那您就有点不走运了，因为它看不到您想要单击的元素。然而...你可以绕过它。虽然 selenium 不能点击被其他元素遮挡的按钮，但浏览器仍然可以这样做，selenium 可以注入 javascript 在浏览器中运行。看看这个例子:

```
var runInBrowser = function(argument) { 
  argument.click();
};
var elementToClickOn = browser.$(selector)
> browser.execute(runInBrowser, elementToClickOn); 
```

Enter fullscreen mode Exit fullscreen mode

[。执行](http://webdriver.io/api/protocol/execute.html)来此救援。您可以在页面中插入一个代码片段，因此只要浏览器能够做到这一点，您就可以克服“元素不可点击”的错误。虽然这是一个小技巧，但是你应该只在需要的时候谨慎地使用它，大多数时候应该仍然有效。[如果你遇到这种情况，请点击这里查看堆栈溢出讨论](https://stackoverflow.com/questions/11908249/debugging-element-is-not-clickable-at-point-error)

* * *

上周，我开始整合之前使用 Nightwatch 构建的测试套件，并让它与 webdriverIO 一起工作。虽然我喜欢 webdriverIO 的所有功能，如使用测试运行器和 REPL 时的同步代码，但我想分享一些在文档或快速搜索中很难找到的东西。

以防你错过了...这个星期的每一天，我都在张贴我在建立 webdriverIO 时学到的一件事。点击这里查看我以前的帖子:

*   [用$$。(选择器)vs browser.elements(选择器)](https://www.intricatecloud.io/2018/11/webdriverio-tips-using-selector-vs-browser-elementsselector/)
*   [使用 browser.debug()帮助调试您的测试](https://www.intricatecloud.io/2018/11/webdriverio-tips-using-browser-debug-to-help-debug-your-tests/)
*   [从项目列表中获取文本](https://www.intricatecloud.io/2018/11/webdriverio-tips-get-text-from-a-list-of-items/)
*   [使用 waitUntil 时发现错误](https://www.intricatecloud.io/2018/11/webdriverio-tips-finding-your-errors-when-using-waituntil/)