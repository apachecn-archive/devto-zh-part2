# 拯救 Ruby 中的异常

> 原文：<https://dev.to/appsignal/rescuing-exceptions-in-ruby--4n5b>

一旦引发的[异常](https://blog.appsignal.com/2018/03/13/exceptions-in-ruby.html)到达[调用堆栈](https://blog.appsignal.com/2018/02/06/reading-and-understanding-ruby-stack-traces.html)的顶部，它可以被拯救以防止应用程序崩溃。在 Ruby 中，我们使用`rescue`关键字。

在 Ruby 中抢救异常时，可以指定应该抢救的特定错误类。

```
begin
  raise 'This exception will be rescued!'
rescue StandardError => e
  puts "Rescued: #{e.inspect}"
end 
```

Enter fullscreen mode Exit fullscreen mode

*注意*:在没有指定异常类的情况下使用`raise`时， [Ruby 会默认为`RuntimeError`](https://blog.appsignal.com/2018/03/13/exceptions-in-ruby.html) 。

除了指定一个要拯救的异常类之外，还可以向关键字`rescue`传递一组异常类。这将允许您以相同的方式响应多个错误。

```
begin
  raise 'This exception will be rescued!'
rescue StandardError, AnotherError => e
  puts "Rescued: #{e.inspect}"
end 
```

Enter fullscreen mode Exit fullscreen mode

多个`rescue`块可以用来以不同的方式处理不同的错误。当使用为不同情况产生不同异常的库时，这很有用。

```
begin
  raise 'This exception will be rescued!'
rescue StandardError => e
  puts "Rescued: #{e.inspect}"
rescue AnotherError => e
  puts "Rescued, but with a different block: #{e.inspect}"
end 
```

Enter fullscreen mode Exit fullscreen mode

## 异常层次

Ruby 的异常层次结构用于区分不同类型的错误，同时让您能够在不指定所有错误的情况下从一组错误中拯救出来。

虽然库可以定义自己的异常子类，但是 Ruby 2.5 上内置的[异常子类](http://ruby-doc.org/core-2.5.0/Exception.html)的列表是这样的:

```
- NoMemoryError
- ScriptError
    - LoadError
    - NotImplementedError
    - SyntaxError
- SecurityError
- SignalException
    - Interrupt
- StandardError (default for `rescue`)
    - ArgumentError
        - UncaughtThrowError
    - EncodingError
    - FiberError
    - IOError
        - EOFError
    - IndexError
        - KeyError
        - StopIteration
    - LocalJumpError
    - NameError
        - NoMethodError
    - RangeError
        - FloatDomainError
    - RegexpError
    - RuntimeError (default for `raise`)
    - SystemCallError
        - Errno::*
    - ThreadError
    - TypeError
    - ZeroDivisionError
- SystemExit
- SystemStackError
- fatal (impossible to rescue) 
```

Enter fullscreen mode Exit fullscreen mode

当省略`rescue`块中的异常类时，假定为`StandardError`。因为`ArgumentError`和`NoMethodError`是`StandardError`的子类，所以当它们出现在块中时，它们被拯救出来。

异常层次结构如何工作的一个很好的例子是`SystemCallError`，它是一个底层的依赖于平台的异常类。它最常见于读取或写入文件时。

Ruby 的`File.read`方法会在读取文件失败时抛出异常。发生这种情况的原因有很多，比如文件不存在或者程序没有读取它的正确权限。

由于这些问题是与平台相关的，Ruby 可以根据机器上运行的操作系统类型引发不同的异常。对于像这样的低级错误，Ruby 为每个平台实现了不同的`Errno::*`异常列表。

所有这些`Errno::*`异常都是`SystemCallError`的子类。尽管它们是特定于平台的，但是仍然可以通过从`SystemCallError`中解救出来而在`rescue`块中使用。

```
begin
  File.read("does/not/exist")
rescue SystemCallError => e
  puts "Rescued: #{e.inspect}"
end 
```

Enter fullscreen mode Exit fullscreen mode

## 吞咽异常

通常情况下，营救异常时最好尽量具体，防止无意中吞食异常。

```
image = nil

begin
  File.read(image.filename)
rescue
  puts "File can't be read!"
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`image`变量是`nil`，所以当我们试图对它调用`#filename`(`NoMethodError: undefined method `filename' for nil:NilClass`)时，它会引发一个`NoMethodError`。因为每一个`StandardError`子类都是从(包括`NoMethodError`)中抢救出来的，所以异常被吞掉，“文件无法读取！”-信息已打印。这隐藏了代码中一个可能的错误。

*注意*:虽然有可能，但是在`rescue`块中使用`Exception`超类是[非常不鼓励的](https://blog.appsignal.com/2016/10/18/ruby-magic-exceptions-primer.html)。

关于在 Ruby 中引发或挽救异常有什么问题吗？请不要犹豫，通过 [@AppSignal](https://twitter.com/appsignal) 告诉我们。当然，我们很想知道您对这篇文章的看法，或者您是否有其他想了解更多的话题。