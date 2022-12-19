# Ruby 方法聚焦:切片

> 原文：<https://dev.to/jeremy/ruby-method-spotlight-slice-1f3j>

在我们开始之前，先简单说明一下:这篇文章假设你对 Ruby 数据结构有基本的了解，因为我实在想不出你为什么会想阅读这篇文章。具体来说，你应该知道什么是数组和字符串，你应该对返回值有一些了解。

### 它有什么作用？

`#slice`是一种操作数组、字符串和(从 Ruby 2.5.0 开始)散列的方法。我们现在只关注数组，因为逻辑基本上是一样的，但是请记住，您也可以对字符串和散列调用`#slice`。`#slice`允许你切入一个数组并选择特定的元素。

### 它是如何工作的？

假设你有一系列水果:

`fruit = ["apple", "banana", "orange", "grapefruit", "tomato"]`

您可以在数组上调用`#slice`，向它传递一个特定的索引，它将返回该索引处的对象。使用参数`(2)`调用`fruit.slice`将返回字符串对象`"orange"`，因为`"orange"`位于`fruit`数组的索引 2 处(注意数组元素从 0 开始索引，因此`"apple"`位于索引 0 处，`"banana"`位于索引 1 处，依此类推)。让我们去 IRB 看看:

```
 2.5.1 :001 > fruit = ["apple", "banana", "orange", "grapefruit", "tomato"]
 => ["apple", "banana", "orange", "grapefruit", "tomato"]
2.5.1 :002 > fruit.slice(2)
 => "orange" 

```

如果你想要香蕉、橙子和葡萄柚，你可以用起始索引*和元素长度*调用`#slice`，它将返回数组中的这些对象。

```
 2.5.1 :001 > fruit = ["apple", "banana", "orange", "grapefruit", "tomato"]
 => ["apple", "banana", "orange", "grapefruit", "tomato"]
2.5.1 :002 > fruit.slice(1,3)
 => ["banana", "orange", "grapefruit"] 

```

这将选择从索引 1 开始到索引 3 结束的所有元素。

同样，我们可以使用一个范围:

```
 2.5.1 :002 > fruit.slice(1..3)
 => ["banana", "orange", "grapefruit"] 

```

利用这种方法的另一种方式是调用数组，然后简单地选择您想要的元素，就像这样:

`fruit[2]`，返回
`=> "orange"`

如果您需要处理特定元素的特定部分，这将非常有用。例如，如果您的宝宝真的想要一个“娜娜”，而不是一个“香蕉”，您可以通过调用`fruit[1]`选择索引 1 处的`"banana"`，并将`[2,5]`链接到它上面，这将选择数组索引 1 处的字符串对象，然后选择字符串对象本身索引 2 到 5 处的字母。像这样:

```
 2.5.1 :001 > fruit = ["apple", "banana", "orange", "grapefruit", "tomato"]
 => ["apple", "banana", "orange", "grapefruit", "tomato"]
2.5.1 :002 > fruit[1][2,5]
 => "nana" 

```

我希望你开始看到这个方法有多强大。你可以用它做很多事情，特别是当你使用正则表达式或者其他方法的时候。
用厨师刀，你可以用多种方式拿取食物并更换，同样的方法也适用于`#slice`方法，但没有割伤自己的风险。加入 IRB，玩一会儿，亲自看看它能做什么。Ruby 是为了让程序员开心而设计的，所以有了 Ruby，

[![happiness is a piece of cake](img/6d270f3aca692ebfe1f2a690d66eb237.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PXNz44ZR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lombsqm6son61so6nf78.jpg)

`#slice`蛋糕？

关于散列的一个小提示:就像我上面说的，你可以在散列上使用`#slice`,但是只能在最新的 Ruby 版本中使用。你像这样使用它:

```
 2.5.1 :003 > basket = {fruit: "apples, bananas",  vegetables: "carrots, onions, celery", grains: "rice, cornmeal, quinoa"}
 => {:fruit=>"apples, bananas", :vegetables=>"carrots, onions, celery", :grains=>"rice, cornmeal, quinoa"}
2.5.1 :004 > basket.slice(:vegetables)
 => {:vegetables=>"carrots, onions, celery"}
2.5.1 :005 > basket.slice(:vegetables, :grains)
 => {:vegetables=>"carrots, onions, celery", :grains=>"rice, cornmeal, quinoa"} 

```

关于`#slice`的更多信息，请参见 [ruby 文档](https://ruby-doc.org/core-2.2.0/Array.html#method-i-slice)，特别是看看我们没有涉及的概念，比如什么情况导致`#slice`返回`[]`或`nil`，以及`#slice!`做了什么。
关于使用字符串`#slice`的更多信息，点击[这里](https://ruby-doc.org/core-2.2.0/String.html#method-i-slice)关于使用散列`#slice`的更多信息，点击[这里](https://docs.ruby-lang.org/en/2.5.0/Hash.html#method-i-slice)

如果您有任何问题或意见，请给我发电子邮件，或在下面评论。

下次见，编码快乐！