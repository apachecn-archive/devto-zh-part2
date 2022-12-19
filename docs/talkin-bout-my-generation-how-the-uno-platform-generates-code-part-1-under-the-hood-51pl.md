# 谈论我这一代:Uno 平台如何生成代码，第 1 部分——引擎盖下

> 原文：<https://dev.to/uno-platform/talkin-bout-my-generation-how-the-uno-platform-generates-code-part-1-under-the-hood-51pl>

在之前的文章中，我们已经介绍了 [Uno 平台](https://platform.uno/)如何获取在 [XAML](https://docs.microsoft.com/en-us/windows/uwp/xaml-platform/xaml-overview) 标记语言中定义的可视化树，并在 iOS、Android 和 WebAssembly 上创建它。在本文中，我想深入一个关键的中间步骤:如何解析 XAML 并将其映射到生成的 C#代码。在第 2 部分中，我们将看看 Uno 利用代码生成推动车轮转动的其他几种方式。

### 解析 XAML

XAML 代表可扩展应用程序标记语言。这是一种基于 XML 的语法。尽管它可以用来描述几乎任何东西，但它更适合于描述应用程序的结构。它首先在[的 Windows Presentation Foundation](https://en.wikipedia.org/wiki/Windows_Presentation_Foundation)中使用，从那以后(除了语法上的细微差别)在许多环境中使用，包括 Silverlight (RIP)、WinRT、Xamarin Forms 和 Universal Windows Platform。

XAML 击中了一个甜蜜点:它是人类可读的，富有表现力的，并且有足够的结构来支持像 [Blend](https://docs.microsoft.com/en-us/visualstudio/designers/creating-a-ui-by-using-blend-for-visual-studio?view=vs-2017) 和 [XAML 设计师](https://docs.microsoft.com/en-us/visualstudio/designers/creating-a-ui-by-using-xaml-designer-in-visual-studio?view=vs-2017)这样的设计工具。一个很好的特性是它可以通过[代码隐藏](https://docs.microsoft.com/en-us/dotnet/framework/wpf/advanced/code-behind-and-xaml-in-wpf)文件与 C#无缝混合。

为了利用这种能力，Uno 需要能够读取 XAML 文件，并将其转换成可编译的 C#代码。

第一步是使用[系统将文件解析成 Xaml 对象树。Xaml](https://docs.microsoft.com/en-us/dotnet/api/system.xaml?view=netframework-4.7.2) 命名空间。

接下来，Xaml 对象树被处理成 C#类定义。大部分繁重的工作都是在庞大的 XamlFileGenerator 类中完成的。我们依靠[微软。CodeAnalysis API](https://github.com/dotnet/roslyn) (又名‘Roslyn’)将 XAML 使用的类型与应用程序集合或其依赖项中定义的类型进行匹配。输出将保存到生成的文件中。

每当你建造一个 Uno 头时，所有这些都在幕后发生。

### 最终结果

我们来看一个具体的例子。这是我们在上一篇文章中放入`MainPage.xaml`的标记的一部分:

```
<StackPanel> 
        <TextBlock x:Name="ClickTextBlock" 
                   Text="Button wasn't clicked yet" /> 
        <Button Content="Click me" 
                Click="Button_Click" /> 
    </StackPanel>
```

生成的输出可以在项目标题下的“obj”文件夹中找到。

[![](../Images/ac23760e1aca76aa9357edb26112c979.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oOQWyFKV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AlGYiCBWwMK-g_Fk9.png)

*main page . xaml 生成的代码文件*

这是生成代码的一部分，显示了上面 XAML 片段的输出:

```
public sealed partial class MainPage : Windows.UI.Xaml.Controls.Page 
    { 
        private void InitializeComponent() 
        { 
            var nameScope = new global::Windows.UI.Xaml.NameScope(); 
            NameScope.SetNameScope(this, nameScope); 
            // Source ..\..\..\..\..\..\UnoExtTestbed.Shared\MainPage.xaml (Line 1:2) 
            Content = new global::Windows.UI.Xaml.Controls.StackPanel 
            { 
                // Source ..\..\..\..\..\..\UnoExtTestbed.Shared\MainPage.xaml (Line 8:3) 
                Children =  
                { 
                    new global::Windows.UI.Xaml.Controls.TextBlock 
                    { 
                        Name = "ClickTextBlock", 
                        Text = "Button wasn't clicked yet", 
                        // Source ..\..\..\..\..\..\UnoExtTestbed.Shared\MainPage.xaml (Line 9:4) 
                    } 
                    .MainPage_05a03e67d6a6581089c5d12899278d61_XamlApply((MainPage_05a03e67d6a6581089c5d12899278d61XamlApplyExtensions.XamlApplyHandler0)(c0 =>  
                    { 
                        nameScope.RegisterName("ClickTextBlock", c0); 
                        this.ClickTextBlock = c0; 
                    } 
                    )) 
                    , 
                    new global::Windows.UI.Xaml.Controls.Button 
                    { 
                        Content = @"Click me", 
                        // Source ..\..\..\..\..\..\UnoExtTestbed.Shared\MainPage.xaml (Line 11:4) 
                    } 
                    .MainPage_05a03e67d6a6581089c5d12899278d61_XamlApply((MainPage_05a03e67d6a6581089c5d12899278d61XamlApplyExtensions.XamlApplyHandler1)(c1 =>  
                    { 
                        c1.Click += Button_Click; 
                    } 
                    )) 
                    , 
                } 
            }
```

多拗口啊！生成的代码倾向于为机器可读性而非人类可读性进行优化。例如，当我们发现编译器不得不进行大量计算开销很大的类型推断时，就增加了对`XamlApplyHandler`的显式强制转换。这一改变将大型 XAML 文件的编译速度提高了一倍。

在 Uno 开发的正常过程中，您不需要查看从 XAML 生成的代码，甚至不需要知道它在那里，尽管偶尔在调试时看一眼是有用的。

回想一下，`Button_Click`事件是在主页面的[代码隐藏](https://medium.com/@unoplatform/pushing-the-right-buttons-how-uno-implements-views-under-the-hood-a5e93ea86688#the-number-goes-up)中定义的。就编译器而言，创作和生成的文件都只是同一个类的[部分定义](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)。

最后一个有趣的事实:当使用[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)(Uno 在 preview 中暂时支持)在 MacOS 上构建时，Uno 使用内部端口来代替系统。Mac 上不可用的 Xaml 命名空间。同样的代码支持 Uno 通过[窗口对运行时 Xaml 解释的支持。UI.Xaml.Markup.XamlReader](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.markup.xamlreader) ，用于 [Uno 图库 app](https://github.com/nventive/Uno.Playground) 和 [Uno 的](https://playground.platform.uno/)[交互模式](https://github.com/nventive/Uno.Playground/blob/master/src/Uno.Playground.Shared/Samples/Playground.xaml)。游乐场网站。

帖子[谈论我的一代:Uno 平台如何生成代码，第一部分——引擎盖下](https://platform.uno/talkin-bout-my-generation-how-the-uno-platform-generates-code-part-1%e2%80%8a-%e2%80%8aunder-the-hood/)首先出现在 [Uno 平台](https://platform.uno)。