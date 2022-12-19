# Amazon Lex —破解一个邮政编码槽类型

> 原文：<https://dev.to/samwsoftware/amazon-lex--hacking-a-postcode-slot-type-13gh>

当你创建一个 Amazon Lex 聊天机器人时，你使用 slots 从用户那里收集信息。为了确保插槽捕获正确类型的信息，您必须为每个插槽赋予一个*插槽类型*。

亚马逊提供了一个预制的插槽类型列表，涵盖了许多内容，如数字、姓名、城市和许多其他类别。这很棒，我发现我大约一半的插槽可以设置为 Amazon 插槽类型。

如果你需要的插槽类型不是默认的 Amazon 类型，那么他们会让你创建自己的插槽类型。这很棒，因为理论上讲允许无限独特的插槽类型来完美匹配用户的输入。

### 创建自定义插槽类型

您可以创建两种类型的自定义插槽类型:*扩展值*和*插槽值和同义词*。Expanded values 是您提供一些样本值的地方，Amazon 将这些样本值作为训练数据来学习识别值是否是正确的插槽类型。槽值和同义词允许您对槽值进行硬编码，然后提供一个用户可能会说的同义词列表。

### 处理英国邮政编码

最近，我们创建了一个需要询问用户邮政编码的机器人。亚马逊没有*邮政编码*插槽类型，所以我们需要创建一个自定义的。为此，我们将不得不使用*扩展值*，因为我们不能只列出每个英国邮政编码。

#### 初次尝试

对邮政编码类型的第一次尝试涉及用大约 8 个邮政编码填充示例值列表。我本以为 Lex 会从这些示例中学习邮政编码，但这导致了一些奇怪的事情发生。有时它拒绝大约 1/3 的真实邮政编码，有时它让几乎任何东西通过。

[![](../Images/18dc741ea137934b25de14e52efa2402.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pn9d8Rz7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/786/1%2Au88kERHMowHi8owdVFwTPg.png)

[![](../Images/bfac18107fd7602c68891d2bc4bf2a38.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BoQyBB6h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/768/1%2A2AV1J6U4qptif4nmegZfLA.png)

[![](../Images/ae46b5cde05004945394b3d39243cf02.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZjBsYCcD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/764/1%2ABzDwL17xaOkKdO6H-7w5rA.png)

插槽类型的关键是确保如果用户输入一个有效值，它将通过，不正确的值将被阻止。这种方法不够好。

#### 试 2

当创建*扩展值*槽类型时，它让您知道它使用示例值作为训练数据。为了提高验证器的准确性，我们应该尝试添加更多的训练数据。这样它就有更多的值来训练和测试。

我下载了一个 999 个真实英国邮政编码的列表，并编写了一个脚本来构建 slot 类型的 json 文件。我把这个上传给了莱克斯并进行了测试。

事实上，999 个新的邮政编码只用了几秒钟就完成了，这意味着它没有运行机器学习训练周期。它必须使用另一种匹配值的方法。

测试时，它有一些与第一个版本相同的问题。虽然它确实接受更多的真实邮政编码并拒绝更疯狂的值，但它仍然拒绝一些真实邮政编码并接受一些随机值。

#### 尝试 3 —黑客攻击开始

因此，在尝试使用亚马逊的方法让它正常工作后，我们求助于绕过这个系统。我们知道有一些很棒的 rejex 函数用于邮政编码验证，所以我们决定尝试一下。

我们需要设计一个新的插槽类型，允许所有东西通过，这样我们就可以将它传递给我们自己的验证 Lambda 来执行 rejex。我们复制了第一个插槽类型，然后开始添加长度在 3 到 8 个字符之间的随机值，包括数字、大小写字母和空格。

我们添加了一个初始化和验证 Lambda，它接受邮政编码槽的值，并通过 rejex 运行它。如果它成功了，那么我们让 Lex 继续，否则我们重新引出邮政编码槽。

这比前两种方法都好得多，过滤掉了所有无意义的值，但有时仍然会在有效的邮政编码通过 rejex 验证之前阻止它们。

#### 第四次魅力？？？

当我的一个同事开始研究其他人如何处理类似的问题时，我转到了项目的另一部分。我们不可能是唯一遇到这个问题的人。

幸运的是我们没有。虽然很多问题都是关于美国邮政编码的，但还是有一些有用的信息。最重要的一次是尝试*音乐*和*音乐家*的时段类型。人们建议他们让任何价值通过。

有了这些宝贵的知识，我们为音乐和音乐家创造了一个新的测试来看看谣言是否是真的。

音乐很好，允许通过所有 XXX XXX 格式的邮编，但阻止所有 XXXX XXX 的邮编。音乐家是我们淘金的地方。这种插槽似乎可以让任何东西通过。当我说什么的时候，我是认真的，不管是猫、狗还是新鲜王子的歌词。

[![](../Images/fac355b0c406877af56b54fadf0429e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GdU4kgsW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/424/1%2ApzgwvBUUsWh5iMet5uv4yg.png)

[![](../Images/bcb4bed2e7cce937ac8aba2610c78791.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D-P1aPIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/420/1%2Am3q8ZdQk2Q8KeJyeNBd1Ew.png)

这种插槽类型允许我们的 rejex 处理用户给它的任何值。你可以使用验证 Lambda 在他们说的时候捕捉它，或者只是在你实现 Lambda 的开始运行一些验证检查。

### 总结

*   使用几个值来创建一个插槽类型不是很好
*   使用数百个更好，但不是很好
*   创建一个插槽类型来捕获任何东西是可以的
*   使用*音乐家*插槽类型可以让任何东西通过

利用这些知识，我们现在可以创建 rejex slots 验证，或者您想要的任何类型的验证。

如果你认为你会在你的聊天机器人中使用它，或者只是喜欢看到人们在亚马逊周围黑东西，那么给它一个赞，并关注我以获得更多的聊天机器人提示和技巧。

* * *