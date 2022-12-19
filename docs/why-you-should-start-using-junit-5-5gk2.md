# 为什么您应该开始使用 JUnit 5

> 原文：<https://dev.to/stealthmusic/why-you-should-start-using-junit-5-5gk2>

*这是我博客上发表的一篇文章的交叉帖子，你可以在这里找到[。](https://return.co.de/blog/articles/why-you-should-start-using-junit-5/)*

JUnit 5 是什么？

据其[网站](https://junit.org/junit5/)，

> JUnit 5 是 JUnit 的下一代。目标是为 JVM 上的开发人员端测试创建一个最新的基础。这包括关注 Java 8 和更高版本，以及支持许多不同风格的测试。

这篇文章是写给那些听说过或没有听说过 JUnit 5，并且想知道为什么他们会想使用它的人的。

我不想详细介绍所有的功能或其内部工作原理，我只想向*展示一些迄今为止对我来说最有价值的新功能。*

所以让我们开始吧...

# 显示名称

以前在 JUnit 中，测试的名字来自于它们的测试方法。

例如:

```
 public class AccountTest {

        @Test
        public void testMethod() {
            // do some tests here
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

当您运行它时，它将打印出`testMethod`作为测试的名称，这可能不是很有用，最重要的是不可读。测试应该是人类可读的，因为测试是唯一不会被否决的真正的代码文档。所以人们开始尝试想出一些更具描述性的测试名称。

```
 public class AccountTest {

        @Test
        public void withdrawSomeAmountGreaterThanCurrentBalanceFailsWithException() {
            // do some tests here
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

好些了吗？没有那么多。至少它让你对这里将要发生的事情有所了解。关于从账户中提取大于当前余额的金额。这应该会失败。但是如何让它更具可读性呢？一些开发人员(包括我自己)开始打破 camelCase 严格的 Java 惯例，开始使用下划线来分隔测试的各个方面，例如`method__scenario__expectedResult`。

```
 public class AccountTest {

        @Test
        public void withdraw__amountGreaterThanCurrentBalance__failsWithException() {
            // do some tests here
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

虽然这伤害了每个习惯于 camelCase 的 Java 开发人员的眼睛，但是可读性更好一些。您实际需要的是使用任意字符串作为测试名称。

Kotlin 允许你使用字符串文字，比如

```
 internal class AccountTest {

        @Test
        fun `should thrown an exception when an amount is withdrawn greater that the current balance`() {
            // do some tests here
        } 
```

Enter fullscreen mode Exit fullscreen mode

朱尼特 5 号前来救援。尽管不像 Kotlin 中那么简单，JUnit 5 附带了一个可以用于测试类和方法的`@DisplayName`注释。缺点是，你还需要做一个方法名:

```
 @DisplayName("An account")
    class AccountTest {

        @Test
        @DisplayName("should throw an exception when an amount is withdrawn greater that the current balance")
        void withdrawBalanceExceeded() {
            // do some tests here
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

因此，尽管这是典型的 Java 注释混乱，但结果(如 IDE 所示)更具可读性。

*注意，您不需要再对方法和类使用`public`。*

# 嵌套测试

当我和一个同事结对编程时，我复制并粘贴测试和描述，只是修改了一部分来测试不同边缘情况。她告诉我应该使用嵌套测试。我们尝试了一下，我立刻爱上了这个想法。与其进行这样的测试:

*   一个账户
    *   当提取的金额大于当前余额时，应引发异常
    *   应该从余额中减去提取的金额

你可以做类似的事情

*   一个账户
    *   撤退
        *   当金额大于当前余额时，应引发异常
        *   应减少余额的金额

在 JUnit 5 中，它看起来是这样的:

```
 @DisplayName("An account")
    public class AccountTest {

        @Nested
        @DisplayName("withdrawal")
        class Withdrawal {

            @Test
            @DisplayName("should throw an exception when the amount greater that the current balance")
            public void failureBalanceExceeded() {
                // do some tests here
            }

            @Test
            @DisplayName("should reduce the balance by the amount")
            public void success() {
                // do some tests here
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

同样，使用`@Nested`增加了总行数，因为有必要在嵌套类中包装测试，但它保持了测试的自清洁性。

# 分机

JUnit 5 的构建非常具有可扩展性。通过在类级别使用`@ExtendWith`注释，可以添加自定义或第三方扩展。

## Spring Boot

例如，现在通过使用`@ExtendWith(SpringExtension.class)`来运行 Spring boot 集成测试。请注意，这只适用于 spring-boot 2.x 和更高版本。

## 莫克托扩展

将会有一个更新的`MockitoExtension`可以做类似的事情:

```
 @ExtendWith(MockitoExtension.class)
    class MyMockitoTest {

        @BeforeEach
        void init(@Mock Person person) {
            when(person.getName()).thenReturn("Dilbert");
        }

        @Test
        void simpleTestWithInjectedMock(@Mock Person person) {
            assertEquals("Dilbert", person.getName());
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

所以你既不需要写`Person person = mock(Person.class)`也不需要使用`@Mock private Person person`。

# 从 4 迁移到 5

JUnit 5 中的这些新特性是有代价的:它们与 JUnit 4 测试不兼容。这被称为朱尼特木星。

"但是等等，我现有的 500 个测试用例套件怎么办？"你可能会问。事情没有你想象的那么糟。JUnit 团队做了一件聪明的事情，他们将所有的 JUnit Jupiter 代码和注释放在一个单独的包中，这样就可以将 JUnit 4 和 5 放在同一个代码库中。您仍然需要添加新的 JUnit 平台，JUnit 4 测试被称为 JUnit Vintage。你可以在这里找到一篇关于迁移的好文章。

我们实际上有一个包含数百个测试的项目，我们一步一步地迁移它们。所有新的测试都是用 JUnit 5 编写的，一旦我们需要接触现有的测试，我们就迁移它们。它基本上删除了所有 JUnit `import`，用`@BeforeEach`替换`@Before`，用`@BeforeAll`替换`@BeforeClass`。

# 结论

虽然 Java 本身可以通过引入元编程(这也有它的缺点)或至少允许字符串作为方法名来提高测试的可读性而得到很大的改进，但是 JUnit 5 比 JUnit 4 好得多，我认为将来会有很多扩展。

此外，JUnits 断言库有很多改进，但是因为我使用了 [AssertJ](https://joel-costigliola.github.io/assertj/) ，所以我没有讨论那部分。请参考 JUnit 5 文档。