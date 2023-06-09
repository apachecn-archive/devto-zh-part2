# 如何统一您的 PowerShell 控制台

> 原文：<https://dev.to/hf-solutions/how-to-uniquify-your-powershell-profile-2b35>

## 目录

*   [目录](#table-of-contents)
*   [PowerShell 配置文件基础知识](#powershell-profile-basics)
*   我们想要达到什么目标？
*   我们从哪里开始？
*   [定制标题/提示](#customizing-the-title-prompt)
*   [了解天气](#getting-the-weather)
*   [结论](#conclusion)

## PowerShell 配置文件基础知识

需要指出的是，PowerShell 概要文件的知识虽然在前面很有帮助，但并不是阅读本文和获得潜在价值的绝对必要条件。如果你确实希望提前获得一些信息，我强烈推荐你看一看由[脚本专家](https://social.technet.microsoft.com/profile/The+Scripting+Guys) Ed Wilson 撰写的[这篇博客文章](https://blogs.technet.microsoft.com/heyscriptingguy/2012/05/21/understanding-the-six-powershell-profiles/)。

在继续之前，我认为您需要了解 PowerShell 中的配置文件:

1.  配置文件是一个脚本，它允许您通过在启动控制台后立即执行来定制您的控制台。
2.  您可以利用 6 个配置文件来准确选择您想要定制的*控制台。*

## 我们想要达到什么目标？

也就是说，这将是一个很长的阅读，让我们开始吧。这是我当前的控制台启动时的图像(我们的最终目标):

[![Custom_PowerShell_Console](img/ca019040e28725fbab873e97a1edddaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yi2WvlKp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ljEMzRv.png)

我喜欢它显示当前的天气报告，我定制了提示行来显示当前的目录名和时间，而不是标准的外观:

[![Default_PowerShell_Console](img/21cb0e68fc1f119b71c9ad434efa2948.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DmOyYE0Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/oiReAl8.png)

### 待办事项

因此，这意味着我们需要完成 3 项“任务”来获得我们想要的外观:

1.  为我们的日期集成添加天气 API
2.  将当前时间添加到当前 shell 行的开头
3.  将当前目录添加到当前 shell 行的末尾

## 我们从哪里开始？

老实说，当我开始写这篇文章时，我意识到除了不得不重写我的个人资料中的一个名为`global:prompt`的函数之外，我不知道如何做到这一点。因此，我在位于`C:\Users\Alcha\Documents\WindowsPowerShell\Profile.ps1`的 [PowerShell Studio](https://www.sapien.com/software/powershell_studio) 中打开了我的个人资料脚本，并保存了一个备份副本(`Profile_Backup.ps1`)。然后，一个可爱的`Ctrl + A → Backspace`让我们重新开始。

首先，我们需要了解如何定制提示本身，因此快速的 google 搜索将让我们开始。第一个[三个](https://blogs.technet.microsoft.com/heyscriptingguy/2016/01/03/weekend-scripter-customize-powershell-title-and-prompt/) [结果](https://github.com/dahlbyk/posh-git/wiki/Customizing-Your-PowerShell-Prompt)对我来说已经足够了。就我个人而言，我更喜欢阅读示例而不是文档，所以在浏览了三个链接后，我选择关注第三个，因为它看起来相当长，而且充满了示例。读了一点后，我意识到它更侧重于专门的 [posh-git](https://github.com/dahlbyk/posh-git/) ，所以我切换回 Sean Kearney 写的 [the Scripting Guys response](https://blogs.technet.microsoft.com/heyscriptingguy/2016/01/03/weekend-scripter-customize-powershell-title-and-prompt/) 。

## 自定义标题/提示

虽然 Sean 首先介绍了如何定制窗口的标题，但这是我以前不知道的事情，最终在我的个人资料中发生了变化，所以新东西万岁！更具体地说，我通过添加这一行来自定义`$Host.UI.RawUI.WindowTitle`属性:

```
$Host.UI.RawUI.WindowTitle  =  (Get-Date).ToString() 
```

虽然它很简洁，但我不太喜欢它的格式:

[![Customized_WindowTitle-1](img/e6cd1e5077415a742449cc8c949fe2d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_CP5vgAN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/wmQGfxg.png)

经过[的一些修补](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-date?view=powershell-6#examples)，我登陆了`'%y/%m/%d %R'`格式。你可以像使用`Get-Date -UFormat '%y/%m/%d %R'`一样使用它，你的结果应该是这样的:

[![Customized_WindowTitle-2](img/ca08122b80d97886423cdede7b271382.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QxXIdIX---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/a9fQMeH.png)

现在我已经得到了我想要的头衔，接下来。接下来，Sean 提到了默认的提示函数，这是每次显示提示时执行的函数:

```
"PS $($executionContext.SessionState.Path.CurrentLocation)$('>'  *  ($nestedPromptLevel  +  1)) " 
```

答对了。现在我们可以处理我们的[待办事项](#To-Do)列表中的第三项了:`Add the current directory to the end of the current shell line`为此，我们需要获得当前的工作目录，这有很多种方法。我们可以使用存储当前变量的`$PWD`变量，也可以使用 [Get-Location](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-location?view=powershell-6) cmdlet，这就是我们将要做的。

默认情况下，`Get-Location`返回整个当前路径，但是我们只对它的最后一部分感兴趣，所以我们可以通过使用[分割路径](https://docs.microsoft.com/en-us/powershell/module/Microsoft.PowerShell.Management/Split-Path?view=powershell-6) cmdlet:
来获得这一部分

```
Split-Path  (Get-Location)  -Leaf  # Gives us 'Alcha' 
```

现在我们需要结合关于`Prompt`函数、如何获得当前日期/时间以及`Get-Location` cmdlet 的知识来获得我们想要的结果。这个函数将会像这样结束:

```
function  Prompt  {  $Host.UI.RawUI.WindowTitle  =  (Get-Date  -UFormat  '%y/%m/%d %R').Tostring()  Write-Host  '['  -NoNewline  Write-Host  (Get-Date  -UFormat  '%T')  -ForegroundColor  Green  -NoNewline  Write-Host  ']:'  -NoNewline  Write-Host  (Split-Path  (Get-Location)  -Leaf)  -NoNewline  return  "$ "  } 
```

我们需要`-NoNewLine`参数来将所有文本放在同一行。这也允许我们把它分成 5 个单独的行，把当前时间涂成绿色，而其余的保持默认的白色。最后，我移动了这一行来定制标题，因为现在每当提示符更新时，标题也会更新，它会显示“当前”时间。现在，我们的控制台在启动时应该是这样的:

[![Customized_Prompt-1](img/b7f88460b87c306e916df607177dd320.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1xx8DB-p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/irOTbHA.png)

至此，我们已经完成了待办事项列表中的两个项目，现在我们可以进入最简单的部分，即添加一个天气 API 来显示我们所在地区的当前状况。

## 获取天气

现在，为了获取天气信息，我们将使用 [OpenWeatherMap](https://openweathermap.org/) 服务，主要是因为我[在过去的某个时候发现了一个脚本](https://github.com/obs0lete/Get-Weather)，它会自动将输出格式化为您在第一张截图中看到的格式。为了使用这个服务，我们需要[获得一个 API 键](https://openweathermap.org/appid)，我们可以将它传递给脚本。

当你在 OpenWeatherMap 上创建一个帐户时，进入你的帐户的 API 密钥部分，如果你找不到，可以在这里找到。创建一个键，随你怎么命名，我只是把我的设为“默认”:

[![Open_Weather_Map_API_Key](img/7f995919a60821f2d2dcff5e793b2baf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x6DRP1hg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/RReC8Y9.png)

现在我们有了一个密钥，我们需要将`Get-Weather`脚本添加到我们的概要文件中。我的做法是在我的个人资料目录(`C:\Users\Alcha\Documents\WindowsPowerShell\`)中创建了一个名为 **Get-Weather.ps1** 的新脚本，然后从 GitHub 复制/粘贴该脚本的内容。我对变量命名约定和大小写有点执着，所以经过一些重构后，我最终得到了一个类似如下的脚本:

[![Get-Weather_Script-1](img/f1aa7c873c43e0fa669afcf7e2e17012.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nZcPQjKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/heE3AmH.png)

我不会涉及脚本的细节，因为这已经是一篇相当长的文章了😅但是 obs0lete 的“我的”版本的脚本的全文是作为要点可用的[，你只需要插入你的 API 密匙就可以让它正常工作。现在*使用*这一点，我们只需要在我们的配置文件中点源文件，然后调用`Get-Weather`函数来启动它:](https://gist.github.com/Alcha/f08a83486f7d064be9d4f73300a33872)

[![Weather_Added_To_Profile](img/c789eb5f69ef61db1dece4b2b2ceb386.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oIO7tWqD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/CpEy6aU.png)

当然，您必须将 **API 键**、**城市**和**国家**更改为适合您的值，但这相当简单。需要注意的是，我发现我所居住的*实际上是*城市，因为美国有许多其他城市有相同的名字。这意味着我从 API 得到了非常奇怪的天气结果，想象一下当天气晴朗的时候，它说外面在下雨。我通过找到邻近的大城市并使用它们的名字来解决这个问题:

[![Custom_PowerShell_Console-2](img/319f68a34cea81ed05704972f5cde9d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YiUpz7gC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/i3qpiCi.png)

## 结论

值得注意的是，我对最初的`Get-Weather`脚本做了足够的修改，以使它适合我，所以请务必做任何你可能需要的调整。例如，我提到了调整变量名，但我也将单位从公制更新为英制，并使其输出尝试 URL 的*...*这一行主要是为了调试，但也是为了测试 API 是否因为某种原因而无法工作。

我希望这不是一个太痛苦的阅读，我尽我所能只涵盖解决这个问题的有趣的位，并留下任何脂肪只是沉闷或涉及我的空间和无休止的滚动😂如果您有任何问题、意见或顾虑，请随时通过我的任何社交网络联系我，或发电子邮件至[admin@alcha.org](mailto:admin@alcha.org)。