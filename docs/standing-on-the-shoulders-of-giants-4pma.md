# 站在巨人的肩膀上

> 原文：<https://dev.to/hulquest/standing-on-the-shoulders-of-giants-4pma>

我一直很喜欢 Windows 开发平台。过去，我为 SQLServer Management Studio 编写了一个插件，并为存储平台编写了一些 PowerShell cmdlets。我的主要工作一直是编写 Java 和 Python。

最近，我有机会更频繁地使用我的公司 Windows 10 笔记本电脑。由于我阅读了所有关于 Visual Studio 代码的好评，我想我应该唤醒我的 Windows 开发环境。

就像我们获得的任何技能一样，如果我们不使用它并保持技能的锋利，那么技能就会变得迟钝。但就像肌肉记忆一样，我们的大脑会很快记住曾经是第二天性的技能。我和 PowerShell 有这种关系。我永远无法克服 Vim 按键绑定的缺失，但我总是对 PowerShell 的功能感到惊喜。它真正打破了基于文本的外壳。抱歉 Linux。

我目前的部分重新觉醒引导我到一个新的技术，我想与你们所有人分享。我面临的问题是*“我知道我定义了一个函数来做这件事，但是我想知道函数后面的代码是什么？”*然后偶然发现了 **Get-Content 函数:FunctionName** 。虽然这很酷，但这真的只是一种记住我过去编写的代码的方式。当指向专业人士写的内容时，这种技术变得更加强大。

使用 New-Guid 简单演示了这一点。让我们看看运行 *Get-Content 函数:New-Guid* 时的结果。

```
PS [hci-monitor] > Get-Content Function:\New-Guid

    [CmdletBinding(HelpURI='https://go.microsoft.com/fwlink/?LinkId=526920')]
        [OutputType([System.Guid])]
    Param()

    Begin
    {
        [Guid]::NewGuid()
    } 
```

如您所见，这是一段实现 Cmdlet 接口的 C#。它有一个指向文档的指针和一个(小的格式)错误，因为 OutputType decorator 缩进得太多了。该函数只是返回一个新的 Guid 对象。我不认为 Linux 人员真的意识到这个 Guid 不是您想要的文本，而是一个完全成熟的对象，您可以转发、关联并(当然)获得一个文本表示。

这只是一个简单的例子。看看更复杂的东西，比如*Update-iscstargetportal*。现在，您可以检查这些代码，更好地了解微软的专业人员是如何完成一项重要任务的。这就是你磨砺 PowerShell 技能，站在巨人肩膀上的方法。

直到下次...