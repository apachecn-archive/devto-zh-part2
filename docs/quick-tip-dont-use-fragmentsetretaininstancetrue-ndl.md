# 快速提示。不要用 fragment . setretaininstance(true)！

> 原文：<https://dev.to/androidhate/quick-tip-dont-use-fragmentsetretaininstancetrue-ndl>

我正在开发几个月的 android 应用程序。

TL；就是不要。不要用。不要创造僵尸。让他们去死吧。

说来话长。我在 android 应用程序中使用片段制作精美的 feed。我想如果我转动手机，我的信号就会重现。那很糟糕。所以我用了 setRetainInstance(true)。它在短时间内解决了这个问题。但是它也带来了数百万其他人。你在旋转中幸存下来的碎片附着在慢慢死去的旧活动上。(是啊，安卓就是这样弱智但是 nvm)。所以当你试图用它做 smth 时，过了一段时间它就会死。
你也不能从你的活动中运行这个片段的任何方法。比如 survived Fragment . dosomth()——当你运行这个 android 的时候，它会告诉 fragment 他的上下文已经死了，你不能再给它附加新的活动。但是，即使没有运行命令，垂死的活动最终也会死去，您的片段也会死去。

所以我得出的结论是，您不应该在至少有一个 view 对象的片段上使用 setRetainInstance(true)。

谢谢，对谷歌大喊:我讨厌 android 的生命周期。我认为 android 的生命周期浪费了人类数万亿小时的时间。