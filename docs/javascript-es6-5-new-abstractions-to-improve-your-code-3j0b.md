# JavaScript ES6: 5 改进代码的新抽象

> 原文：<https://dev.to/bnevilleoneill/javascript-es6-5-new-abstractions-to-improve-your-code-3j0b>

#### 利用强大的 ES6 特性编写更好、更优雅、更可预测的 JavaScript。

[![](../Images/e4122f9f3546e7a393fe2260d229bbfd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0Kv1F6x2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AndfkO89TKQrzVXJMopCTOQ.jpeg)

JavaScript 是一种非常强大的编程语言，可以在各种平台上运行，尤其是随着像 *Node.js* 这样的 JavaScript 运行时的出现。不同类别和水平的程序员越来越多地采用这种语言。

和大多数事情一样，自从语言诞生以来，它的各种版本都发生了相当多的变化。然而，该语言的 ES6 规范(*通常称为 ES2015* )增加了许多语法改进和新功能。这使得编写 JavaScript 程序更有效，更少出错，而且更有趣。

这些新特性和语法改进包括:*类*，*模块*，*承诺*，*模板文字*，*析构*，*箭头函数*，*生成器*，*集合和映射*，*符号*，以及*类型化数组*，*代理*，

在本文中，我们将探索 ES6 的五个特性，并考虑如何利用它们来改进 JavaScript 代码。以下是感兴趣的特性:

1.  *模板文字*
2.  *默认和休息参数*
3.  *箭头功能*
4.  *解构*
5.  *类*

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 1。模板文字

在 ES6 中，引入了模板文字来处理一些与格式化和表示字符串相关的挑战。使用模板文字，您可以轻松地处理多行字符串。它还可以执行增强的字符串替换，并对看似危险的字符串(如要嵌入到 HTML 中的字符串)进行适当的格式化。

在 ES6 之前，字符串由一对*单引号*(“字符串”)或一对*双引号*(“字符串”)分隔。在 ES6 中，字符串也可以用一对*反斜线* ( `string`)来分隔。这样的字符串被称为**模板文字**。

正如单引号和双引号分隔符一样，如果字符串包含反勾号字符，反勾号也可以在模板文本中进行转义。要对模板文本中的反勾号字符进行转义，必须在反勾号字符之前放置一个反斜杠()。但是请注意，在模板文本中，单引号和双引号不需要转义。

这里有一个简单的例子:

[https://medium . com/media/b 42479358 BC 9 CFD 9185 a4 f 7530 b8a 845/href](https://medium.com/media/b42479358bc9cfd9185a4f7530b8a845/href)

以这种方式使用模板文字与使用由引号分隔的常规 JavaScript 字符串没有什么不同。当处理*多行字符串*、*字符串替换、*和*标记模板*时，我们开始获得真正的优势。

#### 多行字符串

在 ES6 之前，JavaScript 中的字符串被限制为一行。但是，在开始新行之前用反斜杠()结束一行，使得即使新行不在字符串中输出，也有可能创建看似多行的字符串:

[https://medium . com/media/60cf 776 e 0518 e 75 aff 9758 B4 da 10 fa 8d/href](https://medium.com/media/60cf776e0518e75aff9758b4da10fa8d/href)

如果要在字符串中输出一个换行符，需要在换行符前使用换行符转义序列(\n):

[https://medium . com/media/00fd 84 c 220 ecaf 2492 b 71 c 1 bb 4 fcd 4 AE/href](https://medium.com/media/00fd84c220ecaf2492b71c1bb4fcd4ae/href)

> 使用 ES6 模板文本，字符串输出时格式保持不变。

字符串中的所有换行符和空格都被保留，这使得无需任何附加语法就可以轻松创建多行字符串。然而，由于保留了空白，所以在缩进字符串时应该小心。

考虑这个例子:

[https://medium . com/media/176 e 98023 c 16 fefe 45 C2 de 5b 5135 b 044/href](https://medium.com/media/176e98023c16fefe45c2de5b5135b044/href)

请注意，新行和缩进保留在字符串中。trim()方法还用于删除 html 字符串开头和结尾的任何换行符和空格。

#### 字符串替换

模板文字也使字符串替换变得有趣。在 ES6 之前，*字符串连接*严重依赖于创建动态字符串。

这里有一个简单的例子:

[https://medium . com/media/8c 15655 e 2 AC 41 CB 89 a 5498 e 63d 5429d 8/href](https://medium.com/media/8c15655e2ac41cb89a5498e63d5429d8/href)

使用 ES6 模板文字，可以按如下方式进行替换:

[https://medium . com/media/14983 f 0 bcc 194 DCD 42 f 94 C5 f 5349 AE 97/href](https://medium.com/media/14983f0bcc194dcd42f94c5f5349ae97/href)

字符串替换由开始的${和结束的}分隔，并且可以在两者之间包含任何有效的 JavaScript 表达式。

在前面的例子中，我们在模板文本中替换了一个简单变量的值。假设我们想在商店所有商品的价格上增加 10%的折扣。

它看起来是这样的:

[https://medium . com/media/c 20167 db 2 c 78 D1 bb 2 b 6 FEA 6 EB 95 c 00 af/href](https://medium.com/media/c20167db2c78d1bb2b6fea6eb95c00af/href)

这里，我们用一个 JavaScript 表达式的值来替代计算折扣价的值。

> 模板文字本身就是 JavaScript 表达式，因此可以嵌套在其他模板文字中。

#### 模板标签

有了带标签的模板，您甚至可以更好地控制模板文字的替换和转换。一个*模板标签*仅仅是一个定义模板文字应该如何转换的函数。

模板标签函数可以接受多个参数。第一个参数是包含模板文本中所有文本字符串的数组。其余的参数对应于模板文本中的替换。因此，第二个参数对应于第一个替换，第三个参数对应于第二个替换，依此类推。

这里有一个简单的例子。给定以下模板文字:

```
`The price of ${quantity} units of the item on the online store is $${quantity * price}.` 
```

传递给上述模板文本的模板标记的第一个参数将是文本字符串数组，如下所示:

[https://medium . com/media/b7b 82 ffd 4a 099d 20 c 0931870 eeb 61491/href](https://medium.com/media/b7b82ffd4a099d20c0931870eeb61491/href)

第二个参数是数量的值，第三个参数是(数量*价格)的值。

让我们继续创建一个名为 pricing 的模板标记，我们可以用它来转换定价摘要。这将确保价格值四舍五入到小数点后两位。它还将确保在任何价格被转换为美元之前的$货币符号。

下面是函数:

[https://medium . com/media/94383 C4 D7 E3 ca 46 b 9 add 87379 cf 86 fc 8/href](https://medium.com/media/94383c4d7e3ca46b9add87379cf86fc8/href)

您会注意到，在这段代码中，我们使用了一个名为 replacements 的 rest 参数来捕获模板文本中的所有替换。*我们将在下一节*中了解更多关于 rest 参数的信息。

现在我们已经创建了一个模板标签，使用它是最简单的部分。

> 要使用模板标记，只需将模板标记的名称附加在模板文字的第一个反斜杠(`)分隔符之前。

下面是一个使用我们刚刚创建的定价模板标签的示例:

[https://medium . com/media/e 57 da 2d 27 aa 4c 4 fcee 1 be 6 ba 1 EFD cef 2/href](https://medium.com/media/e57da2d27aa4c4fcee1be6ba1efdcef2/href)

### 2。默认和静止参数

JavaScript 中的函数是非常重要的对象。很有可能你遇到过这样的说法:

> *“职能是一等公民”*。

JavaScript 函数也是如此，因为你可以在你的程序中传递它们，就像你传递其他常规值一样。

然而，JavaScript 函数在 ES6 之前没有任何显著的语法改进。在 ES6 中，我们现在有了一些语法上的改进，比如*默认参数*、 *rest 参数*、*箭头函数*等。

#### 默认参数

在 ES6 之前，基本上没有为函数参数设置默认值的语法。然而，当调用时没有向函数参数传递值时，有一些为函数参数设置后备值的技巧。这里有一个简单的例子:

[https://medium . com/media/679 F9 ce 267 cf 5c 9736 e 15 c 970 DC 34 e 75/href](https://medium.com/media/679f9ce267cf5c9736e15c970dc34e75/href)

在这个代码片段中，我们已经能够为函数参数设置默认值。因此，这些参数的行为就像它们是可选的一样，因为当参数没有被传递时，会使用回退值。

在 ES6 中，您可以用默认值初始化函数参数，当参数未被传递时将使用该默认值。下面是我们如何用默认参数重写前面的 convertToBase()函数:

[https://medium . com/media/9805 a 907 faaee 62664 bddedb 26347 f 52/href](https://medium.com/media/9805a907faaee62664bddedb26347f52/href)

ES6 中的命名函数参数具有与 let 声明相同的行为。ES6 中的默认值不仅限于文字值或原始值。

> 任何 JavaScript 表达式也可以用作函数参数的默认值。

这里有一个例子:

[https://medium . com/media/05191 E1 fdbdcec 4 e 8 e 69 B4 fab 1c 6b 54 f/href](https://medium.com/media/05191e1fdbdcec4e8e69b4fab1c6b54f/href)

这里，我们使用 getDefaultNumberBase()的返回值作为基本参数的默认值。当设置另一个参数的默认值时，您甚至可以使用前一个参数的值。这里有一个例子:

 ``function cropImage(width, height = width) {
_// ...implementation_
}`` 

 ``在这个代码片段中，只要没有传递 height 参数，它就会被设置为 width 参数的值。

尽管在设置默认值时可以使用以前的参数值，但不能使用在函数体内声明的变量。这是因为默认参数有自己的作用域，它与函数体的作用域是分开的。

#### 休息参数

arguments 对象是在调用时捕获传递给函数的所有参数的最终手段。这使得创建可以接受不同数量参数的重载函数成为可能。

> 然而，尽管 arguments 对象类似于*数组*，但在对其执行某些数组操作之前，需要将其转换为实际的数组。

这里有一个简单的例子:

[https://medium . com/media/702 b 5420 c 65 EC 054 beba 646 b 7 bdefe 37/href](https://medium.com/media/702b5420c65ec054beba646b7bdefe37/href)

该函数计算传递给它的任意数量的参数之和。如果参数不是数字，它会尝试使用 number()全局函数将其转换为数字。如果没有传递参数，则返回 0。请注意，arguments 对象首先被转换为一个数组，并赋给 args 变量，以便使用 reduce()方法。

在 ES6 中，引入了*其余参数*。一个 **rest 参数** **只是一个前面带三个点**(**)的函数参数...).rest 参数被赋予一个数组，该数组包含传递给函数的其余参数。下面是我们如何使用 rest 参数重写前面的 sum()函数:**

[https://medium . com/media/40326966067088 a6ae 32804 da 0 f 86 c 73/href](https://medium.com/media/40326966067088a6ae32804da0f86c73/href)

**关于 rest 参数，有几点值得注意。**

1.  一个函数只能有一个 rest 参数。

2.  rest 参数(如果存在)必须是最后一个参数。

3.  rest 参数与 arguments 对象不同。它只捕获其他命名参数之后的剩余参数，而 arguments 对象捕获传递给函数的所有参数。

4.  rest 参数不能在对象文本设置器中使用。

#### 传播算子

假设我们有一个包含班级学生分数的数组，我们想计算学生的平均分数。基本上，我们将首先计算分数的总和，然后将总和除以分数的数量。

我们可以使用在上一节中创建的 sum()函数来计算分数的总和。然而，问题是我们有一个分数数组和 sum expects 数字作为参数。

在 ES6 之前，Function.prototype.apply()方法可以用来处理这样的情况。此方法将一个数组作为其第二个参数，该参数表示调用函数时应该使用的参数。

这里有一个例子:

[https://medium . com/media/f 6 E1 af 526 de 4a 54035657 ff 9131 def 6c/href](https://medium.com/media/f6e1af526de4a54035657ff9131def6c/href)

在 ES6 中，一个新的操作符被称为*扩展操作符*(...)被介绍了。它与 rest 参数密切相关，对于处理数组和其他*迭代*非常有用。使用 spread 运算符，我们可以如下计算总分:

 ``const totalScore = sum(...scores);`` 

 ``> 因此，对于大多数用例，spread 操作符是 Function.prototype.apply()方法的一个很好的替代。

### 3。箭头功能

ES6 中另一个非常重要的语法改进是引入了*箭头函数*。Arrow 函数使用了一种全新的语法，当以它们最适合的方式使用时，它提供了一些很大的优势。

箭头函数的语法省略了 function 关键字。此外，使用*箭头* (= >)将函数参数与函数体分开，因此得名*箭头函数*。

尽管箭头函数比常规函数更紧凑、更短，但它们在某些定义使用方式的方面与常规函数有很大不同:

1.  箭头函数不能用作构造函数，它们没有原型。因此，将 new 关键字与 arrow 函数一起使用通常会导致错误。

2.  数组函数没有 arguments 对象，因此函数参数必须使用命名参数和 rest 参数。也不允许使用重复的命名参数。

3.  箭头函数中的 this 绑定不能被修改，它总是指向最近的非箭头父函数。

#### 箭头函数语法

箭头函数可能看起来略有不同，这取决于您想要实现的目标。

让我们来看看一些表格:

**无参数**

如果 arrow 函数没有参数，则在箭头(= >)前使用一对空括号(())，如下面的代码片段所示。

[https://medium . com/media/AEA 6a b 25 fc 6 c 0 f 4c 1066522 b 7185 ed2b/href](https://medium.com/media/aea6ab25fc6c0f4c1066522b7185ed2b/href)

对于像这样只返回 JavaScript 表达式值的非常简单的箭头函数，可以省略 return 关键字和函数体周围的一对花括号({})。

因此，arrow 函数可以重写为:

 ``const getTimestamp = () => +new Date;`` 

 ``但是，如果从 arrow 函数返回一个对象文字，则需要用一对括号(())将它括起来，否则 JavaScript 引擎会将对象文字的大括号({})视为包含函数体，这将导致语法错误。这里有一个例子:

[https://medium . com/media/b2e0e 288843029 ed 0e 732669 b 6962 c 6a/href](https://medium.com/media/b2e0e288843029ed0e732669b6962c6a/href)

**带参数**

对于只接受一个命名参数的箭头函数，可以省略参数列表周围的括号对，如下面的代码片段所示:

[https://medium . com/media/DD 669 a 8529290 c 56995 a 83151 c 84 b 505/href](https://medium.com/media/dd669a8529290c56995a83151c84b505/href)

但是，有些情况下不能省略参数列表周围的括号。下面是一些这样的情况:

1.  当有多个命名参数时

    [https://medium.com/media/f1a6eeb5f3ad8de1ac2a2c9e6575a66e/href](https://medium.com/media/f1a6eeb5f3ad8de1ac2a2c9e6575a66e/href)
2.  当有默认参数时，即使它是唯一的参数

[https://medium . com/media/cc 25 be 3 b 268 a 059 BC 14 A8 a 28d 89 ca 01 c/href](https://medium.com/media/cc25be3b268a059bc14a8a28d89ca01c/href)

1.  当有 rest 参数时，即使它是唯一的参数

[https://medium . com/media/3c 11712 e82a 1652 E1 D1 ba 1 B1 a3 a4 D7 DD/href](https://medium.com/media/3c11712e82a1652e1d1ba1b1a3a4d7dd/href)

1.  当有一个析构参数时，即使它是唯一的参数

[https://medium . com/media/94e 442094 d8f 74 d6d 323 B1 CBD 009 da C1/href](https://medium.com/media/94e442094d8f74d6d323b1cbd009dac1/href)

**传统功能体**

如前所示，非常简单的箭头函数只返回 JavaScript 表达式的值，可以省略 return 关键字和函数体周围的一对花括号({})。但是，如果您愿意，您仍然可以使用传统的函数体，尤其是当函数有多个语句时。

[https://medium . com/media/6f 50 D1 EB 433 BC 156 f 899 ce 7264 f 441 D6/href](https://medium.com/media/6f50d1eb433bc156f899ce7264f441d6/href)

上面的函数试图模仿 JavaScript 库 *Lodash* 的 snakeCase()方法。这里，我们必须使用传统的用花括号({})括起来的函数体，因为函数体内有太多的 JavaScript 语句。

> 与常规函数不同，arrow 函数不存在 arguments 对象。但是，他们可以访问非箭头父函数的 arguments 对象。

[https://medium . com/media/62 F8 D1 be D2 f 33 BDD 428 ad 6 DD 0207691/href](https://medium.com/media/62f8d1bede2f33bdd428ad6dd0207691/href)

#### **立即调用函数表达式(IIFEs)**

JavaScript 中函数的一个有用应用是在*立即调用函数表达式(IIFEs)* 中观察到的，这些函数是 ***函数，它们被立即定义和调用，而不保存对函数*** 的引用。这种函数应用通常见于一次性的初始化脚本，像 *jQuery* 等 JavaScript 库。

使用常规的 JavaScript 函数，IIFEs 通常采用以下形式之一:

[https://medium . com/media/e43 F8 fff 70249 D2 e8c9f7 e6c 4f 53 ce 58/href](https://medium.com/media/e43f8fff70249d2e8c9f7e6c4f53ce58/href)

如果 arrow 函数用括号括起来，arrow 函数语法也可以与 IIFEs 一起使用。

[https://medium . com/media/0469463950 b 6 eff 9 c 7d 11 fef 12 fa 9 b 57/href](https://medium.com/media/0469463950b6eff9c7d11fef12fa9b57/href)

#### 回调函数

回调函数在异步程序和数组方法中大量使用，如 map()、filter()、forEach()、reduce()、sort()等。

> 箭头函数非常适合用作回调函数。

在前面的代码片段中，我们看到了 arrow 函数如何与 reduce()一起使用来计算一个数字数组的和。使用箭头功能更紧凑、更整洁。同样，这里是比较:

[https://medium . com/media/d 809623 a 94963 fc 362 f 42 cef 00 e 918d 4/href](https://medium.com/media/d809623a94963fc362f42cef00e918d4/href)

让我们做一些更复杂的事情来演示如何使用 arrow 函数作为数组回调来帮助我们用更少的代码完成更多的工作。我们将模仿 JavaScript 库的 flattenDeep()方法。此方法递归展平数组。然而，在我们的实现中，我们将递归地展平传递给函数的参数数组。

以下是 flattenDeep()函数的代码片段:

[https://medium . com/media/1304 d28f 895187273 bbfe 26304d 5856 c/href](https://medium.com/media/1304d28f895187273bbfe26304d5856c/href)

这就是数组函数用作回调函数时有多酷，尤其是在使用接受回调函数的数组迭代方法时。

#### 本和箭头功能

许多 JavaScript 程序中混淆和错误的一个主要来源是它的值解析。

> 根据函数调用的范围和上下文，这将解析为不同的值。

例如，当用 new 关键字调用一个函数时，这指向由构造函数创建的实例，然而，当没有 new 关键字调用同一个函数时，这指向在浏览器环境中为非严格模式中的窗口对象的全局对象。

这里有一个简单的例子。在下面的代码片段中，在没有 new 关键字的情况下调用 Person()会意外地创建一个名为 name 的全局变量，因为该函数处于*非严格模式*。

[https://medium . com/media/aa 624 FD 3252955 cf 37 cf 95 ef 8 ebbb 0 f 6/href](https://medium.com/media/aa624fd3252955cf37cf95ef8ebbb0f6/href)

另一个常见的混淆来源是 DOM 事件侦听器。

> 在事件侦听器中，这指向事件所针对的 DOM 元素。

考虑下面的代码片段:

[https://medium . com/media/0 AC 5 f 83 b 6 f1 b 92 FD 7 a 66 c 6878548 b6a 7/href](https://medium.com/media/0ac5f83b6f1b92fd7a66c6878548b6a7/href)

这段代码看起来一切都很好。但是，当您开始垂直滚动浏览器窗口时，您会看到控制台上记录了一个错误。错误的原因是 this.offsets 未定义，而我们正试图访问 undefined 的 offsetY 属性。

问题是:****怎么可能是** **未定义？****

 **这是因为事件侦听器中的 this 的值与封闭原型函数中的 this 的值不同。事件侦听器中的 this 指向作为事件目标的 window，而 offsets 不作为 window 的属性存在。因此，事件侦听器中的 this.offsets 是未定义的。

Function.prototype.bind()可用于显式设置函数的 this 绑定。下面是如何通过使用 Function.prototype.bind()显式设置此绑定来修复错误:

[https://medium . com/media/c 91 ad 40035 c 75 ba 81 ed 53 b 0 f 970 b 437 a/href](https://medium.com/media/c91ad40035c75ba81ed53b0f970b437a/href)

在这里，我们用圆括号将事件侦听器包装起来，并调用 bind()方法，从封闭的原型函数中传递这个值。调用 bind()实际上会返回一个具有指定 this 绑定的新函数。现在一切都很完美，没有任何错误。

> 用 ES6 的箭头功能，就没有这个绑定。因此，箭头函数使用其最近的非箭头函数祖先的值。

在像我们这样的情况下，我们可以使用一个箭头函数来代替使用 bind()来返回一个新函数——因为来自封闭原型函数的 this 绑定被保留。

这是:

[https://medium . com/media/900 a9 da e98 f 29103 CFA 5575 a 0201 b95a/href](https://medium.com/media/900a9dae98f29103cfa5575a0201b95a/href)

### 4。解构

析构是 JavaScript 语法的另一个非常重要的改进。析构使得从复杂结构(如数组和对象)中访问局部变量并为其赋值成为可能，无论这些值在父数组或对象中的嵌套有多深。析构有两种形式:**对象析构**和**数组析构**。

#### 物体毁坏

为了说明对象析构，假设我们有一个国家对象，如下所示:

[https://medium . com/media/d7a 827 d4b 5540267 e 4922 b 60120 f 5127/href](https://medium.com/media/d7a827d4b5540267e4922b60120f5127/href)

我们想向游客展示一些关于这个国家的信息。下面的代码片段显示了一个非常基本的 countryInfo()函数，它就是这样做的:

[https://medium . com/media/63 e5f 87 c 31 f 10a 03 c 77 a2 fc 6577 FAA 35/href](https://medium.com/media/63e5f87c31f10a03c77a2fc6577faa35/href)

在这个代码片段中，我们已经能够从 country 对象中提取一些值，并将它们分配给 countryInfo()函数中的局部变量——效果非常好。

使用 ES6 析构，我们可以提取这些值，并用更优雅、更简洁的语法将它们赋给变量。下面是旧代码片段和 ES6 析构的比较:

[https://medium . com/media/8363 f 717 ACF 66949 CD 017700870 a503 e/href](https://medium.com/media/8363f717acf66949cd017700870a503e/href)

上面代码片段中的这种析构形式被称为*对象析构*——因为我们从一个对象中提取值并将它们赋给局部变量。

> 对于对象析构，在赋值表达式的左边使用对象文字。

您甚至可以对函数参数使用对象析构，如下面的代码片段所示:

[https://medium . com/media/68 c 94 bfe 2 af 346035753122 caf 2 daf 96/href](https://medium.com/media/68c94bfe2af346035753122caf2daf96/href)

#### 数组解构

数组析构用于从数组中提取值并将它们赋给局部变量。假设我们将一种颜色的 RGB(红绿蓝)值表示为数组，如下所示:

 ``const color = [240, 80, 124];`` 

 ``我们想要显示给定颜色的 RGB 值。下面是如何通过数组析构来实现的。

[https://medium . com/media/2a7b 92a 324522690 F2 b 86012 eecbefb 1/href](https://medium.com/media/2a7b92a324522690f2b86012eecbefb1/href)

> 对于数组析构，数组文字用在赋值表达式的左边。

通过数组析构，可以跳过不需要的赋值。假设我们只想要颜色的蓝色值。下面是我们如何跳过红色和绿色值，而不把它们赋给局部变量。

[https://medium . com/media/45c 7a 96 ed 7931 bfc 8749 b 0e 40 a 52 ee 5a/href](https://medium.com/media/45c7a96ed7931bfc8749b0e40a52ee5a/href)

数组析构也可以和函数参数一起使用，就像对象析构一样。然而，还有其他一些方法可以用来解决常见的问题。

一个非常重要的用例是在**交换变量**。假设我们想在数据库中搜索存储在两个日期之间的记录。我们可以编写一个简单的函数来接受两个日期对象:fromDate 和 toDate，如下所示:

 ``function fetchDatabaseRecords(fromDate, toDate) {
_// ...execute database query_
}`` 

 ``我们希望确保 fromDate 总是在 toDate 之前，因此我们希望在 fromDate 在 toDate 之后的情况下简单地交换日期。下面是我们如何使用数组析构来交换日期:

[https://medium . com/media/b1f1c 32667 BDD 1040 DBC 3a 54 e 8255527/href](https://medium.com/media/b1f1c32667bdd1040dbc3a54e8255527/href)

> *关于析构更详细的指南，可以看看* [*ES6 析构:完全指南*](https://codeburst.io/es6-destructuring-the-complete-guide-7f842d08b98f) *。*

### 5。班级

类是一些 JavaScript 开发人员长期以来一直想要的一个特性，尤其是那些以前有过其他面向对象编程语言经验的人。JavaScript ES6 语法增强最终包括了类。

尽管类现在是 JavaScript 的一部分，但它们的行为方式与其他经典编程语言不同。它们更像是以前模拟基于类的行为的方法的语法糖。因此，它们仍然基于 JavaScript 的原型继承工作。

在 ES6 之前，类是使用*构造函数*模拟的，实例方法基本上是通过增强构造函数的原型创建的。因此，当用 new 关键字调用构造函数时，它返回构造函数类型的一个实例，该实例可以访问其原型中的所有方法。这个的值也指向构造函数实例。

这里有一个例子:

[https://medium . com/media/ec2f 26 FB 48 df 966 c 0408 edfd 808014 a 9/href](https://medium.com/media/ec2f26fb48df966c0408edfd808014a9/href)

#### 类语法

类和函数在很多方面都很相似。就像函数一样，可以使用 class 关键字使用*类声明*和*类表达式*来定义类。

> 与函数一样，*类是第一手公民*，可以作为值在程序中传递。

然而，类和函数之间有一些显著的不同。

1.  类声明没有被提升，其行为类似于 let 声明。

2.  必须始终用 new 调用类构造函数，而不能用 new 调用类方法。

3.  类定义代码总是处于*严格模式*。

4.  所有的类方法都是不可枚举的。

5.  不能从类内部修改类名。

下面是我们之前使用类语法重写的矩形类型:

[https://medium . com/media/ca 81 ad 90062 A8 F2 FDD 92 C5 e 6970 c7bb 8/href](https://medium.com/media/ca81ad90062a8f2fdd92c5e6970c7bb8/href)

这里，我们使用一个特殊的 constructor()方法来定义类构造函数逻辑，并设置所有的实例属性。事实上，每当 typeof 操作符被用在一个类上时，它都返回“function”——不管是否为该类显式定义了构造函数。

还要注意，computeArea()实例方法实际上是添加到底层类构造函数的 prototype 对象中的。这就是为什么在 rectangle . prototype . computearea 上使用 typeof 运算符也会返回“function”的原因。

基于这些相似性，您可以得出这样的结论:类语法主要是在前面用于创建自定义类型的方法之上的语法糖。

让我们看另一个稍微复杂一点的例子，演示如何使用类表达式并将类作为参数传递给函数。

[https://medium . com/media/725d 94 F2 b 04 e 155d 739 baaaf 91 Fe 5 f 68/href](https://medium.com/media/725d94f2b04e155d739baaaf91fe5f68/href)

这里，我们首先创建了一个匿名的类表达式，并将其赋给了 Rectangle 变量。接下来，我们创建了一个函数，它接受一个形状类作为第一个参数，接受实例化该形状的维度作为其余参数。代码片段假设它接收的任何 Shape 类都实现了 computeArea()方法。

#### 扩展类

就像其他面向对象的编程语言一样，JavaScript 类具有类扩展的功能。因此，可以从*父*类创建具有修改功能的*派生*或*子*类。

假设我们有一个用于创建矩形的 Rectangle 类，我们想创建一个用于创建等长和等宽的矩形(正方形)的 Square 类。我们可以这样做:

[https://medium . com/media/2ba 486892928114 b 449357 c 57 e 2d 9d 32/href](https://medium.com/media/2ba486892928114b449357c57e2d9d32/href)

首先，注意 extends 关键字的使用，它表明我们想要从父类创建一个派生类。

> 派生类继承父类原型中的所有属性和方法，包括构造函数。

还要注意，我们使用一个超级引用从派生类的构造函数中调用父类的构造函数。当您想要增强派生类中继承方法的功能时，这非常有用。

例如，从 Square 类中调用 super.computeArea()将调用 Rectangle 类中实现的 computeArea()方法。

> 对 super()的调用必须在每个派生类的构造函数中进行，并且必须在对它进行任何引用之前进行。

这是因为调用 super()设置了这个的值。然而，super()不应该在不是派生类的类中使用，因为它被认为是一个语法错误。

创建派生类不仅限于扩展类。派生类通常是通过扩展任何可以用作构造函数并且具有原型的 JavaScript 表达式来创建的——比如 JavaScript 函数。因此，以下是可能的:

[https://medium . com/media/9 dbcf 480d 95169 F5 b 6 C3 D5 d 28 fa 4 da 9 c/href](https://medium.com/media/9dbcf480d95169f5b6c3d5d28fa4da9c/href)

#### 静态类成员

到目前为止，我们一直在看*实例方法*和*属性*。有时候你需要直接应用于类的*静态方法*或*属性*，并且不会从一个实例改变到另一个实例。在 ES6 之前，静态成员可以按如下方式添加:

[https://medium . com/media/916 b 6 F3 f 45455d 75 db 9 c 1983 fa 6 deda 4/href](https://medium.com/media/916b6f3f45455d75db9c1983fa6deda4/href)

对于 ES6 类，static 关键字放在方法名之前，表示该方法是一个*静态方法*。然而，*静态属性*不能从类内部创建。下面是我们如何创建静态成员:

[https://medium . com/media/98b 950 e 57 aa 1071 a 196 ff 37 ce D3 f 0a 9 e/href](https://medium.com/media/98b950e57aa1071a196ff37ced3f0a9e/href)

> 静态类成员也由派生类继承。它们可以被派生类重写，就像实例方法和属性一样。

这里有一个简单的例子:

[https://medium . com/media/06b 92 f 581 eafcb 88 ddfd 5423 b 2594136/href](https://medium.com/media/06b92f581eafcb88ddfd5423b2594136/href)

#### 更多特性

还有几个值得考虑的类特性，其中之一是 ***访问器属性*** 。在需要在类原型上有属性的情况下，它们会非常有用。

这里有一个简单的例子:

[https://medium . com/media/3757873 BF 0962925 f 30 AEA 9 ff 49 be 86/href](https://medium.com/media/3757873bf0962925f30aea9fff49be86/href)

类的另一个非常类似于对象文字的好特性是能够为类成员使用 ***计算名称*** 。这些计算出的名称也可以用于访问者属性。

> 计算出的名称通常是包含在一对*方括号* ([])之间的 JavaScript 表达式。

这里有一个简单的例子:

[https://medium . com/media/87330 AE 82 b 74198046 BFA 76 C3 ebb 2d 91/href](https://medium.com/media/87330ae82b74198046bfa76c3ebb2d91/href)

### 结论

尽管这是一篇很长的文章，但我坚信我们大多数人都已经学会了一些使用 JavaScript ES6 新特性来改进代码的方法。

在编写改进的代码时，还应该考虑其他超越 ES6 的特性，如 *ES6 模块*、*承诺*、*异步函数*、*生成器*等。

#### 拍拍&跟着

如果你觉得这篇文章很有见地，如果你不介意的话，请随意鼓掌。

你也可以在 Medium ( [Glad Chinda](https://medium.com/u/ddcd0e9719e5) )上关注我，获取更多对你有帮助的有见地的文章。你也可以在推特上关注我( [@gladchinda](https://twitter.com/@gladchinda) )。

***享受编码……***

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

