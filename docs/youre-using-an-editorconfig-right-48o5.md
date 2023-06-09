# 你用的是 EditorConfig 吧？

> 原文：<https://dev.to/peppage/youre-using-an-editorconfig-right-48o5>

在很大程度上，我用两种不同的语言编写代码。C#和 go。虽然我出于不同的原因喜欢它们，但我想念 go 的一点是自动格式化。我知道双方都有争论，但我发现一个遵循相同规则的代码库更容易阅读。当它自动发生时，我就不必担心它，我可以专注于编码。

如果没有自动格式化程序，让每个人都保持在同一页面上并不容易。也许你认为单行 if 语句应该有括号(它应该有)。如果有人创建了一个没有括号的拉请求，那么它必须被改变。

要是我们能有办法解决这个问题就好了...哦等等！ [EditorConfig](https://editorconfig.org/) ！

使用 EditorConfig 文件是如此简单！一旦设置完成，Visual Studio 将在文件中用我们都非常喜欢的曲线给出建议。它不会停止编译器，但我不知道有哪个程序员可以忽略这些红线。还有，你可以想怎么严格就怎么严格。有很大的灵活性。

# 开始吧

有几个简单的方法来设置它。最快的不过是抄袭别人的。如果你能接受他们的选择，这是一个很好的起点。就像罗斯林的这首[。但是，对我来说，在变量名上有点太严格了。](https://github.com/dotnet/roslyn/blob/master/.editorconfig)

微软的 Intellicode 插件会自动从你的代码中创建一个。它试图决定你的风格。从那里你可以调整什么地方出错了，或者你想改变什么。

[![Adding EditorConfig from Intellicode ](img/e2b1fe015d54b4b3926ca7c68a12ebf8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f7-w4LyC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XhuPZ8k.png)

然而，如果你的代码到处都是，那就不是最好的选择。

# 微调

无论如何，如果你想调整文件是很容易的。[文档真的很好](https://docs.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options?view=vs-2017)，只要你登陆[右页](https://docs.microsoft.com/en-us/visualstudio/ide/editorconfig-code-style-settings-reference?view=vs-2017)。

就像我说的，你可以想多严格就多严格。该文件允许您配置它在 Visual Studio 中的显示方式。您可以将其设置为显示警告、错误或建议。对于那些不应该被打破的规则来说非常方便。它也非常精细，似乎每件事都有一个设定！

我想提醒你，这并不会阻止一个特别固执的人去编译。但我认为这对大多数诚信行事的人来说已经足够了。我发现它在提醒人们我们的格式时特别方便。

我希望您能看到这个工具在保持代码库格式化方面是多么有用。它非常容易设置，Visual Studio 会自动使用它。使它成为一个没有大脑！