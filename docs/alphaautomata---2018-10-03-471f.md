# 我最喜欢的项目

> 原文：<https://dev.to/zhu48/alphaautomata---2018-10-03-471f>

# 那东西

阿尔法自动机是我给我的副业取的可爱名字——一个高度模块化的机器人和自动化的命令和控制堆栈。截至目前，我设想它最终将包含以下组件:

*   ROS 包充当更高级应用程序的网关
*   用于设备间通信的物理层不可知分组协议
*   安 [RTOS](https://github.com/AlphaAutomata/AlphaRTOS)
*   一组 SOC 外设驱动程序
*   一套 [VHDL AXI 外设](https://github.com/AlphaAutomata/rtl_devices/tree/master/pwm/hdl)
*   一组 [VHDL I/O 模块](https://github.com/AlphaAutomata/digital_io)

# 背景

回到大学，我为威斯康星机器人公司和 T2 獾机器人采矿队编写嵌入式软件。我为 Wisconsin Robotics 的 [FreeRTOS](https://www.freertos.org/) 应用程序编写了我的第一个任务和驱动程序，并为 Badger 机器人采矿团队编写了一个[自定义 RTOS](https://github.com/BadgerRoboticMiningTeam/LaunchPad) 。通过所有这些开发，我发现了我对操作系统、硬件控制软件以及抽象这一优秀工程的支柱的热爱。

我目前的工作围绕着将一个遗留的 GUI 应用程序移植到一个现代的桌面操作系统，同时记录源代码。由于找不到创造性的出口来满足我在工作中对嵌入式系统的渴望，我决定重新架构我在大学时写的 RTOS，并开始围绕它构建一个完整的框架。

# 目标

我不认为我的框架会取代自由操作系统或泽法。事实上，泽法项目做了我想让我自己的 RTOS 做的所有事情，甚至更多，并且将是我未来想要开始的任何严肃的机器人项目的选择。我确实看到了我的框架的发展，以及我在 Dev 上写的帖子，磨练和扩展了我的工程技能。

我的第二个目标是把所有对嵌入式系统、硬件黑客、物联网或其他类似事物感兴趣的开发人员赶出去。作为一名嵌入式系统爱好者，我发现在这个似乎由 web 开发人员主导的平台上很孤独。

# 阿尔法的状态

我几乎还没有开始这个雄心勃勃的副业。到目前为止，我前面列出的组件几乎没有一个处于可展示的状态:

*   ROS 包充当更高级应用程序的网关
    *   我从未为 ROS 包写过一行代码
*   用于设备间通信的物理层不可知分组协议
    *   我还没有尝试开始这个
*   安 [RTOS](https://github.com/AlphaAutomata/AlphaRTOS)
    *   我已经完成了从我的大学 RTOS 中剥离不可移植代码的工作，并且几乎没有弄清楚我认为 API 应该是什么样的。我渴望成为泽法的一半，所以我可能会试着将泽法 API、RTOS CMS is API 和 POSIX API 组合成一个适度可用的东西
*   一组 SOC 外设驱动程序
    *   没有尝试开始，但我正在为 VHDL AXI 外设编写驱动程序
*   一套 [VHDL AXI 外设](https://github.com/AlphaAutomata/rtl_devices/tree/master/pwm/hdl)
    *   我已经测试了我的 PWM 模块，它工作！我当然没有尝试过所有的边缘案例
*   一组 [VHDL I/O 模块](https://github.com/AlphaAutomata/digital_io)
    *   我的 UART、PWM 和正交模块都在功能模拟中工作。谁知道他们会不会都做硬件？

# 向前和向上

我计划每周三在 Dev 上发布我的进展，感兴趣的人可以很容易地看到我的 GitHub。