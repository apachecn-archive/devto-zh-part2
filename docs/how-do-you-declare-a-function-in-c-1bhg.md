# 在 C++中如何声明一个函数？

> 原文：<https://dev.to/sandordargo/how-do-you-declare-a-function-in-c-1bhg>

今年年初，我回到了 C++开发人员的位置，我们正在为完成向 C++11 的移植做最后的准备，我决定提升我的知识水平。这几乎就像发现了一种新的语言，顺便说一下，这种语言比 C++98 更令人愉快。

让我大开眼界的一件事是函数声明是如何发展的。

如果你已经在 C++生态系统中呆了很长时间，你可能会这样回答:

```
int getElement(const std::vector<int>& container, int index) const; 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你只是最近才开始，或者如果你有使用 C++ (>=C++11)新版本的经验，你可能会有另一个答案，比如:

```
auto getElement(const std::vector<int>& container, int index) const -> int; 
```

Enter fullscreen mode Exit fullscreen mode

我相信你注意到了不同之处:

*   我们没有以`int`作为返回类型，而是使用了`auto`关键字
*   我们在箭头(`->`)后添加了`int`作为返回类型。

返回类型在函数名、参数列表和函数限定符之后！

为什么这对我们来说很有趣？你可能会说这没有任何意义，只会降低代码的可读性。我认为这是风格问题，但我倾向于同意。这无疑增加了代码的长度，却没有增加任何好处。

那么，为什么要添加这种尾随返回类型呢？我们如何使用它？

### 省略范围

尽管我们看到通过使用尾随返回类型，我们的代码变得更长，但这并不一定在所有情况下都是正确的。

让我们来看看我们代表葡萄酒的类。

```
class Wine {
 public:
 enum WineType { WHITE, RED, ROSE, ORANGE };
 void setWineType(WineType wine_type);
 WineType getWineType() const;

 //... 

 private:
  WineType _wine_type;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道什么是橙酒，它不是由橙制成的。你可以在这里找到更多细节[。](https://vinepair.com/articles/orange-wine-guide/)

现在让我们检查一下实现。

二传手看起来很明显，是吗？

```
void Wine::setWineType(WineType wine_type) {
  _wine_type = wine_type;
} 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，我们的第一个 getter 方法可能不起作用:

```
WineType Wine::getWineType() {
  return _wine_type;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码无法编译，因为编译器不知道 WineType。它在全局范围内寻找它。你必须明确声明它是 Wine 类的一部分。

```
Wine::WineType Wine::getWineType() {
  return _wine_type;
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来像是重复，但却是必要的。这是必要的，但也是可以避免的，因为尾部返回类型声明是可用的。看看这个:

```
auto Wine::getWineType() -> WineType {
  return _wine_type;
} 
```

Enter fullscreen mode Exit fullscreen mode

在行首，编译器不知道范围，因此我们必须写`Wine::WineType`，但是当我们在末尾声明返回类型时，编译器已经知道我们在`Wine`的范围内，所以我们不需要重复这个信息。

根据作用域的名称，您可能会省去一些字符，但至少您不必重复类名。

这很好，但是你认为 ISO CPP 委员会会仅仅为了不重复范围而引入改变吗？我不这么认为，但是谁知道呢。可以肯定的是，尾随类型声明还有其他用途。

### 在带有`decltype`的模板中使用尾随类型声明

使用尾部返回类型声明的一个更有说服力的原因可能是函数模板的返回类型依赖于参数类型。

让我们来看看这个古老的好例子:

```
template<class L, class R>
auto multiply(L const& lhs, R const& rhs) -> decltype(lhs * rhs) {
  return lhs * rhs;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`std::declval`创建这样的函数模板是可能的，但是它变得如此冗长和不可读，我甚至不把它放在这里。如果你想睡不好，就去查一下。

另一方面，在 C++14 中，返回类型演绎的范围得到了扩展，这就更简单了:

```
template<class L, class R>
auto multiply(L const& lhs, R const& rhs) {
  return lhs * rhs;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

您看到了使用尾部返回类型声明可以帮助您不重复普通函数的作用域，并且对于 C++11 中的模板函数，它使得声明依赖于模板参数的返回类型比以前更容易。

你应该在每种情况下都使用它吗？一定要一直用吗？不。但是我没有说你不应该一直使用它。这是风格问题。随心所欲，持之以恒。要么一直使用它，要么只在它确实带来积极价值的时候使用它。但不要对半做。

最重要的是你掌握了新的语法，你知道它的存在并知道如何使用它。对我来说，这是全新的，直到最近我开始阅读 [Scott Meyers](https://www.aristeia.com/) 的[有效的现代 C++](https://amzn.to/2Rbh5pI) 。我还推荐 [Fluent{C++}](https://www.fluentcpp.com/) 作为了解这种非常丰富的语言的来源。

*本文最初发表在[我的博客](http://sandordargo.com/blog/2018/11/07/trailing-return-type)上。*