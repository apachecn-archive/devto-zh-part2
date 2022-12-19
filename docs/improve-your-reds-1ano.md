# 提高你的红色

> 原文：<https://dev.to/trikitrok/improve-your-reds-1ano>

提高你的 red 是一个简单的技巧，由史蒂夫·弗里曼和 T2·纳特·普莱斯在他们精彩的书《测试指导下的面向对象软件开发》中描述。

它是 TDD 周期的一个小变化，在这个变化中，您观察失败测试的错误消息，并问自己它给你的信息是否会使诊断问题的根源变得更容易，以防这个错误在将来以回归的形式出现。如果答案是否定的，那么在通过测试之前，您需要改进错误消息。

[![alt text](img/954294e6feb8a971b39486bde9e6a955.png "Variation to TDD cycle: feedback on diagnostics (from Growing Object-Oriented Software by Nat Pryce and Steve Freeman)")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yOK5bhap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pauiz0b08pbfn0p9tcr7.png)

投资时间来提高你的 red 将证明对你的同事和你自己非常有用，因为错误信息越清晰，就越有利于有效地修正回归错误。大多数时候，将这个小的变化应用到 TDD 周期只需要很小的努力。

作为一个简单的例子，看看下面的断言及其失败的原因

```
assertThat(new Fraction(1,2).add(1), is(new Fraction(3,2))); 
```

Enter fullscreen mode Exit fullscreen mode

```
java.lang.AssertionError:
Expected: is <Fraction@2d8e8c3a>
     but: was <Fraction@2d8e84b8> 
```

Enter fullscreen mode Exit fullscreen mode

为什么会失败？如果我们将来看到这个错误消息，它会帮助我们知道发生了什么吗？

答案显然是*否*，但是只需很少的努力，我们就可以添加一点代码来使错误消息更加清晰(实现`toString()`方法)。

```
@Override
public String toString() {
  return "Fraction{" +
    "numerator=" + numerator +
    ", denominator=" + denominator +
    '}';
} 
```

Enter fullscreen mode Exit fullscreen mode

```
java.lang.AssertionError:
Expected: is <Fraction{numerator=3, denominator=2}>
     but: was <Fraction{numerator=1, denominator=2}> 
```

Enter fullscreen mode Exit fullscreen mode

这条错误消息比前一条更清楚，有助于我们在测试代码时以及将来出现回归错误时更加有效，我们只是通过添加由 IDE 生成的`toString()`实现来实现这一点。

拿那个挂得很低的水果。开始提高你的红人！