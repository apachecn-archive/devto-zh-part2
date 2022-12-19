# Selenium WebDriver 中的 CSS 选择器及示例

> 原文：<https://dev.to/skptricks/css-selectors-in-selenium-webdriver-with-examples-500k>

贴子链接:[Selenium web driver 中的 CSS 选择器及示例](https://www.skptricks.com/2018/04/css-selectors-in-selenium-webdriver-with-example.html)

CSS 选择器是元素选择器和选择器值的组合，用于标识网页中的 web 元素。元素选择器和选择器值的组合称为选择器模式。选择器模式是使用 HTML 标签、属性及其值构建的。创建 CSS 选择器和 Xpath 的过程背后的主题非常相似，唯一的区别是它们的构造协议。

[![](../Images/0f68ab4572df0ef2f030dadffb942aa6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h3QGxtO4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-FG7qr5LzZ3E/WsLpGE_XS0I/AAAAAAAABew/Wo8leGFelug2lR6HbchOtbz4Sli2Zks8QCLcBGAs/s400/css%252Bselectors.jpg)

在这篇文章中，我们将讨论如何在 selenium WebDriver java 中使用 css 选择器。大多数自动化测试人员认为，与 XPath locator 相比，使用 CSS 选择器可以更快地执行脚本。CSS 选择器定位器始终是在页面上定位元素的最佳方式。不管浏览器如何，CSS 总是一样的。

要了解更多关于 CSS 部门的信息，请访问下面的页面:
[https://www.w3schools.com/cssref/css_selectors.asp](https://www.w3schools.com/cssref/css_selectors.asp)

使用 CSS 选择器的一些好处:
CSS 选择器速度更快(尤其是在 IE 中)。
CSS 选择器可读性更强。
就性能而言，CSS 比 XPATH 表现更好。
注意:在选择一个元素时，确保该元素应该是唯一的、描述性的并且不太可能改变。

selenium WebDriver 中 CSS 选择器的语法:
driver . find element(by . CSS Selector(" input[class = ' username ']")。sendKeys(" skp tricks-3 ")；
要使用 css 选择器识别 web 元素，我们需要调用 css selector()方法。

查看以下网页链接，我们将使用该链接进行 CSS 选择器演示:
演示链接:
[https://skptricks . github . io/learn code/selenium-DEMO/log in % 20 registration % 20 page/register . html](https://skptricks.github.io/learncoding/selenium-demo/login%20registration%20page/Register.html)

[阅读更多...](https://www.skptricks.com/2018/04/css-selectors-in-selenium-webdriver-with-example.html)