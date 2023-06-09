# Python 切片 vs Ruby 块

> 原文：<https://dev.to/redfred7/--python-slices-vs-ruby-blocks---5h3f>

我的几个 Python 同事今天试图用 Python 的命名切片特性给我留下深刻印象。它的工作方式是这样的:

```
 s = list('helloworld!')
  => ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd', '!']
WORLD = slice(5, 10)
s[WORLD]
 => ['w', 'o', 'r', 'l', 'd'] 
```

Enter fullscreen mode Exit fullscreen mode

所以你可以拥有自己定制的切片机制，可以应用于任何列表。这很酷。这促使我演示如何用 Ruby 做同样的事情。幸运的是，在 Ruby world 中，我们有 blocks、procs 和 lambdas，这是允许我们向各个方向伸展和弯曲的终极橡皮泥。

```
​s = ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd', '!']
  => ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd', '!']
world = proc {|x | x.slice(5, 10)}
world[s]
 => ["w", "o", "r", "l", "d", "!"] 
```

Enter fullscreen mode Exit fullscreen mode

所以我们可以这样做:

```
first = proc { |x| x.slice(0) }
first[s]
=> "h" 
```

Enter fullscreen mode Exit fullscreen mode

或者甚至是我们不能用 Python 的命名切片做的事情，因为它不允许我们将接收者作为参数传递给块(下面例子中的 x)

```
last = proc {|x| x.slice x.length-1, x.length}
last[%w(dog rabbit fox cat)]
=> ["cat"] 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
median = proc {|x| x.slice(x.length / 2) }
median[%w(dog rabbit cat)]
=> "rabbit" 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们不仅仅局限于切片数组，

```
domain_extractor = proc { |x| x.gsub(/.+@([^.]+).+/, '\1') }
domain_extractor["fred@mydomain.co.uk"]
=> "mydomain" 
```

Enter fullscreen mode Exit fullscreen mode

由于块只是一个匿名的 Proc 对象，我们可以在任何接受 Proc 参数

的方法中使用它

```
email_list = ["fred@mydomain.com", "john@gmail.com", "mary@yahoo.co.uk"]
=> ["fred@mydomain.com", "john@gmail.com", "mary@yahoo.co.uk"]
email_list.map(&domain_extractor)
=> ["mydomain", "gmail", "yahoo"] 
```

Enter fullscreen mode Exit fullscreen mode

块和进程(又名 lambdas)是一些快速的、可重用的功能的理想选择，或者当定义一个完整的方法太多时，但也适用于更严重的应用，如回调和延迟执行。依我看，它们是使 Ruby 成为如此灵活和强大的语言的基础部分。学习它们，使用它们，享受它们:)