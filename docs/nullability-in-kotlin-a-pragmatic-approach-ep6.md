# 一种实用的方法。

> 原文：<https://dev.to/raulmonteroc/nullability-in-kotlin-a-pragmatic-approach-ep6>

[![](../Images/9fffbb5718cc3cae0d47414dbab8ce86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F5e5_4Fk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raulmonteroc.com/wp-content/uploads/2018/07/kotlin_800x320-1.png)

NullPointerException 来自其他强类型语言，如 Java 或 C#，是您可以找到的最常见的异常之一，它提供长时间的调试会话只是因为传递并最终调用了一个空引用。

Kotlin 在编译器级别解决了这个问题，当试图给一个有可能包含空值的对象赋值时，使用它抛出错误，* *，除非** 您明确地让 kotlin 知道这是预期的行为，即使这样，您也需要一个特殊的语法来访问该对象的方法。

```
// Java and C# 
string str = null; // valid

//Kotlin 
var str:String = null; // not valid 
```

在上面的代码中，你可以看到，kotlin 甚至不允许将空值赋给变量，如果你想这样做，你必须使用可空类型，这是 kotlin 告诉编译器这个变量有可能包含空值的方式。

您可以在变量类型的末尾添加一个问号，如下所示

```
var str:String? = null; // valid 
```

这段代码可以编译，并允许您在 kotlin 中使用包含空值的变量，方法是向编译器明确声明这是您的意图。

现在，**有一个条件**。还记得我们之前谈到的特殊语法吗？现在是时候使用它了。你看，一旦一个变量包含一个可空类型，你就不能再使用点符号了，所以当调用一个对象的方法或属性时，你必须采取两种方法之一:

1.  **使用双爆炸语法(！！)**访问该方法，并在遇到空值时抛出运行时异常。
2.  使用安全调用语法(？。)访问该方法并返回空值(如果遇到),同时保持程序的工作流不变

```
// Alternative 1: Throw an exception 
var nullError:String? = null var count1 = nullError!!length() // Throws an exception 

// Alternative 2 : Return the null value 
var returnValue:String? = null var count2 = returnValue?.length // Returns the null value and keep the program running 

// dot notation 
var invalidSyntax:String? = null var count3 = invalidSyntax.length //Not valid for nullable types. A compile error is thrown 
```

## 猫王符

尽管 kotlin 不鼓励使用空值，但该语言为那些必须与之交互的情况提供了空检查操作符，从而简化了代码并使其更具可读性。该运算符称为 Elvis 运算符(？:)并按如下方式工作:

*   计算运算符右侧的表达式
*   如果它返回 null，那么计算并返回左边的表达式并返回它
*   如果没有，返回右侧

```
// right side invalid 
var str:String? = null var count = str ?: "" // right side is null, evaluate and return the left side 

//right side valid 
var validStr:String = "Test" var count = validStr ?: "" // right side is not null, return the right side without evaluating the left 
```

## 结论

在 kotlin 中处理空值的方法非常实用，除非使用特殊的语法显式地声明空值，否则不使用空值可以获得编译器验证，这反过来会使您希望尽可能地避免空值，从而防止不必要的 NullPointerExcepetions。

从我的角度来看，这是一个很好的权衡。我知道我一点也不会错过那些例外。

这篇文章是系列文章的一部分，旨在向您介绍这种语言，并体验 kotlin 的工作方式。你可以在这里找到它:[科特林语指南](https://raulmonteroc.com/mobile/kotlin/a-guide-to-the-kotlin-language/)