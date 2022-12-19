# Go 中的测试:测试浮点数

> 原文：<https://dev.to/juliaferraioli/testing-in-go-testing-floating-point-numbers-4i0a>

*这是从[我的博客](https://juliaferraioli.com/blog/2018/06/golang-testing-floats/)交叉发布的。*

我一直在开发一个包含 Go 中一些向量操作的库，试图遵循良好的开发实践，并首先从编写测试开始。但是在早期，我遇到了一个小问题:浮点。

# 问题

现在，我们知道浮点是编程中的一个问题，这是由它们在内存中的表现方式决定的。我喜欢 Julia Evans 在她的 [Linux 漫画杂志](https://jvns.ca/linux-comics-zine.pdf)中对浮动如何工作的简要解释(向下滚动到第六个面板)，要获得更深入的解释，请参见 Carl Burch 的[这篇文章](http://www.toves.org/books/float/#s2.1)。

我的问题:在编写了向量加法函数的实现之后，我无法通过测试，因为比较浮点数的相等性是...很难。我将比较预期结果`{2,3}`和实际结果`{1.99999999, 3.00000000002}`。这些价值观不符合传统的平等。

我查阅了如何对返回浮点数的函数进行单元测试的最佳实践，绝大多数人都建议“不要”。(没什么帮助，各位。)有人建议使用一个容差——也就是说，如果预期数字和实际数字之间的绝对差值在规定的容差之内，那么就认为它们相等。

这很容易实现，看起来像这样:

```
got  :=  Add(tt.vectors)  if  math.Abs(got-tt.want)  >  tolerance  {  t.Fail("Got %v, expected %v",  got,  tt.want)  } 
```

# 定义比较器

然而，我最近开始重新实现我的库，并在这个过程中切换到使用 cmp 包进行测试。cmp 包是一种更加健壮的测试相等性的方法，在探索它的过程中，我发现可以定义一个比较器函数来使它更加简洁。这是文档中的比较器函数，它使用了比我上面的初始函数更精确的逻辑:

```
const  tolerance  =  .00001  opt  :=  cmp.Comparer(func(x,  y  float64)  bool  {  diff  :=  math.Abs(x  -  y)  mean  :=  math.Abs(x  +  y)  /  2.0  return  (diff  /  mean)  <  tolerance  }) 
```

然后，当运行测试时，在测试相等性时使用比较器函数:

```
if  !cmp.Equal(got,  tt.want,  opt)  {  t.Fatalf("got %v, wanted %v",  got,  tt.want)  } 
```

您的测试要干净得多，并且您可以轻松地使用适合您的用例的比较器函数。

# Postscript

然而...你必须小心你的比较函数！上面的一个是直接来自 cmp 文档。但是很快我遇到了一个我没有预料到的测试失败:当试图对零向量执行操作时，它失败了。这导致了我和我的测试之间的交流:

[![More<br>
Me: Tests, why are you failing?<br>
Tests: Not telling.<br>
Me: It's a simply equality check! Nothing complex -- I swear!<br>
Tests: Mmmhmmm.<br>
Me: This was my baseline test. The simplest test case I could think of.<br>
Tests: Suuuure.<br>
Me: Let me look. Wait...are you dividing by ZERO!?!<br>
Tests: 😈](img/6c977da9683b29cba9d9a50182aa6692.png "Screenshot of a frustrated tweet")](https://res.cloudinary.com/practicaldev/image/fetch/s--vwiMSTsy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juliaferraioli.com/blimg/golang-testing-floats/twitter-screenshot.png) 
沮丧的推特，[推特](https://twitter.com/juliaferraioli/status/1004770471227940864)

是的，当一个向量中的所有值都是 0 时，我最后除以 0。所以我的实际比较器函数看起来像这样:

```
opt  :=  cmp.Comparer(func(x,  y  float64)  bool  {  diff  :=  math.Abs(x  -  y)  mean  :=  math.Abs(x  +  y)  /  2.0  if  math.IsNaN(diff  /  mean)  {  return  true  }  return  (diff  /  mean)  <  tolerance  }) 
```

当然，如果不使用`cmp`比较器选项，同样的逻辑(`diff / mean`)也会有同样的问题，所以这个有趣的故事是关于一般情况的。

但是，也就是说，用了 8 行代码来让我的测试真正测试我所关心的东西。所以如果你在做科学计算，不要忘记你的比较函数！

死

*这篇博文中的所有代码都是在 [Apache 2.0 许可](https://www.apache.org/licenses/LICENSE-2.0)下发布的，[可以在 GitHub](https://github.com/juliaferraioli/code_snippets/tree/master/blogs/cmp-testing/floats) 上找到。*