# Ruby 的重做、重试和下一个关键字

> 原文：<https://dev.to/appsignal/rubys-redo-retry-and-next-keywords-223l>

在讨论异常后的[重试时，我们已经讨论了`retry`关键字。它的鲜为人知的对应物`redo`工作类似，但是重新运行循环迭代而不是整个块。](https://blog.appsignal.com/2018/05/16/ensure-retry-and-reraise-exceptions-in-ruby.html)

## `redo`关键词

正如我们在之前的 Academy 文章中了解到的，`retry`允许您在块中重试一段代码:

```
begin
  puts "Iteration"
  raise
rescue
  retry
end 
```

Enter fullscreen mode Exit fullscreen mode

此示例在引发异常之前将单词“Iteration”打印到控制台。rescue blocks 执行，它调用`retry`并从头开始再次启动该块。这导致我们的程序不停地打印`Iteration`。`redo`关键词让你在使用循环时达到类似的效果。这在迭代时需要重试的情况下很有用。

```
10.times do |i|
  puts "Iteration #{i}"
  redo if i > 2
end 
```

Enter fullscreen mode Exit fullscreen mode

这将打印:

```
$ ruby redo.rb
Iteration 0
Iteration 1
Iteration 2
Iteration 3
Iteration 3
Iteration 3
... 
```

Enter fullscreen mode Exit fullscreen mode

注意迭代次数是如何保持不变的？它将执行回退到循环的开始。使用`retry`的代码变体将打印完全相同的输出:

```
10.times do |i|
  begin
    puts "Iteration #{i}"
    raise if i > 2
  rescue
    retry
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

可以使用`redo`实现循环重试。在下一个例子中，我们有一个作业队列。他们要么返回`:success`要么返回`:failure`。我们不断重复运行相同的循环，直到作业成功。

```
[job_1, job_2, job_3, job_4].each do |job|
  redo unless job.call == :success
end 
```

Enter fullscreen mode Exit fullscreen mode

## 红宝石 1.8

`retry`和`redo`的行为在 Ruby 1.8 和 1.9 之间发生了变化。他们曾经重新开始循环的迭代，但两者的方式不同。从 1.9 开始，`retry`仅适用于`begin` / `rescue`块，`redo`仅适用于循环。

## `next`关键词

如果您想移动到循环的下一个迭代，而不是返回到当前循环的开始，您可以使用`next`。

```
10.times do |i|
  puts "Iteration #{i}"
  next if i > 2
  puts "Iteration done"
end 
```

Enter fullscreen mode Exit fullscreen mode

这将打印:

```
$ ruby next.rb
Iteration 0
Iteration done
Iteration 1
Iteration done
Iteration 2
Iteration done
Iteration 3
Iteration 4
... 
```

Enter fullscreen mode Exit fullscreen mode

看到迭代计数器是如何不断递增的了吗？在大多数情况下，使用`next`是你想要的。如果您需要一个运行精确次数的循环，或者在迭代一个数组时需要错误处理，请查看`redo`。

我们希望你学到了一些关于在循环中重做迭代的新东西，并且很想知道你对这篇文章的看法(或者在 [AppSignal Academy 系列](https://blog.appsignal.com/category/academy.html)中的任何其他文章)。请不要犹豫，在评论中告诉我们你的想法，或者如果你有任何想要了解更多的 Ruby 主题。