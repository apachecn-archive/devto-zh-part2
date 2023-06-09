# 面向 Java 开发人员的 C#:扩展方法

> 原文：<https://dev.to/rnowif/c-for-the-java-developer-extension-methods-n69>

在花了几年时间编写 Java 代码之后，我最近决定重新钻研 C#并分享我在这个过程中所学到的东西。在这篇博文中，我将讨论扩展方法。这个概念存在于一些 JVM 语言中(如 [Kotlin](https://kotlinlang.org/docs/reference/extensions.html) )但不存在于 Java 中，它允许开发者在不接触代码的情况下向类中添加方法(因此得名*扩展*方法)。

假设您想要创建一个方法，将一个字符串的第一个字母大写，并返回新的字符串。您不能在 Java 中修改(甚至继承)类`String`来添加这个方法。由于您不一定想要为您的代码库创建自己的定制风格的`String`类，您可能会创建一个静态函数，该函数将一个`String`作为参数并返回另一个`String` :

```
class StringExtensions {
  static String capitalize(String word) {
      return Character.toUpperCase(word.charAt(0)) + word.substring(1);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可以像这样简单地调用这个函数:

```
String word = "hello";
String capitalizedWord = StringExtensions.capitalize(word); 
```

Enter fullscreen mode Exit fullscreen mode

当然，在 C#中创建静态方法也是可以的。然而，C#有一个特性，允许在不修改代码的情况下向类中添加新方法。这个特性叫做*扩展方法*。为了创建一个扩展方法，您必须创建一个顶级静态类并实现一个静态方法。该方法的第一个参数指定了该方法操作的类型，并且应该有`this`修饰符:

```
static class StringExtensions
{
  static string Capitalize(this String word)
  {
      // Note that this cannot access any private data in the String class. 
      return char.ToUpper(word[0]) + word.Substring(1);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使用它，必须用一个`using`指令指定包含该类的名称空间。之后，可以调用该方法，就好像它是类型:
的实例方法一样

```
string word = "hello";
string capitalizedWord = word.Capitalize(); 
```

Enter fullscreen mode Exit fullscreen mode

扩展方法非常强大。除了代码看起来“更干净”这一事实之外，还可以通过简单地导入一个名称空间，在现有类型中无缝地插入新的行为。扩展代码可以位于单独的特定模块中，只在需要时才导入。