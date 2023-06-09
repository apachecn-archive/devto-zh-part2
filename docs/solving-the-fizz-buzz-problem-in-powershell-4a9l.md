# 解决 PowerShell 中的嘶嘶声问题

> 原文：<https://dev.to/hf-solutions/solving-the-fizz-buzz-problem-in-powershell-4a9l>

## PowerShell 中嘶嘶作响

在开始这篇文章的实质性部分之前，我将从一个小轶事开始，所以如果你不感兴趣，可以随意跳到**没有更多的轶事**😅我完全理解。

大约两周前，我意识到我已经有一段时间没有写任何东西了，有点想写，但不知道该写些什么。我还在处理一个困扰我大脑的感觉，“我真的应该再写一篇文章吗？”如果我写了“谁在乎我是否再写一篇文章？还有无数其他人写了同样的东西”，以及其他负面的想法。

我和几个最好的朋友一起去吃墨西哥玉米卷，喝龙舌兰酒，享受彼此的陪伴。当我在那里时，我的一个更注重技术的朋友向我走来，我们开始谈论我的工作，玩 Overwatch 和其他书呆子的事情，当他向我提到他喜欢阅读我过去的文章。虽然他可能不是一个活跃的开发人员，而且有些东西超出了他的理解范围，但正如他所说，我已经“掌握了诀窍”。

听到这些我很高兴，除了当我可以向用户群强烈要求的 [Tron](https://github.com/HF-Solutions/Tron) 添加功能时，我对“项目”没有那种感觉。这足以提醒我，我开始写这些的全部原因是，像 T2 这样的人可能会喜欢我的写作，并可能让他们对编程更感兴趣，学习新的东西，或者只是为了阅读有趣的东西而享受阅读。

最后，我想再次感谢我的那位朋友给我的鼓励，因为如果没有他们，我可能不会有动力开始这个项目💗

## 不再有奇闻

[![Spiderman_Reading](img/e99c87721ff64d0b3d9032cc23938b93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qfgkJ4OT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/uQbNvu9.png) 
*图片由 [Raj Eiamworakul](https://unsplash.com/photos/o4c2zoVhjSw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上传 [Unsplash](https://unsplash.com/search/photos/story)*

现在我们开始这篇文章的有趣部分！🤤如果你不熟悉 [Fizz Buzz](http://codingbat.com/doc/practice/fizzbuzz-code.html) 面试问题，这是一个相对简单的问题，擅长测试你的知识和对给定编程语言的基本理解，以及向面试官展示你是哪种类型的开发人员。

例如，你更喜欢把一堆东西扔向墙壁，看看什么有用，把多余的削掉？或者您更喜欢在编写代码之前就为程序制定一个计划？如果你想了解更多关于面试官能从一个嘶嘶作响的解决方案中得到什么的信息，请随时查看福布斯上 [Lionell Pack](https://www.quora.com/profile/Lionell-Pack) 的这篇文章。

## 有什么问题吗？

实际的问题是(据我所知，如果我说错了，请纠正我)最初由 Imron Ghory 在他的博客上[提出的，它是基于一个*“*](https://imranontech.com/2007/01/24/using-fizzbuzz-to-find-developers-who-grok-coding/)*[儿童小组文字游戏](https://en.wikipedia.org/wiki/Fizz_buzz)来教他们关于除法。”*虽然有很多不同的方式来表达这个问题，但我们将使用以下措辞:

> 写一个打印数字 1 到 100 的程序。然而，对于三的倍数，打印“Fizz”而不是数字。同时，对于五的倍数，打印“嗡嗡”而不是数字。对于两者的倍数，打印“FizzBuzz”

这意味着我们需要记住或考虑一些事情:

1.  最小值为 1。
2.  最大值为 100。
3.  确定检查多个的顺序，以避免遗漏边缘情况。
4.  将输出写入控制台。

## 从哪里开始？

这就是每个人不同的地方。根据您作为开发人员的工作方式，您的切入点会有所不同。就我个人而言，我将从创建`for`循环开始，该循环将遍历我们需要分析的所有数字。这让我们从 PowerShell 的如下内容开始:

```
for  ($x  =  1;  $x  -le  100;  $x++)  {  Write-Output  $x  } 
```

这将为我们提供一个输出从 1 到 100 的每个数字的起点。现在我们应该开始输入一些`if`语句来确定`$x`是 3、5 还是两者的倍数。与大多数现代编程语言一样，PowerShell 有一个 [**模数**](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_arithmetic_operators?view=powershell-6#long-description) 运算符，它将返回两个数相除的余数。

例如，执行`Write-Output (25 % 5)`将输出 0，因为 25 除以 5 没有余数。这也意味着第一个值是第二个值的倍数，我们可以用它来确定`$x`的倍数。当我第一次写这个脚本时，我的解决方案是这样的:

```
for($x  =  1;  $x  -le  100;  $x++)  {  $Threes  =  $x  %  3  $Fives  =  $x  %  5  if  (($Threes  -eq  0)  -and  ($Fives  -eq  0))  {  Write-Output  "FizzBuzz"  }  else  if  ($Threes  -eq  0)  {  Write-Output  "Fizz"  }  else  if  ($Fives  -eq  0)  {  Write-Output  "Buzz"  }  else  {  Write-Output  $x  }  } 
```

## 清理我们的初稿

虽然这是可行的，但它有点笨拙，如果我们想添加 3 或 5 的倍数以外的情况，不允许我们在未来轻松修改它。我并没有完全意识到这一点，直到我去寻找更多关于这个问题的信息，发现了汤姆·斯科特的一个视频。他指出，通过将每个数字的输出合并为一个变量，你可以很容易地考虑未来的调整。

虽然他使用了 **JavaScript** ，但是解决方案应该很容易理解，看起来应该是这样的:

```
for (var i = 1; i <= 100; i++) {
  var output = ""

  if (i % 3 === 0) output += "Fizz"
  if (i % 5 === 0) output += "Buzz"
  if (output === "") output = i

  console.log(output)
} 
```

如您所见，他为`$x`的每次迭代创建了`output`变量，并根据倍数增加或替换该变量的值。然后，最后，他使用`console.log`将其输出到控制台。将它翻译成 PowerShell，我们会得到这样的结果:

```
for  ($x  =  1;  $x  -le  100;  $x++)  {  $Output  =  ""  if  ($x  %  3  -eq  0)  {  $Output  +=  "Fizz"  }  if  ($x  %  5  -eq  0)  {  $Output  +=  "Buzz"  }  if  ($Output  -eq  "")  {  $Output  =  $x  }  Write-Output  $Output  } 
```

至此，我们已经有了用 PowerShell 编写的 Fizz Buzz 问题的工作解决方案。如果您将它复制并粘贴到一个终端中，您将得到如下所示的内容:

[![FizzBuzz.ps1_Output](img/cf4fef295e7a3f4d4a0db0cf4c47ecde.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MQBvyNKK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/tC2pfXg.png)

## 终于，一个剧本！

现在，由于这是 PowerShell，仅仅有一个我们需要复制粘贴的`for`循环并不有趣。不，我们需要一个剧本！😈

我创建了一个名为`FizzBuzz.ps1`的文件，并通过在 PowerShell 脚本的顶部添加标准内容开始工作:

```
[CmdletBinding()]  param  (  ) 
```

由于我最初是为我参与的[示例程序](https://github.com/jrg94/sample-programs)库编写这个脚本，我知道我想要支持的不仅仅是从 1 到 100。相反，我希望用户能够提供最小和最大参数，让他们修改输出。这仅仅意味着向`param ()`字段添加两个参数，如下所示:

```
param  (  [Parameter(Mandatory  =  $false,  Position  =  0)]  $Min  =  1,  [Parameter(Mandatory  =  $false,  Position  =  1)]  $Max  =  100  ) 
```

`Mandatory`和`Position`属性告诉 PowerShell 这些参数有默认值，并且它们不是强制的，position 属性使得像`.\FizzBuzz.ps1 0 75`一样调整最小值和最大值成为可能，而不必指定参数名。然后，通过对`for`循环的一些小改动，我们有了我们的[成品](https://github.com/jrg94/sample-programs/blob/master/archive/p/powershell/FizzBuzz.ps1)！

```
<# .SYNOPSIS A simple script that solves the standard FizzBuzz interview problem. .DESCRIPTION A simple script that solves the FizzBuzz interview problem to illustrate flow
control within a PowerShell script. .PARAMETER Min
  The minimium value to start counting at (inclusive). Defaults to 1. .PARAMETER Max
  The maximum value to count to (inclusive). Defaults to 100. .EXAMPLE .\FizzBuzz.ps1
1
2
Fizz
4
Buzz
Fizz
... .EXAMPLE .\FizzBuzz.ps1 -Min 10 -Max 75
Buzz
11
Fizz
13
14
FizzBuzz
16
... .NOTES For reference, here's a copy of the FizzBuzz problem that this script solves,
the only difference between the requested solution and this script is the script
will let you determine the minimum and maximum values if you desire:

"Write a program that prints the numbers 1 to 100\. However, for multiples of
three, print "Fizz" instead of the number. Meanwhile, for multiples of five,
print "Buzz" instead of the number. For numbers which are multiples of both,
print "FizzBuzz"
#>  [CmdletBinding()]  param  (  [Parameter(Mandatory  =  $false,  Position  =  0)]  $Min  =  1,  [Parameter(Mandatory  =  $false,  Position  =  1)]  $Max  =  100  )  for  ($X  =  $Min;  $X  -le  $Max;  $X++)  {  $Output  =  ""  if  ($X  %  3  -eq  0)  {  $Output  +=  "Fizz"  }  if  ($X  %  5  -eq  0)  {  $Output  +=  "Buzz"  }  if  ($Output  -eq  "")  {  $Output  =  $X  }  Write-Output  $Output  } 
```