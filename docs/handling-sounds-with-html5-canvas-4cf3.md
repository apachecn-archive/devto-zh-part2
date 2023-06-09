# 用 HTML5 画布处理声音

> 原文：<https://dev.to/matthewodle/handling-sounds-with-html5-canvas-4cf3>

这里有两个要点:初始化资源并重用它们，如果您想要播放可能重叠的事件触发的声音(例如，激光发射)，您将需要资源的多个副本。

我很早就学会了初始化静态资产一次，然后重用它们。最初的实现是为每个游戏对象添加一个新的资产。这意味着发射的每一束激光都会给 DOM 增加一种声音。正如您所想象的，这允许您非常快速地计算出 DOM 中元素数量的上限。

解决方案是不要将声音直接与物体联系起来，而是创建一个声音池，然后当声音被触发时在声音池中循环。

下面是实现过程:

[https://github . com/modle/蜈蚣/blob/260 fa 54 dadce 91 f 25148638 cf 1eb 10d 46 ff 13 dec/app/scripts/sound . js](https://github.com/modle/centipede/blob/260fa54dadce91f25148638cf1eb10d46ff13dec/app/scripts/sound.js)

将被加载器脚本调用，并将在 tracks 对象的键上循环，创建声音并将它们添加到 DOM 中。

要播放声音，我们只需用匹配我们想要的键的类型调用`playSound()`。`playSound()`将调用`getSound()`，它将使用 mod 将目标索引设置为下一个索引，或者如果前一个索引是数组的结尾，则调用`0`。那么该索引处的声音元素将被返回并播放。