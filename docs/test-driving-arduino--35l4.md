# 试驾 Arduino

> 原文：<https://dev.to/rubberduck/test-driving-arduino--35l4>

最初发布于[christopherjmcclellan.wordpress.com](https://christopherjmcclellan.wordpress.com/2018/02/16/test-driving-arduino/)。

周二晚上，我为哥伦布 Arduino 和 Raspberry Pi 爱好者举办了一场研讨会。我们度过了一段美好的时光，我认为在这里分享我的经历和我创建工作坊的动机是一个好主意。

Arduino IDE 不是我所说的好。这对刚接触编程或微控制器的人来说非常好。它能让你在几秒钟内从零到闪烁一盏灯，但缺少很多经验丰富的程序员所依赖的功能。它有无数的问题。

*   没有文件浏览器。
*   没有终点站。
*   没有代码导航(比如“转到声明”)。
*   代码被藏在很难找到和进入源代码控制的地方。
*   无法创建自动化单元测试。
*   Arduino 库并不总是表现良好(SoftwareSerial 和 OneWire 因全局禁用中断而臭名昭著)。

几个月前，我开始尝试解决这些问题。我的主要目标是能够 TDD 微控制器代码。第二个目标是能够使用我喜欢的任何编辑器。我想我可能会*需要*使用不同的编辑器。

在我开始之前，我拿了一本 James Grenning 写的[“嵌入式 C 的测试驱动开发”。在开始之前，我想知道我想做的事情是否可能。事实证明，这在很大程度上是非常可测试的，即使没有多少人为嵌入式系统进行 TDD。有些东西确实很难测试，但我稍后会讲到。](https://pragprog.com/book/jgade/test-driven-development-for-embedded-c)

James 在书中举了一个为一个 LED 驱动器做 TDD 的例子。这启发我创造了 [LED 驱动卡塔](https://github.com/rubberduck203/led-driver-kata)。前提很简单。您需要创建一个 LED 驱动程序来点亮映射到 8 位寄存器的一些 LED 内存。问题是，硬件还没准备好。我们如何为尚不存在的硬件编写并*测试*代码呢？James 在他的书中给出了一些例子，但是对我来说最重要的是通过将一个无符号整数的引用传递给驱动程序来伪造寄存器的想法。这不仅允许我们观察代码的结果，还意味着驱动程序代码不依赖于硬件。它看起来像这样。

```
TEST(LedDriverTests, TurnAllLedsOn)
{
volatile uint8_t leds = 0xFF;
LedsOn(&leds);
BYTES_EQUAL(0x00, leds);
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个核心概念是*交叉编译*你的代码。测试中的代码和你对*主机*的测试一起被编译。您的测试在您的开发机器上运行。一旦测试通过，你就可以针对*目标*设备的架构进行编译。

既然我知道测试嵌入式代码确实是可行的，我就着手实现这个形。

这里的秘密是 Arduino 是建立在预先存在的 [AVR 工具链](http://www.nongnu.org/avr-libc/)之上的。Arduino 使用 avr-gcc 编译器和引擎盖下的 AVRDude 编译上传到开发板。你可以自己去文件- >偏好设置，选择“显示详细输出”选项。

[![Show verbose output during [x] compilation [x] upload](img/4acd5b8acc823cd952a8537481de8d85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---XXRLnzH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://christopherjmcclellan.files.wordpress.com/2018/02/screen-shot-2018-02-15-at-7-53-04-pm.png)

Arduino 将工具链安装在它的应用程序目录中，但是我发现单独安装工具很方便。如果你运行的是 Windows，工具链可以通过 [WinAVR](https://sourceforge.net/projects/winavr/) 获得。如果你运行的是*nix 系统，就去看看我创建的 [AVR docker 镜像](https://github.com/rubberduck203/avr)。它列出了您需要的不同 apt 包，包括 CppUTest 测试框架。

我开始手工编译，但是没用多久我就开始摸索着创建 Makefile。事实上，我第一次做形的时候，花在制作 Makefile 上的时间比花在编写代码上的时间要多得多。LED 驱动的卡式原来是 2 个卡式合二为一。谢天谢地，我只需要解决一次。我把 Makefile 文件变成了我所有 AVR 项目的可重用模板。只是克隆项目添加一些代码。实际上，我也一直在为我正在制作的数字温度计[使用这种设置。效果真的很好。](https://github.com/rubberduck203/digital-thermometer)

*   我可以使用任何文本编辑器和终端。
*   我已经 TDD 了大部分的实现。
*   不用担心 Arduino 库行为不当，因为我没有使用它们。

不过这种方法也有一些缺点。

*   没有 Arduino 库+没有 Arduino IDE 意味着没有串行监视器。老实说，我很少错过它，因为我有一个测试套件。
*   并非一切都是单元可测试的。

例如，考虑一些需要非常快的引脚脉冲的外部器件。

```
void ShiftRegister::pulse(uint8_t bitmask)
{
    // pull the pin high to clock the data,
    // then back to low so we choose when to send it.
    port.DataOut |= bitmask;
    port.DataOut &= ~bitmask;
} 
```

Enter fullscreen mode Exit fullscreen mode

没有办法从测试中观察到副作用。当测试完成时，寄存器回到其原始状态。我认为这可以通过一个间接层和一些嘲讽来解决，但是代码显然是正确的。我不介意让它保持原样。

无论如何，这是一个相当冗长的方式，为我们那天晚上在研讨会上做的事情提供一些背景。我克隆了一个模板的新副本，并向小组介绍了形。接下来我给了他们游戏规则。我们会一起编武士道。坐在键盘前的人会按小组告诉他们的方式打字。那个人不允许思考。如果他们有一个想法，他们必须把键盘给别人，并指导那个人下一步该打什么。当我们完成后，我们会把它上传到一个 Arduino 上，这个 Arduino 连接着一排 8 个 led。

[![8 LED Bank](img/75edf326cffbfd73c134425d273a54dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S-z5N8dp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://christopherjmcclellan.files.wordpress.com/2018/02/8-led-bank.png)

我通过编写第一个测试开始了这个小组。事情一开始就有点不顺利。在一整天的工作之后，我在一群观众面前进行现场编码，却无法通过测试。几分钟后，有人指出我将值设置为与期望值相反的*。我谢过他，笑了笑，然后离开了键盘。人们起初非常犹豫，但很快这个团队就找到了他们的节奏。一个接一个的测试会变红然后变绿。偶尔我会插入一些见解或尝试重构。*

最终，我不再关注代码了。我开始四处闲逛，与那些有点过于保守、不愿积极参与的人交谈。我发现有一个有趣的问题组合。有些人问我关于 TDD，有些人问我关于微控制器和嵌入式编程。房间里很少有人两者都做过，据我所知，没有人用嵌入式 C++做过 TDD。

[![Group of people programming together in harmony](img/3108a5a71b365926e3b1bd57954d44ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qnm9CC7w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://christopherjmcclellan.files.wordpress.com/2018/02/img_1419.jpg)

突然我听到“嘿，克里斯！我们完了！我们怎么把这个东西上传到板上？”他们让我大吃一惊。在我们开始之前，我担心我们没有时间完成这个形。我跑回键盘，在 main 中写了一个快速循环，让灯依次闪烁，然后烧了程序。成功了。 **It。只是。奏效了。**当我们的设备驱动在第一次向电路板闪存时就开始运行*时，我们都有点惊叹于 mob 和 TDD 的威力。*

良好的...这几乎奏效了。有一盏灯没有亮。我立刻怀疑我没有经过任何测试就编写的循环，但是有人指出，“代码工作了。测试通过了，我们都看到了你写的那个循环。代码是正确的。这是一个*硬件*错误。”天哪，他们也是对的。在那盏顽固的小灯亮起之前，我们拨弄了一下试验板。

这正是整个练习的要点。我们*知道*代码是有效的，因为每一行代码都经过了满屋子程序员的测试和审查。这缩小了臭虫的搜索范围，只有在 T2 的 T3 才可能找到它。几分钟内就修好了。

我对事情的进展非常满意。尽管我没有提前准备第一个测试，而是通过现场编码搞砸了，但是集体解决问题和测试驱动开发的力量确实闪耀着光芒。然而，最重要的是，我们证明了不仅嵌入式代码可以被测试，而且如果有合适的工具和支持，没有多少经验的人也可以做到。