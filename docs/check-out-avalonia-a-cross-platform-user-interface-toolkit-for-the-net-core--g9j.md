# 查看 Avalonia，这是一个跨平台的用户界面工具包。网络核心

> 原文：<https://dev.to/nishthasinghuk/check-out-avalonia-a-cross-platform-user-interface-toolkit-for-the-net-core--g9j>

生态系统中一个经常被提及的漏洞。NET 核心是缺乏任何真正的跨平台用户界面或 UI 工具包。尽管之前已经有了多平台工具包的尝试，比如 Xamarin 和 Silverlight forms，但是还没有人实现完全 XAML 风格的跨平台选项。Avalonia 尝试支持 Windows 的计划。NET 和[。NET Core](https://github.com/dotnet/core) ，MacOS，Linux，Android，iOS。

虽然主要灵感来自 WPF 或 Windows 演示基础，但这并不是工具包的直接移植。在设计上做了一些重大的改变，包括样式如何工作。在 Avalonia 中，样式的工作方式更像 CSS，选择器和样式类可以级联和重叠。相比之下，Windows Presentation Foundation 仅对每个控件启用一种样式。这意味着 Avalonia 必须能够最小化对重复样式的需求。Avalonia 不使用触发器，而是使用伪类，如。悬停效果的“指针”。其他伪类有:disabled，:focus，:pressed for buttons，:checked for checkboxes 等。

Avalonia 的样式只能绑定到 StyledProperty。这类似于 WPF 的依赖性，具有相同的基本模式。还支持附加属性。

一家[Asp.Net 开发公司](https://www.tatvasoft.co.uk/technology/microsoft-dot-net-development.php)的开发人员会发现一个有趣的特性，那就是数据绑定语法。对异步操作的支持。例如，Avalonia 可以绑定到其他基于 XAML 的用户界面工具包不支持的任务或 IObservable 操作的结果。

[![Avalonia UI for .net Core](../Images/6cfd9d8c5288f0f33d331f47d7c77677.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y08x_loE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/6i5x7zb.jpg)

## 来自更大的开源社区的贡献

虽然已经取得了很多进展，但是只有三个开发人员在做大部分工作。因此，他们呼吁更大的开源社区尽可能地参与进来。开发人员，那些在. NET 软件公司工作的人，甚至那些自由职业者都可以贡献自己的一份力量。

## 阿尔法阶段的阿瓦隆

阿瓦洛尼亚现在在阿尔法。这意味着现在的框架是一个舞台，人们可以在这里玩耍，有希望创造简单的应用程序。仍然缺少很多东西，人们会发现错误，API 会改变。然而，这代表了第一次使用和试验这个框架变得有些容易。

### 阿瓦隆的历史

许多年前，在 WPF 成为 Windows 演示基础并引入 XAML 作为用户界面标记语言之前。NET 的时候，有一个项目，代号是‘Avalon’。这是 WPF 的代号。现在，XAML 无处不在，XAML 标准是一个词汇规范。

Avalonia 是一个开源项目，它显然从 Avalon 那里获得了灵感，并且对 XAML 有着无可厚非的热爱。Steven Kirk 和一个由近 50 名贡献者组成的团队正在询问什么是跨平台。NET UI 框架的样子。Avalonia，以前称为有机玻璃，是一个多平台。NET UI 框架。值得注意的是，虽然阿瓦隆尼亚闻起来像 WPF，但它不是 WPF。这不是跨平台的 WPF，但它是阿瓦隆尼亚。它以不同于 WPF 的方式处理风格，实际上有很多微妙但可观的语法增强。

#### 发布中的一些主要特性

**1。延迟渲染。**它在 UI 线程上渲染到一个低级的场景图，然后在一个单独的渲染线程上渲染到窗口。这极大地增强了呈现性能，尤其是在涉及动画时，并且还提供了控件的正确点击测试。尽管如此，旧的渲染器仍然可以作为 ImmediateRenderer 类提供给那些想要渲染每一帧的人。

**2。基于 Monomac 的后端。用于 Mac OSX 平台的基于 Monomac 的新后端。新的 backed 使用了 Cocoa 窗口对话框，使应用程序看起来更自然。**

**3。相对源语法糖。**引入了绑定到与被绑定控件相关的其他控件的简写方式。在 WPF，这是通过在绑定上使用 RelativeSource 语法来实现的。

**4。图纸。**一种创建矢量图标的便捷方法，为 WPF 所用。Visual Studio 图像库以绘图的形式给出了许多图标。此外，还有其他工具可以产生它们。它们比形状控件更轻量级，因为它们不是视觉树的一部分。

**5。新的预览器。Visual Studio 扩展中的. NET 核心支持。**一个新的预览器架构，应该能够为非 windows 平台制作设计器。新架构使用 TCP 传输协议，以独立于平台的方式在应用程序和设计人员之间进行通信。它仍然可以选择嵌入 Visual Studio 扩展使用的 HWND，但是默认情况下，它会通过 TCP 传输呈现的位图数据。

**6。增加了控制。资源、StaticResource 和 DynamicResource。**新功能旨在效仿 WPF。尽可能靠近 UWP。每个控件都有自己的资源字典。

7 .**。日历控件。**将 Silverlight 日历控件移植到 [Avalonia](http://avaloniaui.net) 以满足所有日历需求。

**8。WPF 一体化。**增加了无缝的 WPF 集成。人们可以在 WPF 应用程序中嵌入 Avalonia 控件，而无需创建本地窗口，并且具有完全的布局集成，这是因为 WPF 和 Avalonia 的布局系统几乎相同。

#### 其他一些特性

**该版本中引入的其他有趣特性:**

*   当命令的绑定链为空时，按钮被禁用
*   将 ReactiveUI 升级到 V8 alpha
*   添加了 FindAncestor 绑定模式
*   工具提示:等开，放置，偏移
*   切换到。网络标准 2.0
*   屏幕实现
*   添加了显示任务栏图标实现
*   向 ProgressBar 添加了 orientation 和 is infinite 属性
*   移除 Cairo 和 GTK2 支持
*   为页面幻灯片动画添加垂直方向选项
*   添加了用于日志记录的 AppBuilder 方法
*   在 CheckBox、ToggleButton 和 RadioButton 上实现了三种状态
*   PortableXAML

为了满足世界各地的企业和其他组织在 Asp.NET 的各种发展需求，我们提供了一整套的点网络发展服务。