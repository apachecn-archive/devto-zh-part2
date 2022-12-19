# 如何使用 selenium 杀死 Chrome 驱动处理器

> 原文：<https://dev.to/skptricks/how-do-i-kill-the-chrome-driver-processor-by-using-selenium-5gfd>

帖子链接:[我如何用 selenium 杀死 Chrome 驱动处理器](https://www.skptricks.com/2018/08/how-do-i-kill-chrome-driver-processor-in-selenium.html)

在这篇文章中，我们将解释如何在 selenium webdriver 的帮助下杀死 chrome 驱动程序后台进程或从内存中释放 chromedriver.exe。根据 Selenium API，您确实应该调用 browser.quit()，因为该方法将关闭所有窗口并终止进程。然而，大多数时候，当我们试图在 Java 平台上执行 chromedriver 测试时，我们注意到了一个巨大的问题，chromedriver 实际上仍然存在。为了解决这个问题，你需要在命令提示符下使用下面的命令。

[![](../Images/bbcc93b3cd9c9411dc8557615bdb929d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c7eRkrsJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-zWH6BTt0IgU/W4DoHtF6iEI/AAAAAAAABz4/6OsoBAjEkvwoALPS3o8Q2N-yOnYJnsyEgCLcBGAs/s640/1.PNG)

从命令行终止多个进程
您需要做的第一件事是打开一个命令提示符，然后使用带有以下语法的 taskkill 命令:
taskkill /F /IM /T

这些参数将强制终止任何与您指定的可执行文件名称匹配的进程。例如，要终止所有 iexplore.exe 进程，我们可以使用:
taskkill/F/IM iexplore.exe

[**阅读更多...**T3】](https://www.skptricks.com/2018/08/how-do-i-kill-chrome-driver-processor-in-selenium.html)