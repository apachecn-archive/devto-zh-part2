# 使用 ShouldKO 获得更好的断言消息

> 原文：<https://dev.to/brightdevs/use-shouldko-for-better-assertion-messages-2l1l>

如今，我们大多数人都在写测试。无论是单元测试、集成测试、端到端测试还是性能测试，一旦写好，我们通常不会回头去看，直到它们失败。因此，当测试失败时，有一个明确的断言消息是至关重要的。

# 汉克雷斯特

我认为 [Hamcrest](http://hamcrest.org/) 是 Java 和 Kotlin 生态系统中最流行的断言库。让我们看一个 [`Money`](https://martinfowler.com/bliki/ValueObject.html) 类的过于简化的例子:

```
import java.math.BigDecimal
import java.util.*

data class Money(val amount: BigDecimal, val currency: Currency) {
    operator fun plus(other: Money): Money {
        if (currency != other.currency) {
            throw IllegalArgumentException("Cannot add $this to $other. Currencies must match.")
        }

        // an accidentally introduced bug 😈
        val newAmount = amount + other.amount + 10.0.toBigDecimal()
        return copy(amount = newAmount)
    }
} 
```

假设我们使用 [JUnit](http://junit.org/junit5/) 和 [Hamcrest](http://hamcrest.org/) :
为 [`plus`](https://kotlinlang.org/docs/reference/operator-overloading.html) 操作符编写一个测试

```
class MoneyTests {
    val usd = Currency.getInstance("USD")

    @Test
    fun can_add() {
        val usd100 = Money(100.toBigDecimal(), usd)
        val usd50 = Money(50.toBigDecimal(), usd)

        assertThat((usd100 + usd50).amount, equalTo(150.toBigDecimal()))
    }
} 
```

测试将失败，并显示以下消息:

```
java.lang.AssertionError: 
Expected: <150>
     but: was <160> 
```

让我们看看我们如何能改进它。

# should ko:kot Lin 更好的断言消息

ShouldKO 是我开发的一个简单的库，它改进了断言消息。它的思想基于。净如[宜](https://github.com/shouldly/shouldly)。让我们看看使用 [ShouldKO](https://github.com/miensol/shouldko) :
的测试是什么样的

```
class MoneyTests {
    val usd = Currency.getInstance("USD")

    @Test
    fun can_add() {
        val usd100 = Money(100.toBigDecimal(), usd)
        val usd50 = Money(50.toBigDecimal(), usd)

        (usd100 + usd50).amount.shouldEqual(150.toBigDecimal())
    }
} 
```

在我看来 [ShouldKO 的](https://github.com/miensol/shouldko)断言语法提高了可读性。然而，这并不是 [ShouldKO](https://github.com/miensol/shouldko) 主要改进的地方。遵循改进的断言消息:

```
java.lang.AssertionError: (usd100 + usd50).amount 
Expected: <150>
     but: was <160> 
```

[ShouldKO](https://github.com/miensol/shouldko) 将带有断言的源代码行合并到断言消息本身中。当我们有多行断言形成一个逻辑条件时，这非常方便。这是一件小事，但是可以极大地改善调试测试问题，特别是当我们只有一个测试运行产生的日志文件时。

## should ko 的安装

[ShouldKO](https://github.com/miensol/shouldko) 目前在 [Jitpack](https://jitpack.io/) 上有售。您需要首先将 Jitpack 添加到您的库:

```
repositories {
    maven { url 'https://jitpack.io' }
    mavenCentral()
} 
```

并在测试中包含该库，例如:

```
testImplementation 'com.github.miensol.shouldko:hamcrest:v0.1.0' 
```

ShouldKO 的 Hamcrest 库允许使用任何 Hamcrest 匹配器。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)