# 为什么要在 C++ 11 中使用' override '说明符？

> 原文：<https://dev.to/sandordargo/why-to-use-the-override-specifier-in-c-11-2h16>

**你想每天出一道 C++题，为下一次面试做准备吗？免费注册[每日新闻采访](https://www.dailycppinterview.com/)！**

如果你懂 Java，这可能对你来说非常简单，因为你已经习惯了 Java 的`@Override annotation`。如果你一直用 C/C++编程，这可能是新的。你可能会问自己这样一个问题，为什么要在不必要的时候放一个额外的说明符。您的代码将以完全相同的方式工作。

虽然在大多数情况下你的代码行为不会改变，但在其他一些情况下——当你真的犯了一个错误时——使用`override`会阻止你签入有问题的代码，因为你的编译会失败。我们没有人会签入不编译的代码，对吗？

`override`说明符将告诉编译器和读者，使用它的函数实际上是从基类中重写一个方法。

它告诉读者“这是一个虚方法，它覆盖了基类的虚方法。”

正确使用它，你看不到任何效果:

```
class Base
{
    virtual void foo();
};

class Derived : Base
{
    void foo() override; // OK: Derived::foo overrides Base::foo
}; 
```

Enter fullscreen mode Exit fullscreen mode

但是它会帮助你*揭示常数* :
的问题

```
class Base
{
    virtual void foo();
    void bar();
};

class Derived : Base
{
    void foo() const override; // Error: Derived::foo does not override Base::foo
                               // It tries to override Base::foo const that doesn't exist
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们不要忘记，在 C++中，方法默认是非虚的。如果我们使用`override`，我们可能会发现没有什么可以覆盖。如果没有`override`说明符，我们将简单地创建一个全新的方法。*不再忘记声明为虚拟的基类方法。*

```
class Base
{
    void foo();
};

class Derived : Base
{
    void foo() override; // Error: Base::foo is not virtual
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们还应该记住，当我们覆盖一个方法的时候——不管有没有`override`说明符——*，都不可能进行转换* :

```
class Base
{
  public:
    virtual long foo(long x) = 0; 
};

class Derived: public Base
{
   public:
     long foo(int x) override { // error: 'long int Derived::foo(int)' marked override, but does not override
      // ...
     }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，使用 C++11 中的覆盖说明符是干净编码原则的一部分。它揭示了作者的意图，使代码更具可读性，并有助于在构建时识别 bug。用起来没有节制！

如果你正在寻找更多现代 C++技巧，我推荐你去看看 [Scott Meyers](https://www.aristeia.com/) 的 [Effective Modern C++](https://amzn.to/2VZrLec) ！

*这篇文章最初发表在我的[博客](http://sandordargo.com/blog/2018/07/05/cpp-override)上。如果你想收到我的最新文章，请[注册我的简讯](http://eepurl.com/gvcv1j)。*