# 你最可能见过的 15 个常见硒异常

> 原文：<https://dev.to/katalon/top-15-common-selenium-exceptions-you-have-most-likely-seen-5gjk>

在 **[Selenium](http://seleniumhq.com)** 的每个自动化测试过程中，你可能会遇到很多不常见的场景。这些被称为“异常”，当你执行你的测试用例时是不可避免的，即使使用其他自动化测试工具，比如 **[Katalon Studio](https://www.katalon.com)** 。然而，如果你知道如何恰当地处理它们，你就会实现它们。

本文将提供对异常处理的一些更好的理解。

## 什么是硒的例外？

### 定义

异常被认为是在软件程序或应用程序执行过程中发生的异常或前所未有的事件。它是一个意外结果或事件的运行时错误，会影响和扰乱正常的程序流程。异常也被认为是故障。

### 硒例外的分类

**硒异常** 分为检查异常和未检查异常两种。

1。检查异常

在编写代码的过程中处理检查到的异常。这些异常在编译代码之前被处理，因此，这些异常在编译时被检查。

2。未检查的异常

这些异常在运行时抛出。未经检查的异常比编译时异常更具灾难性，因为它会在 headless 中运行 Automation pack 时导致问题。

## Selenium 中常见异常的列表

**1。ConnectionClosedException:** 这个异常发生在驱动程序断开连接的时候。

**2。ElementClickInterceptedException:**命令无法完成，因为接收事件的元素隐藏了请求单击的元素。

**3。ElementNotInteractableException:**当一个元素出现在 DOM 中但无法与该元素交互时，抛出 Selenium 异常。

**4。ElementNotSelectableException:**当一个元素出现在 DOM 中但您可以选择时，抛出 Selenium 异常。因此，无法与互动。

**5。InvalidCoordinatesException:**如果提供给交互操作的坐标无效，就会发生这种情况。

**6。InvalidElementStateException:**如果命令因元素无效而无法完成，就会出现 Selenium 异常。

**7。InvalidSessionIdException:** 当给定的会话 ID 不在活动会话列表中时发生，这意味着该会话不存在或者不活动。

**8。InvalidSwitchToTargetException:**当要切换的框架或窗口目标不存在时发生。

**9。JavascriptException:** 这个问题发生在执行用户提供的 JavaScript 时。

10。JsonException: 当您在没有创建会话的情况下获得会话功能时，就会发生这种情况。

**11。如果提供给 ActionChains move()方法的目标无效，则会发生 MoveTargetOutOfBoundsException:**。例如:文档外。

**12。NoAlertPresentException:** 当您切换到无提示警报时发生。

13。NoSuchAttributeException: 当找不到元素的属性时发生。

**14。UnknownMethodException:** 当请求的命令与已知的 URL 匹配，但与该 URL 的方法不匹配时抛出。

15。UnreachableBrowserException:如果浏览器因为某些原因无法打开或者已经崩溃，就会发生这个 Selenium 异常。

## 结论

上面提到的所有异常都是每个自动化项目中最常见的情况。因此，你可以明智地处理异常，因为你掌握了所有清晰的解释。我们希望您将这篇文章作为在 Selenium 和 Katalon Studio 中管理异常和优化代码的参考。

更多阅读: **[硒中例外完整列表](https://www.katalon.com/resources-center/blog/selenium-exceptions/)**