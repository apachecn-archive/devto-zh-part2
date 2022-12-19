# 函数式语言是时尚

> 原文：<https://dev.to/pancy/functional-languages-are-fad-15g4>

我不理解函数式语言的流行。尽管那些自由派书呆子鄙视 IT 这个词，但它既是他们的事业，也是我的事业。在里面，你只使用有用的东西。编程是为了好玩？带着激情？我必须把食物放在桌子上！我从来不理解那些下班后还在用奇怪的理论语言写代码的人。

让我从代码的角度来说，因为这是我们理解学习函数式语言无用性的方式。

### 函数式语言简洁

这就是问题所在。在 IT 界，尤其是承包商，我们通过交付的代码行数获得报酬。看看这段用 Java 写的漂亮代码，我最喜欢的语言:

```
 public static class SumOfSquaresHelper
{
   public static int Square(int i)
   {
      return i * i;
   }

   public static int SumOfSquares(int n)
   {
      int sum = 0;
      for (int i = 1; i <= n; i++)
      {
         sum += Square(i);
      }
      return sum;
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

一共 16 行代码。再多上几节课，那就是发薪日了。这是 F#中的对等物，它是. NET 自己的功能衍生，是有史以来最可怕的语言之一 Ocaml(坦白地说，微软发明这种语言来取代 C#是非常令人失望的):

```
 let square x = x * x
let sumOfSquares n = [1..n] |> List.map square |> List.sum 
```

Enter fullscreen mode Exit fullscreen mode

天啊。两行代码做同样的事情？你怎么能指望像我们这样的人得到报酬，更不用说任何阅读代码的人了？你甚至可以更进一步，把它变成一个带有 lambda 的一行。

```
 let sumOfSquares n = [1..n] |> List.map (fun x -> x * x) |> List.sum 
```

Enter fullscreen mode Exit fullscreen mode

多么令人厌恶！更糟糕的是，如今的语言设计者往往会被这种功能崇拜洗脑，情不自禁地在语言中加入一些功能特性。就连自互联网诞生以来就存在的 Javascript 也想变得实用。

```
 let sumOfSquares = n => [...Array(n).keys()].map(x => x * x).reduce((x, y) => x + y); 
```

Enter fullscreen mode Exit fullscreen mode

的确，对于一个合法的、严肃的程序员来说，这是黑暗的日子。

> 灵感来自[不使用静态类型函数式编程语言的十个理由](https://fsharpforfunandprofit.com/posts/ten-reasons-not-to-use-a-functional-programming-language/)。