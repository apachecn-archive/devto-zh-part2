# 弹性脚本是可以重新运行的脚本...安全地

> 原文：<https://dev.to/briannipper/resilient-scripting-is-scripting-that-can-be-rerun-safely-3115>

[![](img/743bca4833d2bcaaaa56346b9359dbf8.png)](https://2.bp.blogspot.com/-OpAbl076Y8o/W2yvk-FhKOI/AAAAAAAAHJo/mVd1WFJj4E094cwBvBxUb2E4xt5vjGOqgCLcBGAs/s1600/saftey-first.jpg) 

对于不同的人来说，制作一个有弹性的脚本可能意味着许多不同的事情，我认为其中一个重要的事情就是能够“安全地”重新运行脚本。我所说的安全是指尽量减少副作用，防止不良后果。

举例来说，假设我们有一个安装过程，我们需要记录运行安装时发生的详细情况。

为了简单起见，我们只关注日志需求。

我们需要一个日志文件，以便在安装时查看。一个简单的方法如下。

Set-Content log.txt "关于安装的信息"

漂亮简单，我们已经满足了要求。但是让我们看看我们是否能让这个衬垫更有弹性。

$ log filetime = Get-Date yymmdd hhmmss

$logFileName = $logFileTime + "_log.txt"

Set-Content $logFileName "关于安装的信息"

现在，即使我们多次运行此安装，我们也会为每个安装实例创建一个脚本，即使它发生在同一天的同一分钟。此外，使脚本更具弹性的一个有趣的副作用是，我们可以看到安装运行了多少次，因为我们对每次安装尝试都有一个文件。所以让它更有弹性的好处是复合的。

我希望这个简单的例子能帮助你亲爱的读者了解如何通过第二次修改使脚本更有弹性，即使是一行代码也能从第二次修改中受益。