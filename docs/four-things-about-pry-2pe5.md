# 关于窥探的四件事

> 原文：<https://dev.to/runtime-revolution/four-things-about-pry-2pe5>

<figure>[![](../Images/aadd80cab97ee70f18dc201f1c870cd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jKa6ttg1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/819/1%2AQTCgxaIflO13BjErDm69nw.png) 

<figcaption>准备行动撬。</figcaption>

</figure>

我喜欢 REPL，它是一个很棒的开发工具。任何像样的语言都有 REPL。 **Lisp** 有。 **Python** 有。哈斯克尔也是如此。而**红宝石**，显然也有！

REPL 代表**R**EAD**E**val**P**rint**L**OOP。这是一个与编程语言交互的命令行界面。大多数交互式编程语言都有 REPL。(我没查。)这个术语来源于 Lisp 的实现，其最基本的形式可以是一行代码:

```
(loop (print (eval (read)))) 
```

Lisp 复制器非常强大。我们可以检查堆栈树，(重新)执行一个框架(通常是一个函数调用)，改变框架参数并重新执行它，改变函数定义并重新执行调用它的框架。

这么高的期望，难怪 [**IRB**](https://ruby-doc.org/stdlib/libdoc/irb/rdoc/IRB.html) (Ruby 的默认 REPL)看起来没那么强大。我想你会同意我的观点。) [**撬**](http://pryrepl.org/) 是 IRB 的一个非常强大的替代品，包含了许多新功能。但还是…不能和 Lisp 的比。然而，有几个特点(我认为)会吸引新来者。我打算在这篇文章中讨论它们。

### 以前的评估值

在学习 Ruby 的时候，我经常需要做的事情之一就是使用之前评估的值。假设我已经计算了一个产品的税收，现在想得到最终价格:

```
pry(main)\> price, tax\_rate = 34, 0.08
=\> [34, 0.08]
pry(main)\> price \* tax\_rate
=\> 2.72
pry(main)\> final\_price = price + # damn! -\_- 
```

我花了很长时间才发现我们可以用 **_** (下划线)访问之前的值。用两个下划线 **__** 我们访问倒数第二个值。

```
pry(main)\> price, tax\_rate = 34, 0.08
=\> [34, 0.08]
pry(main)\> price \* tax\_rate
=\> 2.72
pry(main)\> final\_price = price + \_
=\> 36.72
pry(main)\> \_\_
=\> 2.72 
```

### 我在哪里？

一种调试方法是在代码中放置断点。使用 Pry，我们可以通过 **binding.pry** 表达式来实现。这会导致执行在该行停止。当这种情况发生时，ruby 的控制台会显示如下内容:

```
pry(main)\> Grid.generate(6, 6)

From: /Users/bjacquet/Projects/gol/gol.rb @ line 27 Grid.generate:

25: def self.generate(width, height)
 26: self.new(Array.new(height).collect do
 =\> 27: binding.pry
 28: Array.new(width).collect { rand(2) }
 29: end
[1] pry(Grid)\> 
```

它显示了执行停止的代码行，用= >表示。(作为第 27 行。)还有一些代码上下文和周围的行。我们还可以推断它在 Grid.generate 方法中停止了，并且在文件 **gol.rb** 中定义了它。

通过输入 **whereami** 命令，也可以随时获得这些信息。

如果这个方法的大小是它的两倍，我们仍然可以通过使用 **show-source** 命令来查看它的完整内容，比如:

```
pry(main)\> show-source Grid.generate

From: gol.rb @ line 25:
Owner: #\<Class:Grid\>
Visibility: public
Number of lines: 7

def self.generate(width, height)
 self.new(Array.new(height).collect do
 binding.pry
 Array.new(width).collect { rand(2) }
 end
 )
end
pry(Grid)\> 
```

### 回放历史

Pry 的 **hist** 命令真了不起。它可以让你查看、搜索和回放历史，等等。当在没有选项的情况下调用时，它显示 Pry 的所有历史，也就是我们给它的所有输入。

```
pry(main)\> hist
1: price, tax\_rate = 34, 0.08
2: price \* tax\_rate
3: price, tax\_rate = 34, 0.08
4: price \* tax\_rate
5: final\_price = price + \_
6: \_\_
7: Grid.generate(6, 6)
8: show-command Grid.generate
pry(main)\> 
```

使用 **-重放**选项，我们可以重新评估表达式:

```
pry(main)\> hist --replay 3..4
=\> [34, 0.08]
=\> 2.72 
```

我发现使用这个命令比用向上箭头旋转之前的输入更快。

### 让我马上解决这个问题

我们经常在 Pry 中输入多行表达式。比我们希望的更多的是，我们输入了一个有错误的行。为了解决这个问题，我们使用了 **amend-line** 命令。它将行号和替换代码作为参数。

```
pry(main)\> def hello
pry(main)\* puts “hello #{name}”
pry(main)\* amend-line 1 def hello(name)
1: def hello(name)
2: puts “hello #{name}”
pry(main)\* end
=\> :hello 
```

在这个例子中，我意识到我忘记了将 name 指定为方法参数。因此，我用新代码在第 **1** 行调用 amend-line 命令来纠正这个错误。然后，Pry 显示到目前为止提供的所有输入行，并等待更多。我输入 end，方法就定义好了。

### 更进一步

一个错误，很抱歉！值了。

Pry 还让我们有可能随时改变方法。为此，我们需要使用两个命令。首先，我们使用**编辑**命令改变方法。

```
pry(main)\> edit Grid.generate 
```

这将打开一个编辑器，光标位于方法定义的第一行。完成后，我们保存更改并退出编辑器。

接下来我们需要评估新的定义。命令 **reload-method** 就是这么做的。

```
pry(main)\> reload-method Grid.generate 
```

从现在开始，它将使用 Grid.generate 的新版本。

### 进一步阅读

Pry 有更多的功能可以发现。在准备这篇文章的时候，我发现了 amend-line 命令。它非常有用，我不得不把它包括进来。您应该了解的其他命令包括:

*   **show-model** ，列出了模型属性和类关联。
*   **show-stack** ，显示帧调用栈。(通常情况下，显示任何内容都需要很长时间。)

Pry 的 wiki 有很好的文档记录，也有其他资源的链接。

Pry 的帮助命令也很有用。我通过那里了解了这些特性中的大部分。(RTFM 综合症)

目前就这些。玩得开心点，态度好点！:-)

*我在*[*Runtime Revolution*](http://www.runtime-revolution.com/)*做 Rails 开发人员。我们也使用 Python、JavaScript……它们都有 REPL！我们也帮助创业公司达到下一个水平。*

* * *