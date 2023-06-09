# 材料设计 2——不仅仅是圆角

> 原文：<https://dev.to/saschb2b/material-design-2-more-than-just-rounded-corners-khh>

<figure>[![](img/b1d4e41d5dbd4b7235683ec8ba338df6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OIjjKSYY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtIOufDyXyCbog64OBiUrfg.png)

<figcaption></figcaption>

</figure>

### 材料设计

谷歌在 2014 年首次展示了其对下一代 UI 原则的愿景。

[https://www.youtube.com/embed/1TbbLzCBwj0](https://www.youtube.com/embed/1TbbLzCBwj0)

因此，Google I/O 2018 发布了材料设计的迭代版本。

许多东西都没有改动，这很好，因为到目前为止它运行得很好。一些东西现在被调整和增强，为有创造力的 UI 设计者提供了更好的自由。加入了一些新的东西。我们来分解一下。

### 目标

材料设计指南的第一个版本有两个基本目标，在过去几年中实现得非常好。

#### **创造**

创造一种视觉语言，将优秀设计的经典原则与技术和科学的创新和可能性结合起来。

#### **一统**

开发单个底层系统，实现跨平台和设备尺寸的统一体验。移动规则是基础，但触摸、语音、鼠标和键盘都是一流的输入方法。

如果你想了解更多关于第一版的内容，请阅读存档的指南。

[简介-材料设计](https://material.io/archive/guidelines)

采用材料设计指南的设计非常直观。但是他们有一个共同点，他们看起来和感觉起来几乎都一样。你可能会说这是一件好事，但如果你想展示你的品牌，这肯定不是。这就是为什么谷歌今年增加了第三个目标。

#### 自定义

扩展材料的视觉语言，为创新和品牌表达提供灵活的基础。看一下这篇演讲，看看只需做一些调整就能实现什么。

[https://www.youtube.com/embed/3VUMl_l-_fI](https://www.youtube.com/embed/3VUMl_l-_fI)

### 原则

材料设计始于三个非常重要的关键原则。

#### 比喻物质

材料设计的灵感来自物理世界及其纹理，包括它们如何反射光线和投射阴影。材料表面重新想象了纸和墨水的媒介。

#### 醒目、生动、有意

材料设计受印刷设计方法的指导——排版、网格、空间、比例、颜色和图像——创造层次、意义和焦点，让观众沉浸在体验中。

#### 运动提供了意义

运动通过微妙的反馈和连贯的过渡来集中注意力并保持连续性。当元素出现在屏幕上时，它们转换并重组环境，相互作用产生新的转换。

### 两条新原则

为了实现定制的新目标，他们不得不稍微扩展这个列表。

#### 柔性基础

材料设计系统旨在实现品牌表达。它集成了一个定制的代码库，允许组件、插件和设计元素的无缝实现。

#### 跨平台

Material Design 跨平台保持相同的 UI，使用跨 Android、iOS、Flutter 和 web 的共享组件。

### 颜色

[https://www.youtube.com/embed/xYkz0Ueg0L4](https://www.youtube.com/embed/xYkz0Ueg0L4)

[色彩风格材料设计](https://material.io/archive/guidelines/style/color.html#)

事情就是这样开始的。预定义的调色板，作为开发人员，我很高兴我的决定如此简单。视频标题里都这么说了！

现在，对于新的目标和原则，我们可以选择更多的颜色。喜欢所有的颜色。希望你的应用程序是紫色的，但有点蓝色？没问题，去吧！

[![](img/11017bd5da45098a1ca732649abaeb13.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EzhsWvn7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AuNfp9jsEENQgZqVq.png)

所以他们为颜色定义了三个原则。

#### 分等级

颜色表示哪些元素是交互的，它们如何与其他元素相关，以及它们的突出程度。重要的元素应该最突出。

#### [清晰可辨](#legible)

文本和图标等重要元素在所有屏幕和设备类型中出现在彩色背景上时，应该符合易读性标准。

#### 富有表现力

在难忘的时刻展示品牌色彩，强化你的品牌风格，从而强化你的品牌。

[颜色系统](https://material.io/design/color)

### 形状

到目前为止，我们有圆形按钮、略圆的角和硬角。现在，我们可以使用各种设置进行各种组合。

[![](img/87c45d4f2387942a341ba265fa00d63d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i3us-QuZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A5NwaS0Joh4PEso8l.png)

默认情况下，材料曲面为矩形，带有 4dp 圆角。他们有可调的:

*   拐角角度和曲线
*   边缘角度和曲线
*   拐角的数量

形状变化，如圆角或切角，可能很细微，也可能更显著。

[关于形状](https://material.io/design/shape/about-shape.html)

### 组件

#### 应用栏

我们已经知道并喜爱应用程序栏。它放在顶部，为我们的汉堡菜单图标、标题和我们需要的各种动作提供空间。

[![](img/1cc8bf66fdd3eb325da79a996f4b7d5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nk2TCC8---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AZeSNdKPlX2ElCrBg.png)

但你知道什么最喜欢顶层空间吗？缺口。哦，光荣的缺口。你可能会注意到 iPhone X UI 的调整，以更好地将 notch 融入当前的设计中。android 也是如此。缩放的页面标题和所有顶部动作现在都在底部。他们没有说出来，但这就是为什么应用程序栏底部被介绍。

[![](img/b12c060fda696af5720fc06a746a4171.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0Bre-T3m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AMrmnIeOaXAaEw1i4.png)

它将当前的应用程序栏移到底部，基本上和正常情况一样。但是 FAB(悬浮动作按钮)呢。你可能会问，这不会妨碍你吗？是的，会的。所以他们就合并了。

关于工厂的布局和视觉吸引力有各种各样的变化。它甚至可以放在一个切口中。

[应用程序栏:底部](https://material.io/design/components/app-bars-bottom.html)

#### 按钮

该页面上的主要操作是什么？过去每个设计师都是这么要求的。在大多数情况下，很容易定义什么是主要的*，什么是次要的*。如果你不需要突出，使用*平按钮*，使用*凸起按钮*突出。**

 **对于材料设计 1，我们有平按钮和凸起按钮。结合原色或第二色很容易区分。但是有时我们希望有一个“中间”按钮来显示一个特殊的用例。这才是新的！

他们将平面按钮重命名为文本按钮，将凸起按钮重命名为内含按钮。他们称“中间”按钮为轮廓按钮。

[![](img/777263eb5986f30ec99669f5a4ad0945.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jd9c5XoZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/720/0%2AF5RubobFy7Pn00YU.png)

这将使不同的重点更容易显示，并给设计师更多的选择。

[按钮](https://material.io/design/components/buttons.html)

#### 工作表

哦，可爱的床单。抽屉的弟弟。它基本上只是一个纸元素，将从按钮上升，以显示美丽的行动或细节。在某些情况下，它甚至是持久的，并提供媒体播放中使用的动作。

一些设计需要在侧面显示选项。如过滤器、排序选项或附加细节。我们必须自己建造这个。但不再是我的朋友了。我在边上介绍床单！

[![](img/9071419da277ae73ca12f45326f5fa52.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rrA8MMZy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AKEAQYW0XO_7u8zeO.png)

它的工作原理和底层一样，但现在是在侧面。在持久状态下使用它，或者像一个模态，让它覆盖你的内容。

[纸张:侧面](https://material.io/design/components/sheets-side.html)

#### 天幕

这是一个全新的组件。它将前一层中显示的内容与后一层中与该内容相关的细节、过滤器或其他操作相结合。

[![](img/cc14edc162a262497928b9a183ffb925.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--seFQCf77--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIXPbQbUpJ5QNfnTwn9ruVA.gif)

[背景](https://material.io/design/components/backdrop.html)

#### 文本字段

输入字段只有一个标签、输入区域和一条下划线。现在有两个选项可以使用。填充文本和轮廓文本。

[![](img/12a9e4b7cc8cdfb853f97f3faeb7fc16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dA46vwaI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AhOHC2MVbQlqiY754.png)

填充的文本字段具有更多的视觉强调，使它们在被其他内容和组件包围时更加突出。

与填充文本字段相比，轮廓文本字段的视觉效果较弱，这有助于简化许多文本字段放在一起的表单等地方的布局。

[文本字段](https://material.io/design/components/text-fields.html)

### 结论

许多微妙但非常重要的变化，以实现更多定制的新目标。还有很酷的新功能，可以解决过去的困惑。

这些变化将导致标准的不同变化和适应，同时仍然具有不同的外观和感觉。

作为一名开发者和设计师，我仍然难以下咽，因为我刚刚认为我掌握了材料设计 1。但是改变和进步对每个人都有好处。

* * ***