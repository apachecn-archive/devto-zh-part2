# 如何在 Selenium WebDriver 中等待页面加载/就绪

> 原文：<https://dev.to/skptricks/how-to-wait-for-page-to-loadready-in-selenium-webdriver-1do8>

发布链接:[如何在 Selenium WebDriver 中等待页面加载/就绪](https://www.skptricks.com/2018/09/how-to-wait-for-page-to-load-ready-in-selenium-webdriver.html)

在本教程中，我们将解释如何在 selenium webdriver 中等待页面加载/就绪。通常 selenium WebDriver 自己处理网页加载或等待页面加载。但有时网页需要更多时间来加载页面，因为驱动程序脚本无法找到网页中指定的元素，从而导致问题。因此，在这种情况下，selenium webdriver 将在控制台中抛出异常消息，并停止脚本执行。在这个例子中，我们使用 javascript executor 来解决页面加载问题，javascript executor 还提供了预定义的函数，帮助检查页面加载状态。

[如何在 Selenium WebDriver 中等待页面加载/就绪](https://www.skptricks.com/2018/09/how-to-wait-for-page-to-load-ready-in-selenium-webdriver.html)
[![](img/32080699c80a0074bda25c2979914433.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AD2lq4nL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://4.bp.blogspot.com/-FNbh5HjKNhs/W6YClJQzPeI/AAAAAAAAB5g/V5ixlkCN4HUBJOu2xgDLs2eyE2CAhRLHQCLcBGAs/s400/sel.png)

检查页面加载状态的 Javascript 执行示例:
JavaScript executor js =(JavaScript executor)驱动程序；
js . execute script(" return document . ready state ")。toString()。等于(“完成”)；

[阅读更多...](https://www.skptricks.com/2018/09/how-to-wait-for-page-to-load-ready-in-selenium-webdriver.html)