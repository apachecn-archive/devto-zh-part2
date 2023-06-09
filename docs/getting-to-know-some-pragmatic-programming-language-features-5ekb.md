# 了解一些实用的编程语言特性

> 原文：<https://dev.to/onmyway133/getting-to-know-some-pragmatic-programming-language-features-5ekb>

[![](img/5f4356e30707523129a0502f20b5ca61.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q-45nmkt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AOTe0Y7EkIclz1sIT.jpg)

如你所知，在[实用程序员](http://www.amazon.com/The-Pragmatic-Programmer-Journeyman-Master/dp/020161622X)，你的知识组合部分，据说

> 每年至少学习一门新语言。不同的语言用不同的方式解决同样的问题。通过学习几种不同的方法，你可以拓宽你的思路，避免墨守成规。此外，由于互联网上大量免费软件的存在，学习多种语言变得更加容易

我把学习编程语言看作是开拓视野和学习一些新概念的机会。它也鼓励良好的习惯，如不变性，组成，调整，…

我想回顾一下我玩过的所有语言的一些特性。有些很有用，有些只是让我感兴趣或者说“哇”

### 花括号

每种语言都可以有自己的代码块分组风格，但是我自己最喜欢花括号，它们很可爱:]

有些像 C，Java，Swift，…用花括号

雨燕

```
init(total: Double, taxPct: Double) {
  self.total = total
  self.taxPct = taxPct
  subtotal = total / (taxPct + 1)
} 
```

有些像 Haskell，Python，…使用缩进

哈斯克尔

```
bmiTell :: (RealFloat a) => a -> String  
bmiTell bmi  
    | bmi <= 18.5 = "You're underweight, you emo, you!"  
    | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"  
    | otherwise   = "You're a whale, congratulations!" 
```

有些像仙丹一样使用关键词列表

**仙丹**

```
if false, do: :this, else: :that 
```

### 命名参数

像 Objective C、Swift 这样语言提供了命名参数，这使得推理函数调用变得更加容易

```
func sayHello(to person: String, and anotherPerson: String) -> String {
    return "Hello \(person) and \(anotherPerson)!"
} 
```

### 外显式

像 C、Swift、Java 等语言在参数和返回中都有类型信息，这使得推理函数调用变得更加容易

雨燕

```
func sayHello(personName: String, alreadyGreeted: Bool) -> String {
    if alreadyGreeted {
        return sayHelloAgain(personName)
    } else {
        return sayHello(personName)
    }
} 
```

### 列表理解

像 Haskell、Python、Elixir 这样的语言支持列表理解

**仙丹**

```
iex> for n <- [1, 2, 3, 4], do: n * n
[1, 4, 9, 16] 
```

### 第一类功能

我喜欢函数式编程，所以 Javascript、Swift、Haskell、Elixir 等一流的函数支持真的让我很开心

哈斯克尔

```
zipWith' (*) (replicate 5 2) [1..] 
```

### 咖喱

> *Currying 是一种将带有多个参数(或一组参数)的函数的求值转化为一系列函数的求值的技术，每个函数只有一个参数(部分应用)*

像 Swift 2，Haskell，…这样的语言，默认都有库里。有些像 Javascript 可以使用库(Lodash，…)来实现这一点。在 Haskell 中，每个函数都只接受一个参数。

在 Swift 3 中，库里被[移除](https://github.com/apple/swift-evolution/blob/master/proposals/0002-remove-currying.md):(

哈斯克尔

```
multThree :: (Num a) => a -> a -> a -> a  
multThree x y z = x * y * z 
```

通过调用参数太少的函数，我们正在动态地创建新的函数。

**Javascript**

```
var curry = require('lodash.curry');
var map = curry(function(f, ary) {
  return ary.map(f);
});
var getChildren = function(x) {
  return x.childNodes;
};

var allTheChildren = map(getChildren); 
```

### 模式匹配

我发现模式匹配是绕过 if else 语句的一种更好的方法

Swift 支持 switch 语句中的模式匹配

雨燕

```
enum Trades {
    case Buy(stock: String, amount: Int, stockPrice: Float)
    case Sell(stock: String, amount: Int, stockPrice: Float)
}

let aTrade = Trades.Buy(stock: "APPL", amount: 200, stockPrice: 115.5)

switch aTrade {
case .Buy(let stock, let amount, _):
    process(stock, amount)
case .Sell(let stock, let amount, _):
    process(stock, amount * -1)
} 
```

有些像 Haskell，Elixir，…也支持函数名的模式匹配，这使得它非常适合递归

哈斯克尔

```
sayMe :: (Integral a) => a -> String  
sayMe 1 = "One!"  
sayMe 2 = "Two!"  
sayMe 3 = "Three!"  
sayMe 4 = "Four!"  
sayMe 5 = "Five!"  
sayMe x = "Not between 1 and 5"  

map _ []     = []
map f (x:xs) = f x : map f xs 
```

在 Elixir 中，=操作符实际上是一个匹配操作符

**仙丹**

```
iex> x = 1
1
iex> x
1
iex> 1 = x
1
iex> 2 = x
** (MatchError) no match of right hand side value: 1 
```

### 递归

有些语言像 Haskell，Elixir，…不使用循环，他们使用递归时考虑到了性能，没有溢出。

哈斯克尔

```
length' :: (Num b) => [a] -> b  
length' [] = 0  
length' (_:xs) = 1 + length' xs 
```

### 懒惰

有些语言支持无限集合，这要感谢它们的懒惰。

Haskell 是懒惰的，如果你在一个列表上映射一些东西几次，过滤几次，它只会在列表上传递一次

哈斯克尔

```
largestDivisible :: (Integral a) => a  
largestDivisible = head (filter p [100000,99999..])  
    where p x = x `mod` 3829 == 0 
```

Elixir 定义了带有 Enum 的 Eager 和带有 Stream 的 Lazy 的概念

**仙丹**

```
1..100_000 |> Enum.map(&(&1 * 3)) |> Enum.filter(odd?) |> Enum.sum 
```

## 自定义操作员

仙丹因其烟斗|符而闻名

> *上面代码片段中使用的| >符号是管道操作符:它只是从左边的表达式中获取输出，并将其作为第一个参数传递给右边的函数调用*

长生不老药

```
1..100_000 |> Enum.map(&(&1 * 3)) |> Enum.filter(odd?) |> Enum.sum 
```

Haskell 经常利用这个自定义操作符

```
x -: f = f x  

(0,0) -: landLeft 1 -: landRight 1 -: landLeft 2 
```

### 函子，适用函子，幺半群，单子

因为这些类型类，我真的很喜欢 Haskell。它用编译上下文实现通用模式(映射、应用、连接、绑定等)。当我发现函数也是一个单子时，这真的启发了我(你应该读一下《单子的读者》)

哈斯克尔

```
instance Monad Maybe where  
    return x = Just x  
    Nothing >>= f = Nothing  
    Just x >>= f  = f x  
    fail _ = Nothing  

landLeft :: Birds -> Pole -> Maybe Pole  
landLeft n (left,right)  
    | abs ((left + n) - right) < 4 = Just (left + n, right)  
    | otherwise                    = Nothing  

landRight :: Birds -> Pole -> Maybe Pole  
landRight n (left,right)  
    | abs (left - (right + n)) < 4 = Just (left, right + n)  
    | otherwise                    = Nothing

ghci> return (0,0) >>= landLeft 1 >>= banana >>= landRight 1  
Nothing 
```

Haskell 中的列表理解只是将 l is 用作单子的语法糖

```
ghci> [ (n,ch) | n <- [1,2], ch <- ['a','b'] ]  
[(1,'a'),(1,'b'),(2,'a'),(2,'b')] 
```

雨燕

```
enum Result<T> {
    case Value(T)
    case Error(NSError)
}

extension Result {
    func map<U>(f: T -> U) -> Result<U> {
        switch self {
            case let .Value(value):
                return Result<U>.Value(f(value))
            case let .Error(error):
                return Result<U>.Error(error)
        }
    }
}

extension Result {
    static func flatten<T>(result: Result<Result<T>>) -> Result<T> {
        switch result {
            case let .Value(innerResult):
                return innerResult
            case let .Error(error):
                return Result<T>.Error(error)
        }
    }
}

extension Result {
    func flatMap<U>(f: T -> Result<U>) -> Result<U> {
        return Result.flatten(map(f))
    }
} 
```

### 特质与混成

像 Scala 这样的语言支持 trait

> 类似于 Java 中的接口，特征用于通过指定所支持方法的签名来定义对象类型。与 Java 不同，Scala 允许部分实现 traits 也就是说，可以为某些方法定义默认实现

**Scala**

```
trait Similarity {
  def isSimilar(x: Any): Boolean
  def isNotSimilar(x: Any): Boolean = !isSimilar(x)
}

class Point(xc: Int, yc: Int) extends Similarity {
  var x: Int = xc
  var y: Int = yc
  def isSimilar(obj: Any) =
    obj.isInstanceOf[Point] &&
    obj.asInstanceOf[Point].x == x
} 
```

Swift 可以使用协议扩展[来实现特性](http://matthijshollemans.com/2015/07/22/mixins-and-traits-in-swift-2/)

雨燕

```
protocol GunTrait {
    func shoot() -> String {
        return "Shoot"
    }
}

protocol RenderTrait {
    func render() -> String {
        return "Render"
    }
}

struct Player: GameObject, AITrait, GunTrait, RenderTrait, HealthTrait {

} 
```

Ruby 通过模块支持 Mixin

**红宝石**

```
module Greetings

  def hello

    puts "Hello!"

  end

def 你好
妓女们“你好！”
结束

def 你好
妓女“你好！”
结束
结束

类用户
包括问候
结束 
```

### 
  
委托财产

有一些常见的属性很容易一劳永逸地实现，比如 lazy、observable 和 storing。一个例子是在**科特林**

```
class Delegate {

    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {

        return "$thisRef, thank you for delegating '${property.name}' to me!"

    }

```
operator fun setValue(thisRef: Any?, property: KProperty&lt;*&gt;, value: String) {
    println("$value has been assigned to '${property.name}' in $thisRef.")
} 
```

} 
```

## 
  
何去何从

希望你能找到有趣的东西。每种语言都有自己的优点，都是为特定的目的而设计的。因此，没有一份清单足以涵盖所有这些问题。

为了快速浏览其他编程语言，我发现 Derek 的视频中的[非常有用。](https://www.youtube.com/playlist?list=PLGLfVvz_LVvSX7fVd4OUFp_ODd86H0ZIY)

<center>

每天都有一些让我们感兴趣的事情，比如 Swift 初始化规则在使用初始化器时使其显式化，Go goroutine 和并发代码通道，用于轻松并发和消息通信的 Elixir process。你会惊讶于 process 封装状态的方式，Haskell 数据类型鼓励不变性和线程安全代码，Elixir macro 对语言的巨大扩展。学习的最好方法是经常使用和钻研语言。

希望您的代码能够继续编译。

当你在这里的时候，你可能会喜欢我的其他帖子

*   [挨着第一，挨着什么都没有](https://hackernoon.com/next-to-first-next-to-nothing-b7b7b2ba8a34)

*   [2018 年 macOS 推荐的 20 款实用应用](https://hackernoon.com/20-recommended-utility-apps-for-macos-in-2018-ea494b4db72b)

*   [如何在 React Native 中制作标签选择视图](https://hackernoon.com/how-to-make-tag-selection-view-in-react-native-b6f8b0adc891)

*   [Android 应用程序中片段和活动之间的 8 种沟通方式](https://hackernoon.com/8-ways-to-communicate-between-fragment-and-activity-in-android-apps-235b60005d04)

原帖[https://hacker noon . com/do-you-know-these-interest-programming-languages-features-1 fab 3 fcb 2118](https://hackernoon.com/do-you-know-these-interesting-programming-languages-features-1fab3fcb2118)

</center>