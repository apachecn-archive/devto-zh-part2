# 标准库的包装器

> 原文：<https://dev.to/voins/on-wrappers-for-standard-library-59bm>

每种语言都有一个标准库。每个人都用它。不是每个人都喜欢它。标准库有几个问题:它们必须让每个人都满意，并且它们必须至少与以前的几个版本兼容(如果这种语言值得使用的话)。有时我不禁注意到，我正在一遍又一遍地编写相同的代码，只是因为标准库在很久以前就是这样设计的，即使现在已经有了新的工具，它们也必须支持兼容性。例如:

```
std::string::size_type p = filename.find_last_of(“/.”);
if (p != std::string::npos)
    // use p in some meaningful way 
```

Enter fullscreen mode Exit fullscreen mode

当已经有`std::optional<>`，或者

```
loglist_t::iterator i = loggers.find(name)
if (i != loggers.end())
    // dereference i and use the result 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
getSomeOptional().map(Optional::of).orElse(getSecondOptional()); 
```

Enter fullscreen mode Exit fullscreen mode

当已经有了`Optional::or()`的时候，但是我的雇主不打算切换到 Java 9。我很想把这些代码放入一些小方法中，但是我应该把它们放在哪里呢？

这个问题其实有一个非常简单的解决方法。我有点惊讶地发现，在 Bob 叔叔的“干净代码”的第 8 章中有详细的描述，但出于某种原因，我从未见过这种方法实际实现。我们只需要为我们从标准库中使用的所有东西创建包装器，但是每个人都忙于创建替代实现。让我告诉你一个秘密:现代编译器非常擅长优化，这个包装器可能不会花你一分钱或者几乎不花你一分钱。突然神奇的事情发生了，你有了一堆用于静态化并放入`OptionalUtil`类的实用方法的家，或者你不在乎你的编译器是否将`std::experimental::optional`重命名为`std::optional`，因为在你的应用程序中只有一行会被改变，或者你可以为字符串创建更好的 API 并利用前面提到的`std::optional`，或者你可以实现来自新版本的标准库的方法并愉快地使用它，或者……你能想到的。

我在这里看到的唯一缺点是很容易习惯这些包装器，当您转移到另一个项目时，您将会错过它们。但是你已经知道该怎么做了，不是吗？