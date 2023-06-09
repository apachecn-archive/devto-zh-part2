# 分三步执行自动化测试

> 原文：<https://dev.to/katalon/execute-automation-testing-in-3-steps-5e0j>

> **截止日期:2021 年 10 月 25 日下午 23:59**
> **2021 年质量状况报告**
> *如果能知道同行和专家如何在质量保证游戏中保持领先，那该多好啊这就是我们创建《2021 年质量状况报告》的原因，该报告旨在向所有级别的专业人士收集最佳质量保证实践。
> 我们很高兴为**前 100 名受访者提供价值 30 美元的亚马逊礼品卡**，以及调查结果的最终报告。[提高嗓门！](https://www.research.net/r/medium-devto)T12】*

自动化测试是一种使用自动化测试工具在软件应用程序上编写和执行自动化测试用例/测试套件，将实际结果与预期行为进行比较并创建测试报告的方法。

然而，在缺乏编程知识的情况下运行测试时，复杂性可能会发生，仍然存在许多其他挑战:

为了克服所有的挑战，在这篇文章中，我不仅将向你展示如何通过三个简单的步骤创建测试用例并执行自动化测试，还将展示有效管理测试和报告的解决方案。

## **执行自动化 Web 测试的 3 个神奇步骤**

所有魔术师都需要支持工具，Web 测试执行也是如此。我的魔术将通过一个叫做 **Katalon Recorder** 的工具来表演，这个工具可以在 3 秒钟内安装在最新版本的 [Chrome](https://chrome.google.com/webstore/detail/katalon-recorder-selenium/ljdobmomdgdljniojadhoplhkpialdid) 和 [Firefox](https://addons.mozilla.org/en-US/firefox/addon/katalon-automation-record/) 上。

关于 Katalon Recorder 的几句话，它被誉为替代解决方案之一，因为 Selenium IDE 从 Firefox 55 开始不再工作([官方声明](https://seleniumhq.wordpress.com/2017/08/09/firefox-55-and-selenium-ide/))。正如工具制造商所说，“Katalon Automation Recorder 记录动作并捕获 web 应用程序上的 web 元素，让您快速轻松地生成、编辑和执行自动化测试用例，而无需编程知识。”

是的，Katalon Recorder 不是唯一的一个，有一些 [Selenium 替代品](https://www.katalon.com/resources-center/blog/selenium-ide-alternative-firefox-chrome)在那里，如量角器，Kantu 或机器人框架，但我只是在这里展示最简单的方法和最熟练的工具来做神奇的事情；).

给定一个示例测试用例，其场景如下:

假设您有一个服务预订网站，要求用户登录、填写表单并提交以完成预订过程。在主题上改了一点点之后，你可能会怀疑这个功能是不是还能用。以下三个简单的步骤将帮助你摆脱困惑。

**步骤 1:使用记录功能**创建一个新的测试用例

*   打开 Katalon 记录器，点击“New”创建一个新的测试用例
    [![Katalon Recorder](img/daa633e505481fcaa22c7bc8c4c31acb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D-Hx8Huy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AdY1e62yLhMhzaqfg.)

*   然后点击“记录”生成测试用例，步骤如下:

*   访问[http://demoaut.katalon.com/profile.php#login](http://demoaut.katalon.com/profile.php#login)(卡塔隆执行自动化演示网站)

*   输入用户名/密码(John Doe/ ThisIsNotAPassword)

*   填写并提交表格

*   注销
    [![Katalon Recorder](img/2039369494043148bcf4292e7b6141fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wOuXO5Wt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A9y-39__xOfVhuTis.)

第二步:保持冷静&执行自动化测试

单击“Play”运行测试并监控。如果测试用例在某个地方失败，您可以停止回放并立即删除错误。

您可以手动更改“值”输入，以测试更多帐户的功能。

快乐案例应该如下图所示
[![Execute automation](img/adec297cc90ff6a9ab39df576c081b90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ofF-O85m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ABBLCJzVBNc8Ghai4.)

并在不登录任何帐户的情况下将您带回主页。
[![Execute automation](img/268f90afbd7772977c8d9d779ee09501.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--7JXk6njJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A31o134gGPwCoOVn7.)

**步骤 3:自动化测试专家的高级步骤**

如果您想要放置更多可变的测试数据来覆盖不同的情况，并使用这些数据自动运行测试，Katalon Recorder 支持数据驱动测试，允许用户定义数据集并执行使用这些数据的测试脚本。

*   您应该在这里下载示例测试数据，并在“test data”选项卡上添加名为“test data.csv”的文件。
    [![data driven testing](img/219d73404e5369293912375ab654c544.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--KhjQbq87--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A-Ir4M6NOzwlebclw.)

*   通过点击“打开测试套件”而不是点击“创建测试套件”来导入保存为 HTML 文件的测试用例，并打开我的示例测试用例。

*   点击“播放”,使用变量输入自动执行测试。
    [![data driven testing](img/dc0c878bfbca32381182978fe3d2bf4f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ExfQ2xPm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A8E3rIdPMLl5ssAeA.)

您还可以将测试用例导出为多种语言的脚本，如 Java、Ruby、Groovy、Python、C#和 Robot framework。

不仅如此，Katalon platform 还提供了名为 Katalon Analytics 的工具，帮助您保存所有测试报告，并通过彩色图表直观地显示出来。您可以点击“上传日志”(云状图标)，将您的结果直接上传至 Katalon Recorder。您可能需要在[www.katalon.com](http://www.katalon.com)注册一个 Katalon 账户(免费)，并登录到[https://analytics.katalon.com](https://analytics.katalon.com)的 Katalon Analytics 来创建一个新项目。
[![katalon analytics](img/9c3cd3261b610b7e192da174b13993c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ii7zlla0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ApN5srzvArQBKMhNW.) 
然后回到 Katalon Recorder 窗口，选择你想要上传日志的项目，点击“上传”
[![upload report](img/5b76eadcdd782cb893f89d6bafce5218.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QoBuXXqr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AQ5NYtfGRj2c5J6MF.) 
下图是我执行两次测试用例后的测试结果。

[![Test result](img/d46a04ce4dbc91310be1fcb8d334b4d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XViMzpnL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AVy6jodNExinZgfRN.) 
虽然 Katalon Analytics 是作为测试版推出的，但它满足了我对测试报告的所有需求，甚至可以轻松地与队友分享结果。

## **在 Chrome 上运行 Selenese (Selenium IDE)脚本& Firefox**

在官方宣布 Firefox 55 以上版本的 Selenium IDE 结束后，这是 Selenium IDE 粉丝感到沮丧和失望的角落。Katalon 的一些人似乎很善良，通过允许用户从 Selenium IDE 导入 Selenese 脚本到 Katalon 记录器来带回希望。[点击这里查看](https://docs.katalon.com/display/KD/Selenese+%28Selenium+IDE%29+Commands+Reference)

在这篇文章中，我不会说更多关于 Katalon 平台的内容，即使它们都很棒&是免费的，它应该属于另一篇题为“如何利用 Katalon 平台(Katalon Studio，Katalon Docker，Katalon Analytics，Katalon Recorder)让你的测试生活更轻松”的文章。

结论
下面是卡塔隆记录仪突出特点的总结:

*   记录、播放和调试(暂停/恢复，设置断点)测试用例。
*   最快的执行速度。
*   与所有 Selenese 命令兼容，用户可以将原始 Selenium IDE 测试导入到 Katalon 记录器中。
*   支持测试文件输入小工具的文件上传(仅限 Chrome)。
*   使用 if/else if/else 和 while 命令轻松控制流量。
*   支持数据驱动测试—数据可以从 CSV 文件导入。
*   带有日志、屏幕截图的高级报告，以及(可选)与 Katalon Analytics 的集成，用于历史报告和分析。
*   导出到 Selenium WebDriver 这些框架中的脚本:C# (MSTest 和 NUnit)、Java (TestNG 和 JUnit)、Ruby (RSpec)、Python(单元测试)、Groovy (Katalon Studio)、Robot Framework、XML(用于构建自定义导出器)。
*   兼容最新的火狐和 Chrome 浏览器。

所以，执行自动化测试用例并管理测试报告并不像我们想象的那么痛苦。一般来说，有了明智的测试策略和适当的支持工具集合，测试执行或自动化测试不再是大问题。

原帖:[分 3 步执行自动化测试](https://dzone.com/articles/execute-automation-testing-in-3-steps)