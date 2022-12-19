# 一个平淡无奇，但令人振奋的，第一叶 SDL-德国管道

> 原文：<https://dev.to/mortoray/an-underwhelming-but-exhilarating-first-leaf-sdl-gl-pipeline-208b>

它可能并不漂亮，但这代表着 Leaf 项目向前迈出了重要的一步。足够开始[游戏直播编程](https://www.twitch.tv/mortoray)——跟我去那看。

[https://www.youtube.com/embed/CPOxLrps3oA](https://www.youtube.com/embed/CPOxLrps3oA)

兴趣点:

*   这个演示是用 Leaf 写的。你可以在这里看到仍然有点吓人的主文件。
*   我使用 libSDL2 创建窗口并初始化 OpenGL。
*   它处理窗口和键盘事件，这需要一些编译器的改变来支持 SDL C-union 类型(需要一个不安全的`cast｢T｣(expr)`操作符)。
*   我使用 GL 顶点和片段着色器，只有几个顶点属性和制服。
*   向 GL 发送数据需要引入一个`array.raw_ptr`成员来获得对 Leaf 中内存地址的原始访问。
*   我修复了编译器中的许多错误，包括许多改进的错误消息。虽然“重复符号”报告仍然是一个问题(没有位置！).
*   修正了一个导致内存耗尽的重大问题。否则这看起来是完全稳定的。

这让我开始在[我的流](https://www.twitch.tv/mortoray)上做最小的游戏编程。我的目标是探索我在晚间 Rust stream 上使用的一些算法，扩展语言，并从中获得一些乐趣。这将是一个艰难的旅程，但欢迎你加入！