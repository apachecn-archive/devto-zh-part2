# 我是如何爱上单元测试的

> 原文：<https://dev.to/stegriff/how-i-learned-to-love-unit-testing-29bf>

闪回到 2012 年。当我到达[村](https://villagesoftware.co.uk/blog/how-i-learned-to-love-unit-testing/)时，我还是一个 19 岁的年轻人，有很多东西要学*，我大概会把自己归类为“黑客”(这曾经让约翰尼明显地做了个鬼脸，事后看来，我能理解)。我喜欢坐在电脑前开始写特写。这个设计出自我的手指。

当有人向我解释单元测试时，我产生了一种生理上的厌恶；“这是您必须编写的代码，以证明您的代码有效”...但是谁测试*测试*测试呢？

与此同时，我还接触到了“模拟”，这似乎是在说“因为用真实数据运行单元测试是不可能的，所以我们必须看看你的测试代码是如何在不是真实数据的数据上运行的”。这似乎不是一件有用的事情。

通过鼓励测试驱动开发(TDD)的尝试，完成了测试痛苦的三连胜，这就是说“你只能编写失败的单元测试或者使失败的单元测试通过的代码”——也就是说，预先编写所有的单元测试，然后编写实现。

这些元素在我内心制造了如此的困惑和悲伤，以至于我在专业软件开发的早期没有做多少(成功的)单元测试。

[![Home Simpson GIF - My life is ruined](img/8cfaf355dc7621dd684185e43cfca620.png)T2】](https://i.giphy.com/media/3o6MbtCPDaE3KSSrN6/giphy.gif)

对我来说，编程是，而且永远应该是有趣的！软件开发就像建筑，除了无限制的撤销、无限制的材料、X 射线视觉和由 unobtainium 制成的工具——你可以在眨眼之间做出任何你想要的东西，并随时改变它。编程是富有表现力的、快乐的和令人满意的。我认为，单元测试剥夺了我的这些快乐。

那么今天，单元测试是我乐趣的一部分，这是怎么回事呢？

*可悲的是，从那时起，我需要学习的东西越来越多

#### 我的第一个单元测试成功

现在是 2015 年。我在业余时间写了一个游戏自娱自乐。在这个游戏中，航天器可以组建和解散舰队，但这个功能是一个烂摊子。在游戏测试中，舰队会意外地无法形成、无法解散、成长和缩小...我需要某种方法来全面测试这些代码路径，而不是以科学的严谨态度亲自尝试每一个选项。我突然意识到单元测试实际上是答案。

[![Source code of unit tests for some space ships](img/6b6856d556d72cc0535d718365f0aa11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5_R7gDZO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mj109q7r6dj9ffqe1b7t.PNG)

我已经尝到了单元测试的有用性。在这个过程中，我甚至写了一个简单的 mock 一些能为我提供对象而不是用户输入数据的东西。测试这个项目是一次愉快而轻松的经历，因为:

*   我的项目只适用于普通的老式。Net 对象(没有数据库连接或奇怪的依赖)
*   接受测试的班级有明确的职责

测试显示了我的实现中的大量缺陷，这些缺陷是我用几个小时的手工测试永远也发现不了的。突然间，代码变得干净、清晰并在控制之下！我还意识到我的游戏逻辑与用户界面紧密相连；测试无法完成，因为它们正在等待用户输入。因此，我重构了游戏对象，以接受一个委托来推进游戏；虽然真正的游戏会使用`Console.Readline()`，但是测试可以通过一个什么也不做的委托。我无意中遇到并实现了控制反转。*感谢测试！*

#### 升温

受到个人领域中单元测试成功的鼓舞，我开始在 Village 的一个棘手的项目中引入测试。这个项目有团队正在努力克服的代码质量问题。

我们制定了一个政策，任何时候你修复了一个 bug，你都必须写一个单元测试来证明它已经被修复了并且一直被修复！这并不总是可能的，因为我们正在与 Microsoft Office 集成，并且一些 Office 对象很难模仿。但这鼓励我们:

*   将业务逻辑与前端集成逻辑分离开来
*   测试所有可能的业务逻辑
*   明确班级责任

这个项目现在有大约 300 个单元测试，并且是一个每天都在用户手中的成功项目！这是一个很好的机会，让我们快速扩展的团队更多的参与到单元测试的好处中来。

#### 使天气凉爽

大约在这个时候，我还注意到 GitHub 上有数百个 NPM 包的酷孩子会自豪地在每个项目的首页显示自动生成的徽章，宣称“__%覆盖率”。他们把单元测试覆盖的代码行的比例游戏化了，这很吸引我！

[![Badges on the front page of a GitHub repo](img/0af9e70ec6cf7ce106ea8c6faff9ab3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cqD0eWKb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qtku6j27vbc8h8rurlp8.PNG)

突然间，单元测试不仅有用，而且很酷。

> ![Ste Griffiths profile image](img/dfbb208bcacee2c0ea23e36c39d92a9a.png)Ste Griffiths[@ stegriff](https://dev.to/stegriff)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)酷黑客，2007:从此 FTP 酷黑客下载我的 leet 无证 Python 脚本 2017:我的 npm 模块有 100%测试覆盖率17:25PM-03 2017 年 1 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=816334726940229632)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=816334726940229632)

#### 单元测试灌输美

最近，我们建立了一个项目，搜索许多不同文件类型的内容。不同的搜索组件是孤立构建的，对系统进行全面的规律性测试是不现实的。这是单元测试的理想场所，项目非常接近测试驱动开发。

*   搜索提供者遵循工厂模式，通过测试确保他们总是为给定的文件类型生成预期的搜索引擎。
*   搜索引擎遵循策略模式，通过测试来确保它们正确读取样本文件。
*   搜索结果模型有简单的测试，如检查新对象是否总是设置了正确的默认值，或者搜索是否创建了对搜索内容和结果数量的适当描述。

这真是一件美丽的事物。

#### 有所作为

在这条路上的某个地方，单元测试不再是一种负担，而开始成为一种乐趣。你能做些什么来实现这一点？

*   从简单开始。作为一名企业编码新手，我被一次引入测试、模拟和 TDD 的过程所淹没。如果你以前没有做过自动化测试，从任何东西开始。不管你使用黑盒还是白盒，正确的技术，或者正确的框架。只是测试一些东西！
*   拥有它。设计一个你可以测试的简单项目。或者，如果你是一个鼓励者，给你的团队一个这样的项目，让他们思考。让它变得有趣。
*   学习结构。单元测试与面向对象的原则密切相关。这些需要时间和经验，但是当你在一个方面提高了，你在另一个方面也会提高。一个大的子例程根本不可测试，但是遵循对象原则的解决方案(通常看起来像设计模式)是非常可测试的。
*   冷静点。是啊，这是一个有点未知。而是让提倡者提倡。让你组织中的文化承载者将此作为一种乐趣而不是负担传递下去。不要反复强调，但要营造一种氛围，让可证明的质量得到奖励，如果可能的话，游戏化！

我希望这有助于鼓励你了解自动化测试的好处和可访问性，尤其是单元测试。如果你有任何问题或意见，请在 twitter 上给我打电话 [@SteGriff](https://twitter.com/SteGriff) ，如果你想让一个专家组织与你或你的项目合作，请与我们联系 [@VillageSoftware](https://twitter.com/VillageSoftware) ！

整合自[乡村软件](https://villagesoftware.co.uk/blog/how-i-learned-to-love-unit-testing/)

[![Geordie thumbs up](img/d4ae24d214c45ff08df0d173f28ad50f.png)T2】](https://i.giphy.com/media/n7C5DOuH1m0iA/giphy.gif)