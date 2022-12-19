# 函数式编程教学:两种大图方法

> 原文：<https://dev.to/eddroid/teaching-functional-programming-two-big-picture-approaches-3nli>

函数式编程(FP)的历史和面向对象编程(OOP)一样长，甚至更长。但是它只是(相对)最近才流行起来，特别是在 JavaScript 社区。为什么？

我在 21 世纪初去了麻省理工学院。我的课本是《计算机程序的结构和解释》(SICP - *恶心尿* ) 。所以我的第一门正式教授的编程语言[是函数式的](https://en.wikipedia.org/wiki/Scheme_(programming_language))。后来我在工业界工作了十多年，几乎没有想过计划生育。现在我震惊地得知，我已经记不太清楚的大学课本被认为是[“函数式编程圣经”](https://www.reddit.com/r/csharp/comments/7pw5mk/can_anyone_recommend_a_book_for_functional/dsklg5s/?st=jo0j3dck&sh=810c43d5)。

不要误解我。很好的教材。我确信这让我成为了一名更好的程序员。但是在我的 Java/action script/PHP/Python/Ruby/JavaScript 职业生涯中，FP 并不是我经常应用的东西。OOP 模式占主导地位。

然后我在 Wyncode 学院教了四年书，发现自己试图向新人解释一些 FP 概念。在 OOP 主导的世界里，很难解释 FP。太*不一样了*。

学了 OOP，为什么 FP 难了那么多？

相关问题:为什么 FP 这么久才流行起来？为什么我不谈论在 FP 主导的世界中学习 OOP 的技术？

我们编码社区的人需要理解为什么 OOP->FP 转换如此难教。[像宗教一样宣扬 FP](http://degoes.net/articles/fp-is-not-the-answer)重复[导致 FP 在这个行业长期萎靡不振的同样的错误](http://wingolog.org/archives/2009/03/24/international-lisp-conference-day-two)。

很多对 FP 的介绍都缺少了一些东西。这不仅仅是一种另类的编程风格。这是一种新的思维方式。当向我的学生介绍新的大东西时，我会试着让他们慢慢适应。这些相同的技巧可能也适用于来自 OOP 背景的更有经验的程序员。

我在 Wyncode 使用的一个技巧是讲故事，这是一个很难的概念的开始。如果我能让我的学生理解背景——大背景——我发现以后解释技术细节会更容易。

所以这里有两个介绍 FP 的大图策略——特别是对面向对象的观众。

## 大图 1:历史

有时候从头开始也不错:*电脑是怎么工作的？*

最常见的(流行？通俗易懂？)计算的模型是[图灵机](https://turingmaschine.klickagent.ch/einband/#1_+_1)。FP 程序员抱怨的*状态*就在图灵机中的我们眼前。用于操作该机器的算法表示不同状态之间的转换，例如，从一些盒为*开* / *关* (1 或 0)到一些其它盒为*开* / *关*。

[https://www.youtube.com/embed/E3keLeMwfHY](https://www.youtube.com/embed/E3keLeMwfHY)

[如果我们试着想象两个图灵机同时在磁带的同一个扇区上运行](https://www.youtube.com/watch?v=E3keLeMwfHY)，我们就能开始理解为什么 OOP 中的“共享状态”和并发是很难解决的问题。但那是以后的事了。

图灵机是一个*通用机*。它可以用来解决每一个可解的(*有效可算的*)数学和逻辑问题。这个简单的操作集合——向左移动、向右移动、写点、读点、擦除点——足以(给定足够的时间和资源)解决宇宙中的每一个数学问题。[这就是艾伦·图灵在 1936 年](https://en.wikipedia.org/wiki/Turing_machine)证明的。

在许多方面，图灵机是计算机“工作”的方式。

但这也是电脑的工作方式。

[![full adder circuit](../Images/923ab2b1d998a5e3bc1a3fca812dee56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gE_0v-yF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8v6bdt0jecbz5f8oi73i.gif) 
*[全加器电路](https://en.wikipedia.org/wiki/Adder_(electronics))*

这是一个加法电路。它是计算机中央处理器内部的一种部件。

这不是图灵机。不是万能的。只是加法。它不能(轻易)被“重新编程”。

也没有类似图灵机的“状态”。向与要相加的数字相对应的输入施加电压，并检测与和相对应的输出中的电压。一旦电压被切断，答案就消失了。没有“磁带”可以随意阅读或操作。两个电路不能同时在同一个逻辑门上操作。(我不*认为*他们可以，但我肯定会有人评论证明我错了。)

这条线路也很快。虽然经典的图灵机在某种介质上来回翻转 1 和 0，但这种电路通过导线以电的速度运行。没有活动部件。

电路是一种不同的计算模型。每个逻辑门(与、或、与非、或非、异或等。)都是纯函数。它们接受输入并产生没有副作用的输出。如果我们所拥有的只是创造和组合这些“函数”的能力，我们也可以解决宇宙中每一个可解的数学问题。也是在 1936 年，阿隆佐·邱奇证明了这一点。

所以我们有两种不同的计算模型:图灵机的 0 和 1 的小盒子(对象)和阿隆佐教堂的用逻辑门构建的 lambda 演算(功能)。哪个是正确的？

有一段时间，有一场关于抽象图灵机是否能解决与 lambda 演算相同的数学问题的辩论(反之亦然)。最终他们被证明是等同于 T2 的 T3。

等价意味着它们同样强大。任何可以为图灵机编写的算法也可以用函数来编写。所以任何可以用图灵机软件编写的程序也可以用电路硬件来表示。

“用硬件编程”是什么意思？

我们可以看到“硬件编程”体现在[专用集成电路(ASICs)](https://en.wikipedia.org/wiki/Application-specific_integrated_circuit) 中。电路可以被“编程”来非常快速地做一件事，就像[挖掘比特币](https://www.digitaltrends.com/computing/what-is-an-asic-miner/)或[下棋](https://www.extremetech.com/computing/76552-project-deep-blitz-chess-pc-takes-on-deep-blue/2)。

自从丘奇-图灵论题的提出，我们有了两种编程选择。硬件更快，软件更慢。软件出错？只要按删除键，再试一次。硬件出错？是时候拿个烙铁了。这是典型的工程设计权衡。

假设我们有一个用 OOP 风格编写的算法，我们想把它转换成 ASIC。以 FP 风格重写程序可能是一个好策略，这样它可以更好地映射到电路图的域。大多数编程语言都足够灵活，可以做到这一点，但有些语言在这方面做得更好。

```
# Elixir pipes
"1" |> String.to_integer() |> Kernel.*(2) # returns 2 
```

Enter fullscreen mode Exit fullscreen mode

许多面向 FP 的语言看起来更像电路。具体来说， [Unix](https://en.wikipedia.org/wiki/Pipeline_(Unix)) 、[elixin](https://en.wikipedia.org/wiki/Elixir_(programming_language))、 [F#](https://blogs.msdn.microsoft.com/chrsmith/2008/05/09/f-in-20-minutes-part-ii/) 、 [JavaScript(也许有一天)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Pipeline_operator)等中的“管道操作符”让代码看起来像一个电路图:输入进入左边，流经许多“门”(管道)，直到它们被转换成右边的最终输出。某些语言使用的管道运算符(`|>`)看起来像逻辑门，这可能不是巧合。

[![NOT logic gate](../Images/cbd12e5c63b1a341d3a6e2cb2e8846cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5BuyOqMY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nqvfb8s3e94ktq0crldc.png)
*[`NOT`门](https://en.wikipedia.org/wiki/Inverter_(logic_gate))*

戴上我的编码教师帽子，介绍 FP 的一个好的“大图”方式是从谈论电路如何工作、它们如何被“编程”以及我们如何用代码模拟电路图开始。

## 大图二:哲学

我在计算机科学学位时辅修了哲学，所以我着迷于这两个研究领域的交叉。我发现在教新程序员时，谈论重叠是有帮助的，尤其是那些有人文背景而不是 STEM 背景的人。

功能对等是功能语言学中一个重要的哲学概念。

也许证明这种等价性的最好例子是汤姆·斯图尔特的伟大文章[“从零开始编程”](http://codon.com/programming-with-nothing)。

Stuart 演示了一个程序(特别是无处不在的 FizzBuzz)是如何完全脱离函数编写的。我不会在这里重复整个练习，但我将借用他对数字如何完全用函数来表示的解释( [Church 编码](https://en.wikipedia.org/wiki/Church_encoding))。

首先将 *zero* 定义为一个函数，它接受一个函数参数，但不做任何事情。

```
# Ruby
ZERO = -> (func) { 
  # does nothing
  func
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们可以将所有自然数定义为接受函数参数的函数，并将其称为`n` -times。

```
ONE = -> (func) {
  # calls it once
  # same as "func.call()"
  func[]
  func
}

TWO = -> (func) {
  # calls it twice
  func[]
  func[]
  func
} 
```

Enter fullscreen mode Exit fullscreen mode

要测试这些“函数编号”，请向它们传递一个测试函数。

```
HELLO = ->() { puts "hello" }

# same as "ZERO.call(HELLO)"
ZERO[HELLO] # nothing displayed
ONE[HELLO]  # one "hello" displayed
TWO[HELLO]  # "hello" twice 
```

Enter fullscreen mode Exit fullscreen mode

这种函数数字表示可能很难使用和调试。

```
p ZERO
# outputs #<Proc:0x000055d195ae57b0@(repl):3 (lambda)> 
```

Enter fullscreen mode Exit fullscreen mode

所以为了更容易操作，我们可以定义一个方法，将这些函数编号转换成我们习惯的对象编号。

```
# convert number function into number object
def to_integer(func)
  # count how many times counter is called
  n = 0
  counter = ->() { n += 1 }
  func[counter]
  n
end

p to_integer(ZERO) # 0
p to_integer(ONE)  # 1
p to_integer(TWO)  # 2 
```

Enter fullscreen mode Exit fullscreen mode

该转换器创建一个计数函数，并将其传递给数值函数。`ZERO`函数将调用它 0 次，`ONE`函数将调用它 1 次，以此类推。我们跟踪计数器被调用多少次以获得结果。

给定这些函数编号定义，我们可以实现加法。

```
ADD = -> (func1, func2) {
  -> (f) { func1[func2[f]] }
}

sum = ADD[ZERO, ZERO]
p to_integer(sum) # 0

sum = ADD[ZERO, ONE]
p to_integer(sum) # 1

sum = ADD[ONE, ONE]
p to_integer(sum) # 2 
```

Enter fullscreen mode Exit fullscreen mode

如果`TWO`调用一个函数两次，那么`ADD[TWO, TWO]`将返回一个调用其参数四次的函数号(函数号`FOUR`)。

这是一个令人费解的练习。当我读到*“从无到有编程”*的结尾时，我感觉这是一个基本计算机科学概念的巧妙应用的有趣产品，但不是我日常工作中可以使用的东西。

这正是我(我怀疑还有许多其他人)对 FP 的总体感觉——它很聪明，但似乎没什么用处。如果我们希望让 FP 技术更受欢迎，那种不必要的复杂性正是我们需要解决的问题。

所以比起教会数字 T1，开始教授 FP 更好的地方是 T2 矩阵 T3。

在那部 1999 年的科幻电影中，大部分人类感知的现实其实是一个名为“黑客帝国”的模拟。几个月前[埃隆·马斯克提出这个“模拟假说”可能是真实的](https://www.space.com/41749-elon-musk-living-in-simulation-rogan-podcast.html)，引发了数周关于这个话题的“哲学 101”级媒体。

矩阵和 FP 有什么关系？

形而上学的争论,“模拟假说”只是其中的一种回应，这种争论非常古老，有时复杂得令人麻木。所以我的总结并不公平。但是重要的是我们没有证据证明我们周围的世界是真实的。也许世界上有真实的物体，或者我们只是[装在罐子里的大脑](https://en.wikipedia.org/wiki/Brain_in_a_vat)。

所以至少有两种相互矛盾的理论，比如，数字一*是*。它是一个我们可以与之互动(触摸和感觉)的东西(名词，物体)吗？还是一个动作(一个动词，一个函数)，一个作用于世界，却没有被具体化的东西？

功能一号是一号的*模拟*。它的*功能等同于*目标一，意味着它*做*目标一能做的一切。例如，我们可以用它做算术。

但是它并不像 OOP 中的对象那样“在那里”。这是矩阵模拟。它没有固有属性——它*不是* x，它只是*做* x

举个不那么抽象的例子，你坐的椅子是真实存在的还是仅仅有[力压着你的身体](https://futurism.com/why-you-can-never-actually-touch-anything)？“椅子”可能是现实世界中存在的椅子对象，也可能是椅子的功能:一种(希望是舒适的)推着你的力量，没有潜在的客观基础。

[![red delicious apple](../Images/7359a3aa594cbfb0899fbfe89992a05f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CZ0K2Rr6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1c5ljsjk363aiztnooxh.jpg) 
*[一个红色可口的苹果](https://en.wikipedia.org/wiki/Red_Delicious)*

考虑颜色。一个红色的美味苹果是真的红色(形容词描述名词)还是表现为红色(动词)？颜色是真实的底层苹果物体的固有属性，还是仅仅是苹果函数被编程为当光线照射到它时所做的一个动作？苹果是真的还是仿真的？

```
# A "real" apple
class Apple
  attr_reader :color
  def initialize
    @color = "ruby red"
  end
end

p Apple.new.color # "ruby red" 
```

Enter fullscreen mode Exit fullscreen mode

```
# A "simulated" apple
APPLE = -> (applied) {
  return "ruby red" if applied == "light"
}

p APPLE["light"] # "ruby red" 
```

Enter fullscreen mode Exit fullscreen mode

这个哲学概念的困难是一个很好的隐喻，解释了为什么 FP 在 OOP 主导的世界里如此难教。为了帮助学生理解，首先要打开他们的思维，让他们了解一个完全由“功能”构成的世界的可能性。从那个*大图*概念开始，然后过渡到世界的 FP 模型:它们如何不同于面向对象的表示，却保持相同的结果。请一位有经验的 OOP 开发人员考虑将`class`重写为它的等效函数。

## 结论

从 OOP 过渡到 FP 可能很难。这不仅仅是不同的编程风格。这是世界的另一种模式。我们越善于让学生适应这种范式转变，就越容易避免半个世纪以来忽视编码器工具箱中这个有用的工具。

写作就像代码一样是可调试的。所以我决定澄清一下，我正在向有 OOP 头脑的程序员介绍 FP 的教学策略。FP 编程本身并不难。需要支持的是范式转变。