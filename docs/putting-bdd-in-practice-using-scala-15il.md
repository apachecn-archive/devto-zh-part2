# 使用 Scala 实践 BDD

> 原文：<https://dev.to/bmbferreira/putting-bdd-in-practice-using-scala-15il>

本文旨在简要解释什么是行为驱动开发(BDD ),以及如何使用它来填补利益相关者和开发团队之间的信息鸿沟，确保每个人(技术或非技术)都参与到项目的进程中。

我第一次接触这种软件开发方法是在几年前的一次大型开放式在线课程(MOOC)中，我必须警告你，我从未在生产中应用过这种方法。然而，我充分意识到[这不是圣杯，它可能不会像宣传的那么好](https://dzone.com/articles/how-completely-fail-bdd)，但是，让团队中的每个人(不仅仅是开发人员)在开发过程中合作的想法，对我来说听起来非常浪漫。

在过去的几个月里，我一直在使用 Scala，并且一直在思考使用 Scala 环境中最流行的测试工具(ScalaTest 和 Specs2)和最流行的 BDD 工具 Cucumber 来应用 BDD 的最佳方式是什么。在本文中，我将使用一个非常简单的例子来比较这三种工具。

### 什么是 BDD？

BDD 可以非常简单地描述为[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development) ( **TDD** )的扩展。
典型的 TDD 周期，又名[红色/绿色/重构](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html)，从失败测试的开发开始。下一步是编写通过测试的代码，最后，重构代码以提高其可读性和可维护性。

[![Typical TDD cycle](../Images/3c71d7815ec882b755feab564a1cd852.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qi2VcqwO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c73l2lmwkewewlcqe4u2.png)

相比之下，BDD 周期与后者几乎相同，只是第一步被规范的编写所取代，而不是测试的开发。这意味着第一步可以由非技术人员完成，例如业务团队的成员或了解系统应该如何运行的功能分析师。最好的部分是这个规范可以用于开发过程中的测试自动化。

[![Typical BDD cycle](../Images/3c79d8fa77492c0290a159701b68ae37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZX7AvH9j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/29qwvbtqg32bewrk3dwq.png)

BDD 也可以描述为[领域驱动设计( **DDD** )](https://en.wikipedia.org/wiki/Domain-driven_design) 和 TDD 的交集。
领域驱动设计是一种软件开发方法，旨在处理具有复杂业务规则的项目，在这种情况下，开发人员和领域专家(通常由客户承担这一角色)之间的合作是必要的，以建立一种通用语言和一个领域模型，该模型可以转化为非常具体和详细的需求。在 BDD 中，这个特定的需求被用来在周期的第一步驱动开发过程。

[![DDD, BDD and TDD](../Images/f60e08354a37fe5651a222209916f00f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bNQ2yFo---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nzcgl8hi4ovn5uf9urnr.png)

### 实践 BDD

显然，BDD 可以在不使用特定工具或框架的情况下付诸实践，你只需要让人们**协作**，**以某种形式的规范记录**这种协作，然后**自动化**该规范以驱动实现。然而，有一些工具可以在自动化过程中提供很大的帮助，所以我们不需要寻找一种新的方法来从自动化测试的需求中提取值。

#### 黄瓜

黄瓜可能是 BDD 最流行的工具，也是我第一次使用的工具。它被宣传为能够使用可执行的规范来简化测试自动化，从而生成每个人都容易理解的活文档。

[![Cucumber Venn Diagram](../Images/97251dc9b17eb0013d449ee43424aa4d.png)T2】](https://cucumber.io/docs/gherkin/reference/)

它最初是用 Ruby 开发的，但也有官方的 Java 和 Javascript 版本。

规范是用 [Gherkin](https://cucumber.io/docs/gherkin/reference/) 编写的，这是一组简单的语法规则，生成的纯文本结构足以让 cumber 理解。这里有一个简单的银行账户特征规范的例子，持有人可以从这里提取现金:

```
Feature: Account Holder withdraws cash

  Scenario: The account has sufficient funds
    Given an account with a balance of $100.5
    When the Account Holder requests $20.25
    Then the account balance should be $80.25

  Scenario: The account has insufficient funds
    Given an account with a balance of $80.25
    When the Account Holder requests $100.75
    Then the Account Holder should be notified that overdrafts are not permitted 
```

Enter fullscreen mode Exit fullscreen mode

小黄瓜文档存储在`.feature`文本文件中，它支持[不同的方言](https://cucumber.io/docs/gherkin/reference/#gherkin-dialects)，所以你甚至可以用世界语编写你的规范！

前一个特性的 Scala 实现是这样的:

```
class BankAccount(val balance: Double) {

  def debit(amount: Double): Either[String, BankAccount] = {
    if (amount < 0.0) {
      Left(s"The debit amount must be > 0.0")
    } else if (balance - amount < 0.0) {
      Left("Overdrafts are not permitted")
    } else {
      Right(new BankAccount(balance - amount))
    }
  }

  def credit(amount: Double): Either[String, BankAccount] = {
    if (amount < 0.0) {
      Left("The credit amount must be > 0.0")
    } else {
      Right(new BankAccount(balance + amount))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我相信代码不需要任何详细的解释。它基本上支持创建一个`BankAccount`实例，其中给定金额的现金可以借记和贷记，但是禁止透支。

使用[这个库](https://github.com/lewismj/cucumber),我成功地在 Scala 项目中自动执行了前面的特性。看起来是这样的:

```
class StepDefinitions extends ScalaDsl with EN {

  var bankAccount: BankAccount = _
  var result: Either[String, BankAccount] = _

  Given("""^an account with a balance of \$(.+)$""") { (balance: Double) =>
    bankAccount = new BankAccount(balance)
  }

  When("""^the Account Holder requests \$(.+)$""") { (amount: Double) =>
    result = bankAccount.debit(amount)
  }

  Then("""^the account balance should be \$(.+)$""") { (expectedResult: Double) =>
    assert(result.right.exists(_.balance == expectedResult))
  }

  Then("""^the Account Holder should be notified that overdrafts are not permitted$""") { () =>
    assert(result.left.exists(_.equalsIgnoreCase("overdrafts are not permitted")))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们只是从每个规范语句中提取值，并用这些值进行操作和断言。

正如您可能已经注意到的那样( [codacy 显然注意到了这一点](https://app.codacy.com/gh/bmbferreira/BddScalaTestVsCucumberVsSpecs2/issues/index)，我不得不牺牲[的不变性](https://www.ibm.com/developerworks/library/j-ft4/index.html)，这是[函数式编程](http://en.wikipedia.org/wiki/Functional_programming)的构建模块之一，通过使用两个可变变量来实现最后步骤中的断言。如果每一步都可以返回一个值，在接下来的步骤中使用，并在最后进行断言，这样会更简洁。我不是唯一一个注意到这个限制的人，一些[问题](https://github.com/cucumber/cucumber/issues/214)已经被报道来使这个功能更强大。然而，这些改进似乎不会在不久的将来发生，因为已经宣布放弃对 JVM 语言的[支持](https://cucumber.io/blog/2018/05/19/cucumber-jvm-languages-support)，这显然包括 Scala。

#### ScalaTest

ScalaTest 可能是 Scala 生态系统中最流行的测试工具。它提供了一些有助于 BDD 风格的特征，支持更符合语法的结构，以便按照规范编写测试。

前面显示的 BankAccount 实现的测试如下所示:

```
class BankAccountSpecs extends FeatureSpec with GivenWhenThen {

  feature("Account Holder withdraws cash") {
    scenario("The account has sufficient funds") {
      val balance = 100.5
      Given(s"""an account with a balance of $$$balance""")
      val bankAccount = new BankAccount(balance)
      val withdrawAmount = 20.25
      When(s"the Account Holder requests $$$withdrawAmount")
      val resultBankAccount = bankAccount.debit(withdrawAmount)
      val expectedBalance = 80.25
      Then(s"the account balance should be $$$expectedBalance")
      assert(resultBankAccount.right.exists(_.balance == expectedBalance))
    }

    scenario("The account has insufficient funds") {
      val balance = 80.25
      Given(s"""an account with a balance of $$$balance""")
      val bankAccount = new BankAccount(balance)
      val withdrawAmount = 100.75
      When(s"the Account Holder requests $$$withdrawAmount")
      val resultBankAccount = bankAccount.debit(withdrawAmount)
      val expectedMessage = "overdrafts are not permitted"
      Then(s"the Account Holder should be notified that $expectedMessage")
      assert(resultBankAccount.left.exists(_.equalsIgnoreCase(expectedMessage)))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

与 Cucumber 相比，我注意到使用 ScalaTest 的主要区别是规范文本与代码内联，给定的/When/Then 语句基本上只是控制台的打印日志，提供了比测试名称更大的粒度。因此，规范与代码完全分离的优势就丧失了。

#### Specs2

Specs2 是 Scala 生态系统中另一个流行的测试工具，也是我最喜欢的。它还提供了一个[特性](https://etorreborre.github.io/specs2/guide/SPECS2-3.6.6/org.specs2.guide.GivenWhenThenStyle.html#full-support)，支持使用规范进行测试的 BDD 风格，在我看来，这是在 Scala 中实践 BDD 的最佳选择。下面是成功撤销场景的代码:

```
class BankAccountWithSufficientFunds extends Specification with GWT {

  def is =
    s2"""
     Given an account with a balance of $$100.5     ${bankAccount.start}
     When the Account Holder requests $$20.25
     Then the account balance should be $$80.25     ${bankAccount.end}
    """

  private val bankAccount =
    Scenario("bankAccount")
      .given(aBankAccount)
      .when(aDouble) { case debit :: bAccount :: _ => bAccount.debit(debit) }
      .andThen(aDouble) {
        case expectedBalance :: bAccount :: _ =>
          bAccount.right.exists(_.balance == expectedBalance)
      }
} 
```

Enter fullscreen mode Exit fullscreen mode

可以看出，规范文本也没有完全从代码中分离出来，但是它没有被内联。它在一个单独的字符串中，可以很容易地从其他地方提取出来。在这个字符串中，我们必须指定场景从哪里开始，在哪里结束，这两点之间的每一行必须对应于场景中的一个`given`、`when`或`andThen`调用。

我们还必须为 balance 和 extracted 值编写一些自定义的步骤解析器，并在从规范字符串中提取值时实例化`BankAccount`。下面是代码:

```
object CustomStepParsers extends StepParsers {

  private val regex = ".*\\$(.*)$"

  def aDouble: StepParser[Double] = readAs(regex).and((s: String) => s.toDouble)

  def aBankAccount: StepParser[BankAccount] =
    readAs(regex).and((s: String) => new BankAccount(s.toDouble))

} 
```

Enter fullscreen mode Exit fullscreen mode

最后，通知账户持有人不允许透支的不成功取款操作的代码:

```
class BankAccountWithInsufficientFunds extends Specification with GWT {

  def is =
    s2"""
     Given an account with a balance of $$80.25                                        ${bankAccount.start}
     When the Account Holder requests $$100.75
     Then the Account Holder should be notified that overdrafts are not permitted      ${bankAccount.end}
    """

  private val bankAccount =
    Scenario("A Bank Account with Insufficient Funds")
      .given(aBankAccount)
      .when(aDouble) { case debit :: bAccount :: _ => bAccount.debit(debit) }
      .andThen() {
        case insufficientFunds :: bAccount :: _ =>
          bAccount.left.exists(msg => insufficientFunds.toLowerCase.contains(msg.toLowerCase))
      }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

我认为 BDD，如果正确应用，并且每个人都投入其中，可能是一种非常有效的软件开发方法。尤其是对于大量团队成员对业务逻辑没有深刻理解的项目。有一种方法来正式指定系统应该做什么以及它应该如何表现，并使用这种规范进行测试和文档记录，可以避免开发过程中的许多问题。

关于工具比较，尽管 Cucumber 是实践 BDD 的标准工具，但我认为 Specs2 绝对是 Scala 中这个简单例子的最佳测试工具。

包含这篇博文所用代码的项目可以在 [GitHub](https://github.com/bmbferreira/BddScalaTestVsCucumberVsSpecs2) 中找到，请随意查看。

*见原文[此处](https://www.codacy.com/blog/putting-bdd-in-practice-using-scala/)T3】*