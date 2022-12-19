# 使用 Hamcrest 和 JUnit 为字符串对象编写断言

> 原文：<https://dev.to/code2bits/writing-assertions-for-string-objects-using-hamcrest-and-junit-33d0>

在这篇文章中，我将向您展示在编写 JUnit 测试用例时，如何利用 Hamcrest 库为 String 对象编写断言。这篇文章提供了一些有用的例子，以及一些我从经验中学到的重要技巧。

### 要求

下面的列表定义了我用来实现示例代码的技术和库:

*   哈姆克雷斯特图书馆 1.3 版。[(下载)](https://code.google.com/archive/p/hamcrest/wikis/HamcrestDistributables.wiki)
*   JDK 1.8 [(下载)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
*   Spring 工具套件[(下载)](https://spring.io/tools/sts/all)

### 多个帖子

这篇文章是关于如何使用 Hamcrest java 库编写断言作为单元测试的一部分的系列文章的一部分。

*   使用 Hamcrest 和 JUnit 为字符串对象编写断言
*   使用 Hamcrest 和 JUnit 为集合编写断言

### 简介

Hamcrest 是一个帮助用 Java 编程语言编写单元测试用例的框架。Hamcrest 这个名字是“matchers”的变位词。Hamcrest 是一个匹配器库，可以组合起来创建测试中灵活的意图表达式，从而允许以声明方式定义“匹配”规则。这些匹配器在诸如 JUnit 和 jMock 之类的单元测试框架中有用途。Hamcrest 从 2012 年开始包含在 JUnit 4 中，但在 2017 年从 Junit 5 中省略了。

> 尽管 JUnit Jupiter 提供的断言工具对于许多测试场景来说已经足够了，但是有时候还是需要更多的功能，比如匹配器。在这种情况下，JUnit 团队建议使用第三方断言库，如 AssertJ、Hamcrest、Truth 等。因此，开发人员可以自由使用他们选择的断言库。~ [JUnit 5 用户指南](https://junit.org/junit5/docs/current/user-guide/)

### 示例:字符串对象的断言

本节包含了许多例子，说明如何利用 Hamcrest Java 库为字符串对象编写断言，作为 JUnit 测试用例的一部分。这并不是所有可能场景的完整列表，在这些场景中，您可以为 String 对象编写断言，而是作为常用示例的列表。

### 1。断言两个字符串对象相等。

一个 JUnit 测试方法的例子，使用“is”ham crest 匹配器断言两个 String 对象相等。

```
@Test 
public final void validateTwoStringsEqual() { 
    String actualValue = "myTestValue"; 
    String expectedValue = "myTestValue"; 
    assertThat(actualValue, is(expectedValue)); 
} 
```

### 2。断言两个字符串对象相等。

一个 JUnit 测试方法的例子，使用“equal to”ham crest 匹配器断言两个 String 对象相等。

```
@Test 
public final void validateTwoStringsEqualTo() { 
    String actualValue = "myTestValue"; 
    String expectedValue = "myTestValue"; 
    assertThat(actualValue, equalTo(expectedValue)); 
} 
```

### 3。断言两个字符串对象不相等。

一个 JUnit 测试方法的例子，使用“not”和“equal to”ham crest 匹配器断言两个 String 对象不相等。

```
@Test 
public final void validateTwoStringsNotEqualTo() { 
    String actualValue = "myTestValue"; 
    String expectedValue = "myTestValue"; 
    assertThat(actualValue, not(equalTo(expectedValue))); 
} 
```

### 4。断言字符串总是匹配的。

一个 JUnit 测试方法的例子，断言一个字符串对象总是使用‘any’ham crest 匹配器进行匹配。

```
@Test 
public final void validateStringIsAnything() { 
    String actualValue = "myTestValue"; 
    assertThat(actualValue, anything()); 
} 
```

### 5。断言字符串总是以指定的值行进

一个 JUnit 测试方法的例子，断言一个字符串对象总是使用‘any’ham crest 匹配器匹配，但是用指定的字符串描述自己。

```
@Test 
public final void validateStringIsAnyString() { 
    String actualValue = "myTestValue"; 
    String specifiedValue = "Anything"; 
    assertThat(actualValue, anything(specifiedValue)); 
} 
```

### 6。断言字符串以预期值开始。

一个 JUnit 测试方法的例子，使用“starts with”ham crest 匹配器断言一个字符串对象以一个期望值开始。

```
@Test 
public final void validateStringStartsWith() { 
    String actualValue = "myTestValue"; 
    String expectedValue = "my"; 
    assertThat(actualValue, startsWith(expectedValue)); 
} 
```

### 7。断言字符串包含预期值。

一个 JUnit 测试方法的示例，使用“contains String”ham crest 匹配器断言一个字符串对象包含一个预期值。

```
@Test 
public final void validateStringContainsString() { 
    String actualValue = "myTestValue"; 
    String expectedValue = "Test"; 
    assertThat(actualValue, containsString(expectedValue)); 
} 
```

### 8。断言字符串以期望值结尾。

一个 JUnit 测试方法的例子，使用“endsWith”ham crest 匹配器断言一个字符串对象以一个期望值结束。

```
@Test 
public final void validateStringEndsWithString() { 
    String actualValue = "myTestValue"; 
    String expectedValue = "Value"; 
    assertThat(actualValue, endsWith(expectedValue)); 
} 
```

### 9。断言字符串等于预期值，但忽略大小写。

一个 JUnit 测试方法的示例，使用“equalToIgnoringCase”ham crest 匹配器断言字符串对象等于预期值，同时忽略大小写。

```
@Test 
public final void validateStringEqualToIgnoringCase() { 
    String actualValue = "myTestValue"; 
    String expectedValue = "mytestvalue"; 
    assertThat(actualValue, equalToIgnoringCase(expectedValue)); 
} 
```

### 10。断言字符串等于预期值，但忽略空白。

一个 JUnit 测试方法的例子，使用“equaltoignoringwhite space”ham crest 匹配器断言一个字符串对象等于一个预期值，同时忽略空白。

```
@Test 
public final void validateStringEqualToIgnoringWhiteSpace() { 
    String actualValue = "myTestValue"; 
    String expectedValue = " myTestValue "; 
    assertThat(actualValue, equalToIgnoringWhiteSpace(expectedValue)); 
} 
```

### 11。断言字符串对象为空。

使用“isEmptyString”ham crest 匹配器断言字符串对象为空的 JUnit 测试方法示例。

```
@Test 
public final void validateStringIsEmptyString() { 
    String actualValue = ""; 
    assertThat(actualValue, isEmptyString()); 
} 
```

### 12。断言字符串对象为空。

一个 JUnit 测试方法的示例，使用“isEmptyOrNullString”ham crest 匹配器断言字符串对象为空。

```
@Test 
public final void validateStringIsEmptyOrNullString1() { 
    String actualValue = ""; 
    assertThat(actualValue, isEmptyOrNullString()); 
} 
```

### 13。断言字符串对象为空。

一个 JUnit 测试方法的示例，使用“isEmptyOrNullString”ham crest 匹配器断言字符串对象为空。

```
@Test 
public final void validateStringIsEmptyOrNullString2() { 
    String actualValue = null; 
    assertThat(actualValue, isEmptyOrNullString()); 
} 
```

### 14。断言字符串按顺序包含预期值。

一个 JUnit 测试方法的示例，它使用“stringContainsInOrder”ham crest 匹配器断言一个字符串对象包含预期值。

```
@Test 
public final void validateStringContainsInOrder() { 
    String actualValue = "ABCDEFGH"; 
    assertThat(actualValue, stringContainsInOrder(Arrays.asList("A", "B", "C"))); 
} 
```

### 总结

本文中列出的例子并不是如何利用 Hamcrest Java 库为 String 对象编写断言的完整列表。这篇文章展示了在字符串对象上编写断言的一些更常见的场景。如需更多信息和示例，请在任何不同的社交媒体平台上关注我，并随时发表评论。

帖子[使用 Hamcrest 和 JUnit](https://www.code2bits.com/writing-assertions-for-string-objects-using-hamcrest-and-junit/) 为 String 对象编写断言最早出现在 [Code2Bits](https://www.code2bits.com) 上。