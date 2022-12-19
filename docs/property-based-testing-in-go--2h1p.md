# Go 中基于属性的测试

> 原文：<https://dev.to/quii/property-based-testing-in-go--2h1p>

[这篇文章最初发表在我的博客上](http://www.quii.co.uk/Property-based_testing_in_Go)

这篇文章希望说明

*   什么是基于属性的测试，为什么它是补充现有测试的重要工具
*   围棋是多么简单

## 您当前的测试

说明 TDD 的常见示例可以描述为基于*示例*的测试；通过提供示例输入和预期输出来编写测试。

```
func  TestAddingExample(t  *testing.T)  {  result  :=  add(3,  2)  if  result  !=  5  {  t.Error("3 plus 2 is 5 but i got",  result)  }  } 
```

当你意识到它们仅仅是示例时，你会发现你的代码可能没有你想象的那么好测试。

如果为您的测试提供**成千上万**种不同的测试输入很容易，这不是很好吗？

你可能已经听说过基于属性的测试，并认为它是用随机生成的数据来测试你的代码，以测试不同的场景；但这实际上需要心态的转变才能获得最大收益。

## 规范，不仅仅是大量的数据

基于属性的测试之所以被称为基于属性的测试，是因为为了编写有效的测试，你必须考虑你领域的*属性*。

## 用加法举例

我们将测试一个加法函数，它只是将两个整数相加。

```
func  add(x,  y  int)  int  {  return  x  +  y  } 
```

当你输入随机数据时，棘手的事情是你如何知道你的预期结果应该是什么？

```
func  TestAddition(t  *testing.T){  x  :=  getRandomInt()  y  :=  getRandomInt()  expected  :=  x  +  y  if  expected  !=  add(x,  y){  // oh no..  }  } 
```

您可能很想这样做，但实际上您是在欺骗，因为您在使用实现来验证它本身。

假设你正在测试一个更复杂的函数。你知道你不应该做以下事情:

```
func  TestComplicatedThing(t  *testing.T){  x  :=  getRandomInt()  y  :=  getRandomInt()  expected  :=  complicated(x,  y)  if  expected  !=  complicated(x,  y){  // oh no..  }  } 
```

这不是真正的测试，如果你改变了`complicated`的实现，它将继续通过测试，即使它的行为不正确。

(这是糟糕测试的常见问题，在测试中，作者重新实现了他们正在测试的单元！)

但是你会怎么做呢？如何在随机输入下验证你的函数？

## 加法的性质

到底是加法的什么*？嗯，有几件事:*

*   `when you add zero to a number, you get the same number back again`
*   `the order of the inputs does not matter`

让我们根据这些加法法则编写一些基于属性的测试。

```
func  TestAddingZeroMakesNoDifference(t  *testing.T)  {  /*
    Create an assertion, which is a function that takes N inputs of
    random data and returns true if the assertion passes. 

    In this case, we're saying take any random integer (x)
    If you add 0, it should equal x
    */  assertion  :=  func(x  int)  bool  {  return  add(x,  0)  ==  x  }  // Run the assertion through the quick checker  if  err  :=  quick.Check(assertion,  nil);  err  !=  nil  {  t.Error(err)  }  }  func  TestAssociativity(t  *testing.T)  {  assertion  :=  func(x,  y,  z  int)  bool  {  return  add(add(x,  y),  z)  ==  add(add(z,  y),  x)  }  if  err  :=  quick.Check(assertion,  nil);  err  !=  nil  {  t.Error(err)  }  } 
```

`quick.Check`函数将运行您的断言 100 次(默认情况下)，为所需的输入提供随机生成的数据。如果它在任何一点失败了，那么你会被告知你的函数在哪些输入上失败了，这样你就可以重现它并修复它。

注意如何:

*   通过断言*属性*而不是已知值，我们不必像我之前那样在测试中“作弊”。
*   我们现在已经根据属性描述了**什么是加法，这比之前的基于示例的测试更具*表现力*。**
*   我们现在有了更强大的测试套件，能够在几毫秒内运行数千种不同的输入。

## 抽象地思考你的领域

一旦你后退一步，真正考虑一下你的代码领域，你可能会发现一些你在一般 TDD 流程中使用的例子不会发现的意想不到的特性。

我想这是很多 QAs 的想法。他们倾向于有点脱离我们写的代码，并且真正地*思考*关于领域和事情如何会分崩离析。基于属性的测试允许我们捕获这种规则作为测试。

## 超越简单类型

基于属性的测试依赖于类型生成器来为测试创建随机数据。很容易想象这对于整数是如何工作的，但是对于你自己的类型呢？

```
type  LatLong  struct{  Lat  float64  Long  float64  }  func  (l  LatLong)  Generate(rand  *rand.Rand,  size  int)  reflect.Value{  randomLatLong  :=  LatLong{  Lat:  rand.Float64(),  Long:  rand.Float64(),  }  return  reflect.ValueOf(randomLatLong)  }  func  TestLatLongIsAlwaysTrue(t  *testing.T){  assertion  :=  func(x  LatLong)  bool  {  t.Log("Random LatLong:",  x)  //todo: Do some interesting assertions!  return  true  }  if  err  :=  quick.Check(assertion,  nil);  err  !=  nil  {  t.Error(err)  }  } 
```

你所要做的就是为你的类型实现[生成器接口](https://golang.org/pkg/testing/quick/#Generator)。Go 会给你提供一个`rand`帮你生成随机数据。

## 结论

为了编写有效的基于属性的测试，你必须后退一步，深入思考你领域的规则。这本身就是一项有价值的工作。

一旦你获得了这种深刻的理解，你就可以很容易地用基于属性的测试来表达它们，这将帮助你在进入生产之前找到 bug。

Go 中的工具简化了这些规范的编写，即使是针对您自己的类型，因此没有理由不在您当前的项目中尝试它。

### 备注

*   [你可以在 github 上查看代码](https://github.com/quii/quickcheck-example)
*   我通过 [RTFM](https://golang.org/pkg/testing/quick) 学会了如何在 Go 中进行快速检查。我建议您也这样做，因为它提供了许多我在这里没有提到的选项和配置。
*   [真正让我对基于属性的测试感兴趣的演讲](https://skillsmatter.com/skillscasts/6432-the-lazy-programmers-guide-to-writing-1000s-of-tests-an-introduction-to-property-based-testing)
*   如果你对此感到怀疑，我可以理解，但是下一篇文章将展示这种在真实应用程序上的测试风格。