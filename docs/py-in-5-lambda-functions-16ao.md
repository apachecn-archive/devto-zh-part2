# Py in 5: Lambda 函数

> 原文：<https://dev.to/kaelscion/py-in-5-lambda-functions-16ao>

[![py in 5 lambdas](../Images/bf2263fb93207df5ea8675a91679ad4c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fcHNCGM5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/knqzt9r827nbrk7465er.jpg)

> ***原载于编码鸭博客:【www.ccstechme.com/coding-duck-blog】T2*T5】**

今天的话题？Lambda 函数！首先，让我们回顾一下 Python 核心团队所说的 Lambda 函数的官方定义(也称为 Lambda 表达式)。根据文件:

***Lambda 表达式(有时称为 Lambda 形式)的句法位置与表达式相同。它们是创建匿名函数的简写方式；表达式 lambda 参数:表达式产生一个函数对象*T3】**

超清晰对吧？没有吗？你不是唯一这样想的人。对于许多程序员来说，Lambdas 是他们知道如何使用的特性，但并不真正理解他们实际上在做什么。事实是，Lambda 函数像常规函数一样工作，只是更短，更临时。像是荒谬的暂时的。“一次性使用”和“扔掉”这两个词立刻浮现在脑海中。让我们看一个例子。下面是一个传统的函数，它取一个数并对其进行立方(3 的幂或 num*num*num):

```
 def num_cubed(num):
  return num**3 
```

Enter fullscreen mode Exit fullscreen mode

干净利落。像这样的函数本身并不那么有用，只有在数据集上重复使用时才能真正找到自己的位置，比如我们想要立方体的充满数字的列表。使用上面的函数，我们可以通过几种方式来实现。

繁琐却*极具*可读性的方式:

```
 list_normal = [2, 4, 16, 10, 13, 7]
list_cubed = []
for num in list_normal:
  list_cubed.append(num_cubed(num)) 
```

Enter fullscreen mode Exit fullscreen mode

AAAA 和 the，*very*python 式的，一句话通过一个列表理解:

```
 list_normal = [2, 4, 16, 10, 13, 7]
list_cubed = [num_cubed(x) for x in list_normal] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个数字列表和一个立方数字列表。这和兰姆达斯有什么关系？他们也能做有趣的事情吗？是的，他们可以，正如我们在这里看到的:

```
 list_normal = [2, 4, 16, 10, 13, 7]
list_cubed = list(map(lambda x: x**3, list_normal)) 
```

Enter fullscreen mode Exit fullscreen mode

但是当数据集变得更大时，列表理解的使用往往会更快，因此它们是这种应用程序的首选。那么，兰姆达斯有什么好处，它们实际上是做什么的？？？

嗯，lambda 函数最好的优点(和缺点)出现在你只需要在你的代码中的几个地方执行一个特定的任务的情况下，而一个普通的函数最好用在它的功能在同一个代码文件中或者在不同的文件中被反复需要的时候。

你可以这样想:假设你是一个木匠，正在盖一栋房子，但就像一栋非常奇怪的房子，钉子有时会随机从木头里冒出来，就好像它们从来没有被钉进去过一样。在大多数情况下，你会在工作现场走来走去，寻找脱落的钉子。当你看到一个钉子时，你会从腰带上取下你那把可靠的锤子，就在它一直在的地方，用锤子敲 5 下就把钉子敲回去。那就是使用一个普通的函数。

如果你在房子里走来走去，看到有 50 个钉子排成一排伸出来，那就是 lambda。你可以用你的 5 号锤子把它们都敲进去。但是拖车里还有一个锤子，可以敲两次钉子。问题是，拖车在 100 码之外，由于这是一个超级特殊的锤子，当不使用时，它必须一直放在拖车里。

这样更有意义吗？一个普通的函数，在初始化的时候，被放在一个 Python 解释器可以随时使用的地方。然而，lambda 在需要的时候被创建，在完成的时候被删除。因此，虽然它的优势是能够在 20 个字符左右的时间内完成相当于 10 行代码的内容，但出于任何原因不得不“从拖车上取来”，除非是一个特殊的用例，即需要快速完成当天完成的相同任务，否则这没有多大意义。从长远来看，您保存的代码行远不足以证明每次看到松动的钉子时“走向拖车”所花费的时间。为此，只需在常规功能中使用您信赖的旧“皮带锤”。

所以我希望这能为你澄清 lambda 函数！如果你有任何问题，请留下评论，这样我可以对我可能忽略的事情提供更多的澄清(我只有 5 分钟的时间)。一如既往，走出去，建设，建设，建设！永远不要认为你不能学会如何使用文本编辑器和一点时间来改变世界。随着所有的垃圾让世界变得悲惨，所有的技术只是为了帮助足够多的人浪费足够多的时间来让开发人员变得富有，这个世界需要你和任何想要通过在命令行中键入这些有趣的短语来改善自己和这个世界的人。