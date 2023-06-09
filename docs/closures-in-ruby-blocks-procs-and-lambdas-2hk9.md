# Ruby 中的闭包:Blocks、Procs 和 Lambdas

> 原文：<https://dev.to/appsignal/closures-in-ruby-blocks-procs-and-lambdas-2hk9>

在 Ruby Magic 中，我们喜欢深入到我们日常使用的事物背后的魔力中，以了解它们是如何工作的。在这个版本中，我们将探索块、进程和 lambdas 之间的差异。

在具有一级函数的编程语言中，函数可以存储在变量中，并作为参数传递给其他函数。函数甚至可以使用其他函数作为返回值。

闭包是环境的一级函数。环境是创建闭包时存在的变量的映射。闭包将保留对这些变量的访问，即使它们是在另一个作用域中定义的。

Ruby 没有一级函数，但是它有 blocks、procs 和 lambdas 形式的闭包。块用于将代码块传递给方法，而 procs 和 lambda 允许将代码块存储在变量中。

## 区块

在 Ruby 中，*块*是代码片段，可以被创建以便以后执行。块被传递给在关键字`do`和`end`中产生它们的方法。其中一个例子是`#each`方法，它循环遍历[可枚举的](https://blog.appsignal.com/2018/05/29/ruby-magic-enumerable-and-enumerator.html)对象。

```
[1,2,3].each do |n|
  puts "#{n}!"
end

[1,2,3].each { |n| puts "#{n}!" } # the one-line equivalent. 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，一个块被传递给`Array#each`方法，该方法为数组中的每一项运行该块，并将其打印到控制台。

```
def each
  i = 0
  while i < size
    yield at(i)
    i += 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个简化的`Array#each`示例中，在`while`循环中，`yield`被调用来执行数组中每一项的传递块。请注意，此方法没有参数，因为块是隐式传递给方法的。

### 隐含块和`yield`关键字

在 Ruby 中，方法可以隐式和显式地接受块。隐式块传递通过在方法中调用`yield`关键字来工作。`yield`关键字比较特殊。它查找并调用传递的块，因此您不必将该块添加到该方法接受的参数列表中。

因为 Ruby 允许隐式块传递，所以可以用一个块调用所有方法。如果它不调用`yield`，该块被忽略。

```
irb>  "foo bar baz".split { p "block!" }
=> ["foo", "bar", "baz"] 
```

Enter fullscreen mode Exit fullscreen mode

如果被调用的方法*产生了*，那么被传递的块将被找到，并使用传递给`yield`关键字的任何参数进行调用。

```
def each
  return to_enum(:each) unless block_given?

  i = 0
  while i < size
    yield at(i)
    i += 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个例子[返回一个`Enumerator`](https://blog.appsignal.com/2018/05/29/ruby-magic-enumerable-and-enumerator.html) 的实例，除非给出一个块。

`yield`和`block_given?`关键字在当前范围内查找块。这允许隐式传递块，但是防止代码直接访问块，因为它没有存储在变量中。

### 明确地传递街区

我们可以在方法中显式地接受一个块，方法是使用一个与号参数(通常称为`&block`)将它作为一个参数添加。由于块现在是显式的，我们可以直接在结果对象上使用`#call`方法，而不依赖于`yield`。

`&block`参数不是一个合适的参数，所以用块以外的任何东西调用这个方法都会产生一个`ArgumentError`。

```
def each_explicit(&block)
  return to_enum(:each) unless block

  i = 0
  while i < size
    block.call at(i)
    i += 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当一个块像这样被传递并存储在一个变量中时，它被自动转换成一个 *proc* 。

## 进程

“proc”是`Proc`类的一个实例，它包含一个要执行的代码块，并可以存储在一个变量中。要创建一个 proc，您调用`Proc.new`并向它传递一个块。

```
proc = Proc.new { |n| puts "#{n}!" } 
```

Enter fullscreen mode Exit fullscreen mode

因为过程可以存储在变量中，所以它也可以像普通参数一样传递给方法。在这种情况下，我们不使用&符号，因为过程是显式传递的。

```
def run_proc_with_random_number(proc)
  proc.call(random)
end

proc = Proc.new { |n| puts "#{n}!" }
run_proc_with_random_number(proc) 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用我们之前看到的 Ruby 的&符号参数语法，并使用一个块来代替创建一个过程并将其传递给方法。

```
def run_proc_with_random_number(&proc)
  proc.call(random)
end

run_proc_with_random_number { |n| puts "#{n}!" } 
```

Enter fullscreen mode Exit fullscreen mode

请注意在方法中添加到参数的&符号。这会将传递的块转换为 proc 对象，并将其存储在方法范围内的变量中。

*提示*:虽然在某些情况下在方法中使用 proc 很有用，但是块到 proc 的转换会影响性能。尽可能使用隐式块。

### `#to_proc`

符号、散列和方法可以使用它们的`#to_proc`方法转换成 procs。一种常见的用法是将从符号创建的过程传递给方法。

```
[1,2,3].map(&:to_s)
[1,2,3].map {|i| i.to_s }
[1,2,3].map {|i| i.send(:to_s) } 
```

Enter fullscreen mode Exit fullscreen mode

这个例子展示了在数组的每个元素上调用`#to_s`的三种等价方式。在第一个示例中，传递了一个以&为前缀的符号，它通过调用它的`#to_proc`方法自动将其转换为 proc。最后两个展示了这个过程可能样子。

```
class Symbol
  def to_proc
    Proc.new { |i| i.send(self) }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是一个简化的例子，但是`Symbol#to_proc`的实现显示了幕后发生的事情。该方法返回一个 proc，它接受一个参数并将`self`发送给它。因为`self`是这个上下文中的符号，所以它调用了`Integer#to_s`方法。

## Lambdas

Lambdas 本质上是具有一些区别因素的过程。它们在两个方面更像“常规”方法:它们在被调用时强制传递参数的数量，并且它们使用“常规”返回。

当调用一个 lambda 需要一个没有参数的参数时，或者如果你传递一个参数给一个不需要它的 lambda，Ruby 抛出一个`ArgumentError`。

```
irb>  lambda (a) { a }.call
ArgumentError: wrong number of arguments (given 0, expected 1)
        from (irb):8:in `block in irb_binding'
        from (irb):8
 from /Users/jeff/.asdf/installs/ruby/2.3.0/bin/irb:11:in `<main>' 
```

Enter fullscreen mode Exit fullscreen mode

此外，lambda 处理 return 关键字的方式与方法相同。当调用 proc 时，程序将控制权交给 proc 中的代码块。因此，如果 proc 返回，则当前作用域返回。如果在函数内部调用 proc 并调用`return`，函数也会立即返回。

```
def return_from_proc
  a = Proc.new { return 10 }.call
  puts "This will never be printed."
end 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将把控制权交给 proc，所以当它返回时，函数返回。在这个例子中调用函数永远不会打印输出并返回 10。

```
def return_from_lambda
  a = lambda { return 10 }.call
  puts "The lambda returned #{a}, and this will be printed."
end 
```

Enter fullscreen mode Exit fullscreen mode

当使用λ时，它的*将*被打印出来。在 lambda 中调用`return`将像在方法中调用`return`一样，所以用`10`填充`a`变量，并将该行打印到控制台。

## 块、过程和λ

既然我们已经深入研究了 procs 和 lambdas 这两个模块，让我们缩小范围，总结一下比较结果。

*   Ruby 中广泛使用块来向函数传递代码。通过使用`yield`关键字，可以隐式地传递一个块，而不必将其转换为 proc。
*   当使用前缀为&的参数时，将块传递给方法会在方法的上下文中产生一个过程。Procs 的行为类似于块，但是它们可以存储在变量中。
*   Lambdas 是行为类似于方法的过程，这意味着它们执行 arity 并作为方法返回，而不是在它们的父作用域中。

这就结束了我们对 Ruby 中闭包的研究。关于闭包还有很多东西需要学习，比如词法范围和绑定，但是我们将在下一集继续学习。与此同时，请让我们知道您希望在 Ruby Magic、闭包或其他方面的未来文章中读到什么。