# Ruby Gem debug_helper 的自定义处理程序

> 原文：<https://dev.to/burdettelamar/custom-handlers-for-ruby-gem-debughelper-43b4>

我的 Ruby gem [debug_helper](https://rubygems.org/gems/debug_helper) 通过显式化一个给定的对象(有时是递归的)来支持 printf 风格的调试。
文档在 GitHub 项目 [debug_helper](https://github.com/BurdetteLamar/debug_helper) 结束。

到目前为止，我已经为内核中许多更常用的类实现了处理，并且为标准库
中的一些类实现了处理，现在我正在研究如何实现一个定制的处理程序——也就是说，为你自己的类`Foo`实现一个`FooHandler`类。

基本上，自定义处理程序需要指定为显式调用哪些方法，以及任何必需的参数。

现在，我将这些数据表示为数组的数组:

```
[
  # Method that takes no argument.
  [:foo],
  # Method that takes arguments.
  [:bar, arg0, arg1]
] 
```

Enter fullscreen mode Exit fullscreen mode

我在考虑提供更多便利，但这也会增加复杂性:

```
[
  # Method that takes no argument is just a Symbol.
  :foo,
  # Method that takes arguments can be a an Array, as before.
  [:bar, arg0, arg1],
  # Method that takes arguments can be a 1-key Hash (method and arguments).
  :baz => [arg0, arg1],
] 
```

Enter fullscreen mode Exit fullscreen mode

观点？