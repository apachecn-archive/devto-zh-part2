# 为什么我们构建了一个定制的 React 样板文件(提示:协作和速度)

> 原文：<https://dev.to/praxentsoftware/why-we-built-a-custom-react-boilerplate-hint-collaboration--speed-djn>

创建定制的 React 样板文件可以加速初始开发，并帮助开发团队更有效地协作。虽然构建样板文件需要更多的时间，但从长远来看，这为我们的团队节省了大量时间。

这个案例研究有力地证明了为协作编码奠定一个共同基础的重要性。

请继续阅读，了解他们是如何构建样板文件的，以及使用这一新工具后我们看到了什么。

[![React boilerplate](../Images/9c8e0187fee0ff9100e3481c273caf46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GbZQ8SdY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1jo3aos7ya9r3mdxzht9.jpg)

## 一个定制的 React 样板文件，将最初的冲刺从 2 周减少到 3 天

Praxent 的两位全明星开发人员 Ryan Ostrom 和 Eddie Rozum 集思广益，创建了一个样板文件，供我们组织内从事 React 项目的所有团队和开发人员使用。定制的样板文件实现了以下目标:

*   减少在每个项目开始时建立构建流程所花费的时间。过去通常需要 2 周的初始冲刺，现在通常最多只需要 3 天的准备过程。
*   向每一个开发人员提供他们需要知道的测试信息。样板文件为开发人员提供了关于测试什么以及如何编写每个测试的信息。经验较少的开发人员现在可以编写测试，而不需要依赖其他团队中忙碌的专家。*样板文档中有每种需要编写的测试的例子，还有内置的 holder 结构。它会自动在测试脚本中包含 Jest。*
*   **对新的网络应用实施最佳实践**。
*   限制错误，促进**更快的编码。**
*   让结果更可预测。(我们提前知道输入输出值。)
*   重新加强数据完整性，增强安全性。

## React 样板文件中有什么

样板文件为开发人员提供了一个生产开发构建脚本；本地实时开发服务器和预先安排的文件夹结构。

开发人员不再需要从头开始创建构建过程。相反，当开始一个新的 React 项目时，他们只是在准备开始开发之前做一些调整。

以下是每个开发人员在访问样板文件后必须对构建过程进行的定制:

1.  自定义项目的参数
2.  将样板文件添加到源代码管理中
3.  连接 CI/CD 管道，实现持续集成和部署

## 更好的协作

Eddie 和 Ryan 开发了这个工具，以便在我们组织内从事不同项目的多个开发人员之间进行更好的协作。

他们希望创建一个工具，让任何新加入 React 项目的开发人员都能快速理解，并让高级开发人员在审查代码时更容易理解项目的布局。

固执己见的样板文件包括实现自动化测试的内置结构。该工具内置了自动代码分割功能。(代码分割允许开发人员在任何给定时刻只访问他们需要的 JavaScript，而不是提取他们必须手动筛选和分离的相关 JavaScript 块。)

以下是 React 样板文件使用的技术堆栈:

*   **React.js:** 用于构建界面的 JavaScript 库
*   用于管理 React 应用程序全局状态的库
*   **不可变 JS:**JavaScript 的不可变集合
*   **Webpack:** 用于预算 SCSS 和 JS 文件的模块捆绑器
*   Ryan 和 Eddie 从名为 Create-React-App 的 React 样板文件开始，为他们定制工具

***了解由埃迪·罗祖姆和瑞安·奥斯特罗姆创建的定制 React 样板文件的更多信息:[“如何加快 React 项目| React 样板文件的开发”](https://praxent.com/blog/speed-development-react-projects-using-boilerplates)***