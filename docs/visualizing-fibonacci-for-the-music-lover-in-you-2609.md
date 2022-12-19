# 可视化斐波那契:为了你心中的音乐爱好者！

> 原文：<https://dev.to/dan_mcm_/visualizing-fibonacci-for-the-music-lover-in-you-2609>

*第一次写文章，所以原谅错别字和错误！*

## 常见问题:编程斐波那契数列

在我最近的计算机科学转换课程中，我学习了 Python 和 Java 的入门知识。这两种语言面临的一个共同挑战是编写一个程序来显示斐波那契数列的第 n 个数字。这似乎是大多数程序员在学习一门新语言时常见的敲门砖。

[![The Fibonacci Series](../Images/edf528f62c38a78dda3ae86832323d46.png "The Fibonacci Series")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iCYNnWm6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cartesianproduct.files.wordpress.com/2011/03/fib.png)

我发现自己想把在课堂上学到的东西应用到一个独立的项目中，我无意中发现了把斐波那契数列想象成乐谱的想法。我不太记得何时何地听说过这个想法——可能是从马特·帕克、汉娜·弗莱或我的一位讲师那里听说的。我在谷歌上搜索了这个话题，没有发现任何斐波纳契数列在乐谱上的直接实际实现。这就是我的任务。回想起来，也许快速搜索一下 github 会得到更多的结果——但我那时还是一个比现在更年轻、更天真的开发者。

## Python -哪个库？

好了，我有了整个项目的想法——生成第 n 个斐波那契数列，并以某种方式在乐谱上可视化这些数据。经过一些快速的研究，我决定将名为 [Mingus](http://bspaans.github.io/python-mingus/) 的 Python 库与程序 [Lilypond](http://lilypond.org/) 结合使用

## 如何不浪费时间重新构建 python2 库来使用 python3...

这是不言而喻的，当时我经验不足，容易犯这些错误。我最后在运行 Python3 的时候下载了一个运行在 Python2 上的 Mingus 版本。我没有查找 Python 3 包是否存在，而是对包进行了一些手动调整以使其工作。哦！对于年轻的开发者，一定要检查任何已安装的包的 python 版本！

## 半音阶

虽然不是最优雅的解决方案，但我最终基本上修改了以前开发的大学解决方案，该方案输出 Fibonacci 数列到第 n 个数字，并使用该数字的模数来确定它将代表哪个音符。使用 12 的模数，我能够将不同的数字与半音阶中所有的音符联系起来，从 C 的基音开始(为了方便起见，我只使用了升半音)

[![The Chromatic Scale](../Images/bd5938d04df897755dffb9124afb1e26.png "The Chromatic Scale")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5NpxkcxB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pianowizardacademy.com/campus/files/2015/10/ChromScale.png)

在建立了斐波那契数列和音阶半音音符之间的联系后，唯一的“困难部分”就是弄清楚如何将这些生成的音符转换成乐谱。

在导入 Mingus 库之后，这实际上非常简单！
代码看起来有点像这样(是的，我知道这有点低效——自从代码最初被组合在一起后，我就没有回去重构过代码):

```
from mingus.containers import Bar
from mingus.containers import Composition
from mingus.containers import Track
from mingus.containers import NoteContainer
import mingus.extra.LilyPond as LilyPond

x = int(input('Song Length (number of notes): '))

song = []
b = Bar() # creates new bar element for Mingus = has limit of 4/4 timing by default
b.set_meter((4,4))

c = Composition()
c.set_author('Daniel McMahon', 'daniel40392@gmail.com')
c.set_title('Ode to Fibonnachi')
t = Track()
t.add_bar(b)
c.add_track(t)

def fib(n):
    """Function that generates notes based off the Fibonnachi Series"""
    a=0
    b=1
    print('Generating Music.... Please Wait....',end='')

    for i in range (1,n+1):
        fib=a+b
        if fib%12==1:
            song.append('C')
        elif fib%12==2:
            song.append('C#')
        elif fib%12==3:
            song.append('D')
        elif fib%12==4:
            song.append('D#')
        elif fib%12==5:
            song.append('E')
        elif fib%12==6:
            song.append( 'F')
        elif fib%12==7:
            song.append('F#')
        elif fib%12==8:
            song.append('G')
        elif fib%12==9:
            song.append('G#')
        elif fib%12==10:
            song.append( 'A')
        elif fib%12==11:
            song.append('A#')
        elif fib%12==0:
            song.append('B')
        a=b
        b=fib

    #this code will print song in reverse
    #for i in reversed(song):
    #    print (i + ' ,', end='')

    n = NoteContainer(song)

while x != '':
    fib(x)
    print()
    for note in song:
            b + note
            c.add_note(note)
    comp = LilyPond.from_Composition(c)
    LilyPond.to_pdf(comp, "Ode_to _Fibonnachi")
    print("Song completed - check desktop for exported PDF")
    x = int(input('Song Length (number of notes): ')) 
```

Enter fullscreen mode Exit fullscreen mode

## 结果呢？

下面是结果的一个小快照——输出的音乐出现了一个有趣的模式，每 25 个左右的音符显示一个重复的模式！

[![Ode to Fibonacci](../Images/9908d4fbdd450e65fd3d6a04020782d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wZXEElHF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dwoojll9ythz66ywwuqb.png)

GitHub repo 的链接[在这里](https://github.com/daniel40392/MusicPrograms)可用，示例输出为 PDF 文件。

## 一些额外的想法和实验...

这种乐谱生成方法可以很好地与其他数学级数配合使用——我想采用的直接方法包括加泰罗尼亚和菲戈拉特级数。上面的关联回购协议中也包含了一些斐波纳契票据生成示例。

尝试从半音音阶的不同音符开始也是值得的，但是音符之间的音程仍然保持不变。

这个项目的下一个逻辑步骤是从生成的乐谱中获取音乐输出。两年前，我在一台旧的 Windows 笔记本电脑上开始尝试这个项目，我将不得不很快回到这个项目，看看我是否可以集成一些现场音乐反馈。

我在存储库中采用的另一个有趣的方法是使用 [NLTK](http://www.nltk.org/) 库来生成不同斐波那契数列中音符出现的频率。使用不同的数学级数，你会得到一些有趣的结果——我建议你自己尝试一下！

## 一些最终的想法

这个项目本身被用作将我对音乐的热爱融入我的编码的基本方式。当试图用一种新的语言实现斐波那契数列时，它可能被认为是一个非常无聊的话题，但是当你添加一些基本的额外步骤时，你可以产生一些真正有趣和有创造性的东西！

我总是鼓励新开发人员以新的和创造性的方式探索编程语言的基本构件。就个人而言，你可能会发现这更有价值——而且这是建立个人投资组合的好方法，在未来的工作面试中也有额外的话题可以聊！

也许你已经尝试过用不同的语言编写类似的程序，并获得了有趣的结果——我很想知道——请务必在下面留下你的反馈或评论。

再次原谅任何打字错误或格式问题-这是我的第一个希望在这里的许多职位！

最诚挚的问候，丹尼尔·麦克马洪