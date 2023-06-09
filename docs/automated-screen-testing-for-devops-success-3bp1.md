# 开发运维成功的自动化屏幕测试

> 原文：<https://dev.to/techcommunity/automated-screen-testing-for-devops-success-3bp1>

# 为 DevOps 成功

| 2018 年第三期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://bit.ly/2zTIWaf) |
| --- | --- |

我的同事 Zvonimir Ivanetic 最近在 2018 年第 1 期 TECHniques:[DevOps for the Mainframe](http://techcommunity.softwareag.com/web/guest/techniques-blog/-/blogs/devops-for-the-mainframe)和 2018 年第 1 期 Enterprise Tech Journal 文章:[Win-Win:Mainframe and devo PS(http://ourdigitalmags.com/publication/?i=484809)](http://ourdigitalmags.com/publication/?i=484809)中指出，“devo PS 的成功需要持续的测试。面对以惊人的速度交付的日益复杂的应用程序，软件测试人员有可能成为决定开发运维计划成败的瓶颈。要实施完整的开发运维，测试流程必须实现自动化，并转变为持续进行。”

在本文中，我将深入探讨为什么大型机应用程序的自动化屏幕测试是确保您的 DevOps 成功的重要因素，并介绍 Software AG 的 Natural Screen Tester。

## 测试在 DevOps 中的作用

DevOps 是为了更快地交付更好的软件，以满足最终用户的需求。自动化测试有助于确保软件的质量，并且需要克服手工测试的重大缺陷。手动测试经常被高成本和冗长的测试时间所困扰。测试用例通常没有文档记录，并且随着许多程序员退休年龄的迅速临近，基于过去的成果进行构建的能力正在迅速削弱。结果往往容易出错，代码覆盖率和测试的百分比很低。

Software AG 的 Natural Screen Tester 实现了测试的自动化，从而大大减少了确保应用程序功能所需的工作量。通过在用户界面(UI)级别进行测试，Screen Tester 超越了组件测试，以确保从最终用户的角度来看应用程序工作正常。因此，测试还覆盖了 UI 代码，这是应用程序代码的 5%到 10 %,用于调用测试的业务对象的规则和屏幕导航。除非你在屏幕上测试，否则软件组件(例如，在 NaturalONE 中测试的)可能会工作，但是没有人知道应用程序是否为用户正确工作。

## 基于屏幕的业务功能自动化测试

市场上有大量的测试工具，它们在不同的层次上针对不同的目标进行测试。有些人只是将屏幕上的字符与标准视觉进行比较。Natural Screen Tester 在表 1 中支持的终端协议上测试任何编程语言(即 Natural、Cobol、RPG、Fortran、PL/I、Assembler)的业务级屏幕上发生的事情。

| 设备类型 | 草案 |
| --- | --- |
| IBM 3278 | TN3270、TN3270E |
| AS/400 | TN5250、TN5250E |
| BS2000 | TN9750 |
| 串联 | TN6530 |
| 日本富士通公司 | TN6680 |
| (视频)终端 | VT100、VT200、VT220-7、VT220-8 |

Screen Tester 允许您:

*   记录基于绿屏的业务功能的流程，并将它们自动转换成单元测试
*   构建可以跨团队和环境共享的可执行测试用例
*   记录一次测试并提供测试断言——包含输入和输出参数的文件，允许您使用不同的输入集运行测试，以测试所有可能的组合
*   快速运行测试并明显看到结果
*   将您的测试集成到构建服务器中，并在每个新的构建中运行它们(DevOps)

让我用一个人力资源(HR)应用程序示例来演示一下。在人力资源应用程序中，我进入特定的屏幕，输入数据以添加新员工。然后，在最后，我检查雇员是否被正确地输入到系统中。为了对此进行测试，我将输入参数和输出参数存储在一个单独的测试数据文件中。同一个测试可以针对多个测试数据文件运行多次。

Screen Tester 通过将输入数据输入业务对象来模拟业务用户，然后将业务功能的结果与预期的输出数据进行比较。在测试中只关注输入和输出数据的决定是由客户需求特别驱动的。如果要比较(即测试)屏幕上的每个字符，UI 定义需要非常详细。通过只测试重要的东西——输入和输出——我们节省了大量的时间和成本，因为不需要在测试运行之前详细描述整个屏幕。这种方法使工具的用户能够确定什么是重要的。有了自动化的基于屏幕的测试，你将花更少的时间做常规任务，更快地发布软件。

## 自动化一次完成

当一个新的功能被引入到一个应用程序中时，你不能只测试这个新元素。您冒着巨大的风险，不知道一个变更会对代码的其余部分产生什么影响。为了增加您的测试覆盖率，您还需要重新测试所有之前的代码更改。这可能需要您运行数千次测试，才能真正确定系统保持了引入新变化之前的相同质量和稳定性。

手动完成这项工作非常昂贵，而且容易出错。过去，当添加新功能时，人们通常会反复测试。显然，这变得令人厌烦，因此过程

变得容易出错。测试人员可能会忽略业务功能的某个结果是不正确的——他们只是没有看到。今天，您甚至可能无法复制这些以前的测试，因为它们可能没有被记录，并且执行这些测试的专家可能已经退休或转移到其他组织。

对于手动测试，我们没有能力重新运行过去完成的所有测试。这就是自动化价值发挥的地方。如果我可以证明之前(手动)完成的所有测试仍然在工作，那么我就可以证明应用程序在最近一次更改后仍然处于稳定状态。通过自动化，您可以简单地重用过去创建的测试，并重新运行接近总代码覆盖率的测试。

[![](img/ec3d744a1fccee41941d4805519df558.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X-_B2CRr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/DEVOPS1.jpg/073c7a49-2e8c-4b17-9403-2616027d0e66%3Ft%3D1531381203653) 

**图 1:** 比较结果

自动化测试背后的思想是，当新功能被引入并且只被执行一次时，测试就被建立起来了(见图 1)。通过自动化测试，测试是“罐装的”,这意味着它在一个完全自动化的环境中被保存和重做。这是自动化测试的主要目的，确保系统不会因为一个小小的改变而崩溃。

有几种方法可以确保自动化测试为您的企业服务。如果您对某些函数使用静态测试感到满意，那么只需以 JSON 文件、Microsoft Excel 或 CSV 格式存储输入/输出参数。通过使用诸如 GIT 这样的源代码控制系统，您可以将所有版本与某些测试数据保存在一起。应用程序的代码被登记到源代码管理储存库中，并且对于具有相关测试的相同应用程序代码版本，测试断言也被登记。

使用静态“测试断言”的另一个选择是直接将结果与来自数据库或 web 服务的数据进行比较。如果规则发生变化或者应用程序生活在一个默认变化的世界中，这种方法是有意义的。例如，通过将测试数据与测试分开，您可以更改数据(如增值税值)，而不必更改测试本身。

创建一个场景测试床对保护测试知识大有帮助，通常由用心做测试的人来完成，并且给你的测试人员更多的时间来做更多的探索性测试。自动化测试并不是要取代测试人员，而是不要把时间浪费在日常事务上，并确保机构知识的安全。

## 它是如何工作的？

我们开发 Screen Tester 的目标是构建可移植的、可执行的测试用例，通过记录绿屏应用程序并将它们自动转换成单元测试，这些测试用例可以在团队和环境中共享。它记录测试一次，并提供输入文件来运行测试，使用不同的输入集来测试所有可能的组合。因为测试是在 Eclipse 中进行的，所以可以快速运行，并且结果是可见的。以下是屏幕测试器的工作原理。

**步骤 1–启动终端会话**

在测试工具中启动您的终端会话。使用直接 TCP/IP 连接连接到主机应用程序。在 Screen Tester 的例子中，工作环境应该看起来非常熟悉，因为它是基于 Eclipse 的。

[![](img/4f810d948373a0cd3ba80029531c9a08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DKnAClg5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/DEVOPS2.jpg/ae179b23-902c-4c70-b177-1ef866ef7f06%3Ft%3D1531381207222) 

**图 2:** 启动一个终端会话，记录屏幕流程。

**步骤 2–记录屏幕流**

记录您想要变成单元测试的屏幕流。捕获导航流中使用的屏幕和输入字段。

图 3Try Screen Tester

试用 Software AG 的 Natural Screen Tester，以弥合敏捷开发、持续集成&对基于屏幕的业务功能进行手工测试导致的代码持续交付之间的差距。Screen Tester 自动化测试，通过向业务功能提供输入数据，然后检查结果，允许通过屏幕界面测试新的业务功能。要评估 Screen Tester，只需向您的 Software AG 代表索要测试合同。Screen Tester 可以与 Software AG Installer(Empower 中提供)一起安装。安装时包括快速入门和自学的培训视频。成为高级用户的在线培训课程可于 2018 年 10 月预订。

[![](img/f5d8c5cc8357bbf120a11cb7cc71619f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--psMKK_NI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/DEVOPS3.jpg/f6450327-0bb6-4c9c-a1dc-be731254de4b%3Ft%3D1531381211020) 

**图 3:** 输入您想要用作输入的数据

**步骤 3–输入数据**

确定您想要用作“输入”的数据，在您的测试用例中可以是动态的。Screen Tester 将让您轻松地从应用程序中已标记的潜在字段的建议列表中选择要用作“输入”的字段。

[![](img/96e37d3028a084ec89884fe62e262bf9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0VlEv_Y3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/DEVOPS4.jpg/33cbfb3b-d8da-49c3-b401-07477ec48192%3Ft%3D1531381214661) 

**图 4:** 从建议列表中选择 _ *文件*_ 作为“断言”。

**步骤 4–记录输出**

确定您想要用作“断言”的数据从应用程序中已经标记的潜在字段的建议列表中，轻松选择要用作“断言”的字段。

[![](img/84331cf0a5faa97cfbaa3205d46ee77d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_qNIbjuE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/DEVOPS5.jpg/c32174c1-3d2e-41c4-8f32-fec6e35ebdd0%3Ft%3D1531381218144) 

**图 5:** 比较结果。

**步骤 5——比较结果**

在测试用例被创建之后，你可以将它生成为一个单元测试。然后执行它，将收到的运行时数据与预期的结果数据进行比较。Screen Tester 允许您创建多组不同的输入，与在同一个测试用例中运行多次的不同组预期结果进行比较。它还集成了您的独立开发环境(IDE)和构建环境，如 Jenkins。

## 自动化屏幕测试的好处

测试是关键任务应用程序开发的重要部分。不要只是测试你实现了什么；请记住，还有许多其他领域会受到你的改变的影响。请确保在您的所有应用程序中测试它。由于自动化测试，使测试成为开发过程的一部分不再太昂贵。

自动化屏幕测试的好处很多。无论您是解决:小代码的变化，以解决业务或法规的变化；在使应用程序现代化的同时适度修改；或者在将您的应用程序重新托管到新平台时发生重大变化，依靠自动化测试将帮助您在合理的成本和

时间范围内实现您的目标。借助与 DevOps 集成的 Screen Tester，您可以:

*   保护您的遗留系统免受退化和缺陷的影响
*   削减成本—减少测试时间
*   提高质量
*   保存知识
*   在重新托管期间和之后重用测试
*   与构建服务器和 DevOps 工具集成

DevOps 实际上是将软件快速、正确地提供给最终用户。软件开发完成后，集成测试是最好的情况，完全自动化。在确保功能按要求运行和用户输出被正确交付之间达到适当平衡的自动化测试对您的整体开发运维成功至关重要。

## 试屏测试仪

尝试 Software AG 的 Natural Screen Tester 来缩小敏捷开发、持续集成和持续交付代码之间的差距，这是由基于屏幕的业务功能的手动测试引起的。Screen Tester 自动化测试，通过向业务功能提供输入数据，然后检查结果，允许通过屏幕界面测试新的业务功能。

要评估 Screen Tester，只需向您的 Software AG 代表索要测试合同。Screen Tester 可以与 Software AG Installer(Empower 中提供)一起安装。安装时包括快速入门和自学的培训视频。成为高级用户的在线培训课程可于 2018 年 10 月预订。