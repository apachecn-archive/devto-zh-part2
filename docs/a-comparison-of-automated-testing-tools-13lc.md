# 自动化测试工具的比较

> 原文：<https://dev.to/executeautotest/a-comparison-of-automated-testing-tools-13lc>

在软件项目中成功应用测试自动化的一个决定性因素是选择和使用正确的测试自动化工具集。这是一项艰巨的任务，尤其是对于那些软件测试自动化的新手来说，因为市场上有如此多的工具可供选择，每一个都有不同的优点和缺点。没有工具可以满足所有自动化测试的需求，这使得寻找合适的工具变得困难。通过 Katalon Studio 与市场上其他流行的自动化测试工具集的定性比较，了解如何为您的项目确定正确的自动化工具。

## 工具概述

Katalon Studio 是一个自动化测试平台，它提供了一套全面的功能来实现移动和 Web 应用的全自动化测试解决方案。Katalon Studio 构建在开源的 Selenium 和 Appium 框架之上，通过减少学习和集成这些框架以满足自动化测试需求所需的工作和专业知识，允许团队快速开始测试自动化。

Selenium 可能是最流行的自动化框架，它由许多用于 Web 应用程序测试的工具和插件组成。Selenium 以其支持 Web 应用程序性能测试的强大功能而闻名。Selenium 在开源测试自动化领域是一个流行的选择，部分原因是其庞大而活跃的开发和用户社区。

统一功能测试(UFT) ，前身是 QuickTest Professional (QTP)，可能是功能测试自动化最流行的商业工具。UFT 提供了一套全面的功能，可以覆盖桌面、移动和网络平台上的大多数功能自动化测试需求。

[TestComplete](https://smartbear.com/product/testcomplete/overview/) 也是一个用于桌面、移动和 Web 应用测试的商业集成平台。像 UFT 一样，TestComplete 提供了许多关键的测试自动化功能，如关键字驱动和数据驱动测试、跨浏览器测试、API 测试和 CI 集成。该工具支持多种语言，包括 JavaScript、Python、VBScript、JScript、DelphiScript、c++脚本和 C #脚本，用于编写测试脚本。

## 对比工具

下表提供了基于软件自动化关键特性的工具对比。

| 特征 | 加泰罗尼亚工作室 | 硒 | UFT | 测试完成 |
| --- | --- | --- | --- | --- |
| **测试开发平台** | 跨平台 | 跨平台 | Windows 操作系统 | Windows 操作系统 |
| **测试中的应用** | 网络和移动应用 | webapps | Windows 桌面、网络、移动应用 | Windows 桌面、网络、移动应用 |
| **脚本语言** | Java/Groovy | Java，C#，Perl，Python，JavaScript，Ruby，PHP | VBScript | JavaScript、Python、VBScript、JScript、Delphi、C++和 C# |
| **编程技巧** | 不需要。建议用于高级测试脚本 | 集成各种工具所需的高级技能 | 不需要。建议用于高级测试脚本 | 不需要。建议用于高级测试脚本 |
| **学习曲线** | 中等 | 高的 | 中等 | 中等 |
| **易于安装和使用** | 易于设置和运行 | 需要安装和集成各种工具 | 易于设置和运行 | 易于设置和运行 |
| **脚本创建时间** | 快的 | 慢的 | 快的 | 快的 |
| **对象存储和维护** | 内置对象存储库、XPath、对象重新识别 | XPath，用户界面映射 | 内置对象存储库，智能对象检测和纠正 | 内置对象存储库，检测常见对象 |
| **基于图像的测试** | 内置支持 | 需要安装额外的库 | 内置支持，基于图像的对象识别 | 内置支持 |
| **DevOps/ALM 集成** | 许多 | 否(需要额外的库) | 许多 | 许多 |
| **连续积分** | 流行的 CI 工具(如 Jenkins、Teamcity) | 各种 CI 工具(如 Jenkins、巡航控制) | 各种 CI 工具(如 Jenkins、惠普质量中心) | 各种 CI 工具(如 Jenkins、惠普质量中心) |
| **测试分析** | [Katalon Analytics](https://www.katalon.com) | 不 | 不 | 不 |
| **产品支持** | 票务支持、社区、专职人员(第三方支持服务) | 开源社区 | 敬业的员工、社区 | 敬业的员工、社区 |
| **许可证类型** | 免费软件 | 开源(Apache 2.0) | 所有人 | 所有人 |
| **成本** | 自由的 | 自由的 | 许可和维护费用 | 许可和维护费用 |

## 长处和短处

上面的比较表主要集中在自动化测试工具的共同特征上。下面通过挑选和比较这些工具的主要优点和局限性，展示了另一个视角。

| 工具 | 强项 | 限制 |
| --- | --- | --- |
| **目录工作室** | 

*   No licensing and maintenance fees are required (if necessary, paid dedicated support services can be provided).
*   Integrate the necessary framework and features for rapid test case creation and execution.
*   Build on Selenium framework, but don't need Selenium.

所需的高级编程技能 | 

*   Emerging solutions for rapid community development.

*   The function set is still evolving.
*   Lack of choice of scripting language: only Java/Groovy is supported.

 |
| **硒** | 

*   Open source, no authorization and maintenance fees.
*   And the huge active development and user groups keep pace with software technology.
*   Open for integration with other tools and frameworks to enhance their capabilities.

 | 

*   Test teams need to have good programming skills and experience to set up Selenium and integrate it with other tools and frameworks.
*   The new team needs the upfront investment time to set up and integrate.
*   Slow support from society.

 |
| **UFT** | 

*   Mature and comprehensive automated testing functions are integrated into a single system.
*   Dedicated user support plus a huge user community has been established.
*   Only basic programming skills are needed to start the creation and execution of tests.

 | 

*   Expensive solution: The licensing and maintenance costs are quite high.
*   And upgrading and adding modules can be costly.
*   Only VBScript is supported.

 |
| **测试完成** | 

*   Mature and comprehensive automated testing functions are integrated into a single system.
*   A variety of scripting languages are available.
*   Only basic programming skills are needed.

 | 

*   Like UFT, TestComplete requires considerable licensing and maintenance costs.
*   Additional costs for additional modules and additional components.

 |

自动化测试没有通用的工具。强烈建议测试人员评估各种工具，以便选择最能满足他们自动化测试需求的工具。用于开发软件的编程语言和技术不断发展，自动化测试工具也是如此，这使得成本成为工具选择中的一个重要因素。商业供应商通常对工具升级收费，如果您的软件使用新兴的和经常变化的技术，这可能是很大的一笔费用。另一方面，开源和非商业工具不会产生额外的费用，但需要集成新升级的努力和专业知识。很难找到将各种工具和框架集成到开源解决方案中所需的支持和专业知识。与开源框架集成的新兴工具，如 Katalon，为商业和开源自动化测试解决方案提供了一个可行的替代方案。

自动化测试工具的比较