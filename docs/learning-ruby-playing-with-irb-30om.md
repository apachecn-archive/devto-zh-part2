# 学习 Ruby:使用 irb

> 原文：<https://dev.to/damcosset/learning-ruby-playing-with-irb-30om>

## 简介

我有这本书，叫做*开始红宝石*，作者是彼得·库珀。这是我买的第一本关于编程的书。我读过...大概 20 页...出于某种原因，我几乎没有开始。从编程的角度来说，这本书已经变得越来越老了，但是我想学一点 Ruby，我觉得它可以完成教我基础知识的工作。我以前从未使用过 Ruby，所以我在这里从头开始。

## irb(交互式红宝石)

*注意:*我假设你的机器上安装了 Ruby。您可以通过在终端中运行`ruby -v`来检查。您应该会看到类似于
的内容

```
➜  ~ git:(master) ✗ ruby -v
ruby 2.3.3p222 (2016-11-21 revision 56859) [universal.x86_64-darwin17] 
```

Enter fullscreen mode Exit fullscreen mode

要启动 ruby 的 REPL IRB，只需输入 *irb* 并按回车键:

```
➜  ~ git:(master) ✗ irb
irb(main):001:0> 
```

Enter fullscreen mode Exit fullscreen mode

要退出 REPL，请键入 *exit* 并按 Enter 键。

对于这篇文章，我想我只会使用 irb。

### 基本照单全收

据我所知，Ruby 的目标是成为一种可读性很强的语言，接近英语。为了遵循我们人民的古老传统，我们必须向我们的终端打印友好的问候。让我们在终端中键入以下内容:

`10.times do print "Hello Ruby" end`

嗯，这是真的，它读起来像英语。你可以读给任何懂英语的人听，他们会明白你在说什么。我们可以很容易地猜到这个命令会做什么:

```
Hello RubyHello RubyHello RubyHello RubyHello RubyHello RubyHello RubyHello RubyHello RubyHello Ruby=> 10 
```

Enter fullscreen mode Exit fullscreen mode

打印 10 次“你好，鲁比”！！！现在，我认为忘记结尾的 *= > 10* 是安全的。我还不知道这意味着什么，:D

### 数学

当然，我们也可以做一些数学:

```
irb(main):005:0> 2+2
=> 4
irb(main):006:0> 3-2
=> 1
irb(main):007:0> 8-45
=> -37
irb(main):009:0> 4*5
=> 20
irb(main):008:0> 10/3
=> 3 
```

Enter fullscreen mode Exit fullscreen mode

请注意最后一次操作的结果。返回 3。我们知道这不是真的。如果你给 Ruby 提供整数，你将得到一个整数作为结果。你想要一个浮点数？在操作中提供浮点数:

```
irb(main):013:0> 10.0 / 3
=> 3.3333333333333335
irb(main):014:0> 10 / 3.0
=> 3.3333333333333335 
```

Enter fullscreen mode Exit fullscreen mode

Tadaaaa！！

另一个你可能会遇到的问题，如果你尝试以下:

```
irb(main):019:0> print 42 + " is the answer to the universe"
TypeError: String can't be coerced into Fixnum
    from (irb):19:in `+'
    from (irb):19
    from /usr/bin/irb:11:in `<main>' 
```

Enter fullscreen mode Exit fullscreen mode

或者:

```
irb(main):020:0> print "The answer to the universe is " + 6*7
TypeError: no implicit conversion of Fixnum into String
    from (irb):20:in `+'
    from (irb):20
    from /usr/bin/irb:11:in `<main>' 
```

Enter fullscreen mode Exit fullscreen mode

字符串和数字不能这样关联。要解决这个问题，你可以做:

```
irb(main):021:0> print "The answer to the universe is ",  6*7
The answer to the universe is 42=> nil

irb(main):022:0> print 42, " is the answer to the universe"
42 is the answer to the universe=> nil 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了一个逗号，而不是一个 *+* ，它像预期的那样工作！

## 物体！！到处都是物体！！

Ruby 是一种面向对象的编程语言。你能想到的任何概念都可以用 Ruby 中的对象来表示。如果您使用过另一种面向对象的语言，这可能看起来很熟悉。让我们看看 Ruby 是怎么做的。

让我们创建一个名为 Book 的概念:

```
irb(main):023:0> class Book
irb(main):024:1> attr_accessor :title, :author, :year
irb(main):025:1> end
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

所以，在 Ruby 中，我们称一个概念为一个*类*。所以我们创建了一个 Book 类。总是以大写字母开头。然后，我们为 Book 类定义三个属性:标题、作者和年份。最后， *end* 关键字告诉 Ruby 我们已经定义好了我们的概念。

*书*是概念，一个对象是基于那个概念的一个东西。例如，乔治·奥威尔写的书《1984》可以是基于书的概念的对象。

### 使用我们的概念

让我们创建一个变量来存储图书概念的一个*实例*。

```
irb(main):028:0> book_instance = Book.new
=> #<Book:0x007fdc17898208> 
```

Enter fullscreen mode Exit fullscreen mode

我们通过在 Book 类上调用 *new* 方法创建了一个新的 Book 实例。我们将该实例存储在一个名为 *book_instance* 的变量中。现在，只要你的 REPL 还活着， *book_instance* 就是 book 类的一个实例。让我们改变一下我们的 book 实例:

```
irb(main):029:0> book_instance.title = "1984"
=> "1984"
irb(main):034:0> book_instance.author = "George Orwell"
=> "George Orwell"
irb(main):035:0> book_instance.year = 1948
=> 1948 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们为 book_instance 属性赋予了一个值。正如您所看到的，对于赋予这些属性的数据类型没有任何限制。我可以把“1984”写成“1984”，这样就不会出现错误了。

### 检索数据

打印属性:

```
irb(main):036:0> puts book_instance.year
1948
=> nil
irb(main):037:0> puts book_instance.title
1984
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

*放*就像*印*。不同的是，*在末尾添加了一个换行符(并开始新的一行)。*

## 继承

有一本书的概念是好的，但是有很多不同的书可供选择。我们可以让概念继承其他概念。

```
irb(main):038:0> class Novel < Book
irb(main):039:1> attr_accessor :genre
irb(main):040:1> end
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

*小说*的概念继承了*书*的概念。这意味着小说将获得书的属性。让我们创建一个新的实例:

```
irb(main):041:0> novel_instance = Novel.new
=> #<Novel:0x007fdc17818aa8>
irb(main):043:0> novel_instance.title = "Notre Dame de Paris"
=> "Notre Dame de Paris"
irb(main):044:0> novel_instance.genre = "Romance"
=> "Romance" 
```

Enter fullscreen mode Exit fullscreen mode

我们的 *novel_instance* 有对书籍属性(比如书名)的访问权，有自己的属性(这里是流派)。**但是**，这本书没有获得小说的概念属性:

```
irb(main):045:0> newbook = Book.new
=> #<Book:0x007fdc17013650>
irb(main):046:0> Book.genre = "Romance"
NoMethodError: undefined method `genre=' for Book:Class
    from (irb):46
    from /usr/bin/irb:11:in `<main>' 
```

Enter fullscreen mode Exit fullscreen mode

而且，如果我们创建另一个概念，从 Book 继承，那个概念将只能访问它的属性，以及 Book 的属性，*而不是小说的属性。* 

```
irb(main):007:0> class Magazine < Book
irb(main):008:1> attr_accessor :type
irb(main):009:1> end
=> nil

irb(main):017:0> mag_instance = Magazine.new
=> #<Magazine:0x007fcabc05db90>
irb(main):018:0> mag_instance.type = "Periodical"
=> "Periodical"
irb(main):019:0> mag_instance.title = "GQ"
=> "GQ"
irb(main):020:0> mag_instance.genre = "ERROOOOR"
NoMethodError: undefined method `genre=' for #<Magazine:0x007fcabc05db90 @type="Periodical", @title="GQ">
    from (irb):20
    from /usr/bin/irb:11:in `<main>' 
```

Enter fullscreen mode Exit fullscreen mode

我们创造了一个继承自书本的*杂志*概念。杂志不能访问小说的属性，只能访问它的属性和它的父属性。

### 方法

类可以有方法，你告诉你的类去执行的指令。你需要在类内部定义它们，就像这样:

```
irb(main):021:0> class Kindle < Book
irb(main):022:1> def sayMotto
irb(main):023:2> puts "No more paper!!!"
irb(main):024:2> end
irb(main):025:1> end
=> :sayMotto

irb(main):026:0> new_kindle = Kindle.new
=> #<Kindle:0x007fcabc1630d0>
irb(main):027:0> new_kindle.sayMotto
No more paper!!!
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

听着，我们创造了 Kindle 的概念。在里面，我们使用 *def* 关键字来定义一个方法。这种方法叫做*格言*。这个方法的主体告诉它打印*没有纸了！！！*。注意有两个*结束*关键字。第一个表示我们已经完成了方法的定义，第二个表示类定义的结束。然后，我们可以通过在新创建的 Kindle 实例上使用其名称来调用我们的方法！

好了，写一篇文章就够了。我觉得我已经为一篇文章介绍了足够多的内容。下一集再见。

玩得开心！