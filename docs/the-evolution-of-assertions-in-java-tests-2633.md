# Java 测试中断言的发展

> 原文：<https://dev.to/martinhaeusler/the-evolution-of-assertions-in-java-tests-2633>

作为一名开发人员，我当然有资格成为一名测试狂。我绝对喜欢产生有意义输出的自动化测试。今天，我想把重点放在自动化测试的历史和现状上，更具体地说:放在断言上。

> 声明:在这篇文章中，我们讨论的是*测试*的断言。其他种类的断言(比如前置和后置条件以及`assert`关键字)超出了本文的重点。

# 一些要测试的代码

没有至少一个断言的自动化测试不是一个合适的测试(除了冒烟测试)。对于本文，我们将测试下面的 Person 类:

```
public class Person {

    private String name;
    private Date birthDate;
    private final Set<String> hobbies = new HashSet<>();

    public Person(){}

    public String getName() { 
        return this.name; 
    }

    public void setName(String name){ 
        this.name = name; 
    }

    public Date getBirthDate() { 
        if(this.birthDate == null) { 
            return null; 
        }
        return new Date(this.birthDate.getTime()); 
    }

    public void setBirthDate(Date date) { 
        this.date = new Date(date.getTime()); 
    }

    public Set<String> getHobbies() { 
        return Collections.unmodifiableSet(this.hobbies); 
    }

    public void setHobbies(Set<String> hobbies) {
        this.hobbies.clear();
        if(hobbies != null) { 
            this.hobbies.addAll(hobbies); 
        }
    }

} 
```

> 声明:我很清楚测试一个 bean 类及其访问器不是一个人通常会做的事情。然而，它是一个很好的简单的例子，允许我们关注断言本身。

# 原始断言

在 Java 中陈述一个断言的最基本的方法(没有任何框架)如下:

```
if(someCondition){
    throw new AssertionError("Hey I didn't expect that!");
} 
```

无论你的断言框架表面上多么花哨，最终总会归结到这一点。断言框架在这种最初的断言方法上有几个方面的改进:

*   让它更**简洁**。减少需要编写的代码量。
*   让它变得更加流畅。提供某种构建器语法。
*   自动生成**消息**，使其永远不会与测试不同步。

# 输入 JUnit `Assert`

JUnit 附带了一个名为`Assert`的类。这个类由一系列静态方法组成，这些方法可以帮助用户编写简洁的断言。大多数方法有以下形式:

```
public static void assertXYZ(message, expectedValue, actualValue) { /* ... */ } 
```

...其中`message`是一个可选字符串，如果断言失败，将打印该字符串，而不是自动生成的消息。这种方式的测试如下所示:

```
import org.junit.*

public class PersonTest {

    @Test
    public void testWithAssert(){
        Person p = new Person();
        Assert.assertNull("A new Person should not have an initial name.", p.getName());
        p.setName("John Doe");
        Assert.assertEquals("John Doe", p.getName());
    }

} 
```

此外，人们通常会使用`import static org.junit.Assert.*`来静态导入断言方法。这样，断言前面的`Assert.`可以省略。

这已经比最初的`if`结构前进了一大步:它适合一行。然而，这种方法有几个问题:

*   混淆断言调用和交换`expected`和`actual`是非常容易的。
*   因为`message`总是第一个参数，而且这个参数也是可选的，所以不总是很清楚第一个传递的字符串是`expected`值还是`message`。
*   自动生成的断言错误消息非常简单，可用的断言方法数量非常有限。

# Hamcrest！等待...什么？

JUnit 发布几年后，一个名字叫做 [Hamcrest](http://hamcrest.org/) 的漂亮的小库发布了。Hamcrest 是围绕这样一个思想构建的，即断言本质上是一个**匹配标准**，因此它应该由一个`Matcher`对象来表示。这允许更多的断言变化，有更好的错误消息和更流畅的语法:

```
@Test
public void hamcrestTest(){
    Person p = new Person();

    assertThat(p.getHobbies(), is(empty());
    assertThat(p.getName(), is(nullValue());

    p.setName("John Doe");
    p.setBirthDate(new Date());
    Set<String> newHobbies = new HashSet<>();
    newHobbies.add("coding");
    newHobbies.add("blogging");
    newHobbies.add("reading");
    p.setHobbies(newHobbies);

    assertThat(p.getName(), is("John Doe");
    assertThat(p.getHobbies(), not(hasItem("programming"));
    assertThat(p.getHobbies(), containsInAnyOrder("coding", "blogging", "programming");
    assertThat(p.getHobbies().size(), is(3));
    assertThat(p.getBirthDate().getTime(), is(greaterThan(0L));

} 
```

如您所见，`assertThat(value, matcher)`方法是入口点。与`assertEquals`不同，哪个是`expected`哪个是`actual`值非常清楚，所以这是一个很大的优势。不利的一面是，由于`assertThat(...)`有如此多不同的重载，你不能使用`asserThat(p.getName(), is(null))`，因为`null`使得使用哪个重载变得不明确。相反，您需要使用`nullValue()`，它本质上只是一个匹配器，用于检查与`null`是否相等。

Hamcrest 还引入了带有`not(...)`的否定条件、简单的数字比较以及集合的助手。所有这些(特别是集合助手)本身都会生成非常有用的错误消息，所以通常不再需要提供自定义错误消息(尽管可能)。

Hamcrest 的缺点是它非常依赖静态导入，这甚至可能导致导入冲突(如果你在内部也大量使用静态方法)。另一个缺点是，虽然断言行现在读起来很流畅，但实际上写起来并不流畅:

```
// alright, let's test the name...
p.getName()|

// ... oh, I forgot the assertThat...
assertThat(|p.getName()

// ... moves carret all the way back again...
assertThat(p.getName(), |

// ok IDE, I need the "is" method, do your magic!
assertThat(p.getName(), is|

// (200 completion proposals show up)
// *sigh*
assertThat(p.getName(), is("John Doe"));|

// finally! 
```

明白我的意思吗？幸运的是，有些人也遇到了同样的问题。

# 实话实说！

[Truth](https://google.github.io/truth/) 是一个类似于 Hamcrest 的库，除了它提供了一个流畅的构建器 API:

```
@Test
public void testTruth(){
    Person p = new Person();

    assertThat(p.getName()).isNull();
    Set<String> newHobbies = new HashSet<>();
    newHobbies.add("coding");
    newHobbies.add("blogging");
    newHobbies.add("reading");
    p.setHobbies(newHobbies);

    assertThat(p).containsExactly("coding", "blogging", "reading");
} 
```

这现在终于是一个用于测试的流畅的 API，并且也不需要太多的静态导入。只要遵循 IDE 的代码补全，您将很快创建强大的断言。

和往常一样，这里也有一些警告。我对这个解决方案最迫切的担忧是，与 Hamcrest 相比，它很难扩展。真理库本身不在你的控制之下(除非你叉它...)，所以您不能简单地向现有类添加新方法来做您的自定义断言。

# 测试它如何`shouldBe`

我们现在离开安全的爪哇岛，去野外探险。事实证明，Kotlin 非常适合构建一个只包含两个函数(和 Hamcrest 库)的测试小型框架

```
infix fun <T> T.shouldBe(other: T): Unit {
    assertThat(this, is(other));
}

infix fun <T> T.shouldBe(matcher: Matcher<T>): Unit {
    assertThat(this, matcher);
} 
```

你可能会问“这有什么用”和“这到底是什么”。嗯，我们定义了两个**扩展函数**，它们驻留在`Object`(或者，在 Kotlin 中:`Any`)上。这意味着:我们现在可以在任何事情上调用`x.shouldBe(...)`，不管`x`是什么。此外，它是一个`infix`函数，这意味着我们可以删除点、左大括号和右大括号。

看看这个:

```
@Test
fun testKotlin(){
    Person p = Person()

    p.name shouldBe null
    p.name = "John Doe"
    p.name shouldBe "John Doe"

    p.hobbies = setOf("coding", "blogging", "reading")
    p.hobbies.size shouldBe 3

    p.birthDate = Date()
    p.birthDate!!.time shouldBe greaterThan(0L)

} 
```

现在**这个**就是我正在寻找的那种可读性！

# 进一步阅读

如果你喜欢冒险，我也推荐你去看看

*   斯波克(常规测试)
*   [黄瓜](https://cucumber.io/)(小黄瓜测试语法的由来)

# 结束语

我希望您喜欢这次对断言库的小小参观。请随意分享您的经验，并提供我错过的关于库和/或编码风格的建议！