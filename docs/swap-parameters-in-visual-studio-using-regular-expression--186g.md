# 使用正则表达式在 Visual Studio 中交换参数

> 原文：<https://dev.to/vr_nithin/swap-parameters-in-visual-studio-using-regular-expression--186g>

这个问题在我发送审查请求时多次出现，例如当我使用 Assert 时。AreEqual()方法和参数没有按顺序(预期值，实际值)交换。纠正所有的断言是一项既无聊又费时的工作。AreEqual()参数顺序。在网上搜索时发现了使用正则表达式交换参数的简单方法。

```
// One of the common place to swap parmeter
public static void AreEqual(object expected, object actual) 
```

Enter fullscreen mode Exit fullscreen mode

例如，我写了这样的代码:

```
Assert.AreEqual("ActualString1", CretedString1);  
Assert.AreEqual("ActualString2", CretedString2);  
Assert.AreEqual("ActualString3", CretedString3); 
```

Enter fullscreen mode Exit fullscreen mode

参数是颠倒的顺序，这是不正确的，当然我会得到评论来改变这一点。

为了交换这个参数，我们可以用正则表达式来查找和替换。

> 注意:Visual Studio 中的正则表达式有点不同。

*将此添加为搜索词*

```
\((".*"),([^\)]*) 
```

Enter fullscreen mode Exit fullscreen mode

*添加此条款作为替换条款*

```
($2, $1 
```

Enter fullscreen mode Exit fullscreen mode

## 其工作原理

[![Alt text of image](img/72272a348226c2fc001bbc242961d934.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oup-sGb9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://vrnithinkumar.github.io/2017/08/19/vs-regex/RegEx.png)

有关替换模式中使用的正则表达式的信息，参见正则表达式中的[替换。要使用带编号的捕获组，语法是$1 指定带编号的组，语法是(x)指定有问题的组。例如，分组正则表达式(\d)([a-z])在以下字符串中找到四个匹配项:1a 2b 3c 4d。替换字符串 z$1 将该字符串转换为 z1 z2 z3 z4。](https://msdn.microsoft.com/en-us/library/ewy2t5e0.aspx)

**示例截图**
*前:
[![alt text](img/cae2a3b0a41a68f1bbff955648d0e3ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--83XVt2Tx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://vrnithinkumar.github.io/2017/08/19/vs-regex/before.png) 
*后:*
[![alt text](img/0ed4b54967eb6d7945523f69aa898503.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--audYvQLq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://vrnithinkumar.github.io/2017/08/19/vs-regex/after.png)*

 *> 1.  Be careful to choose the part of the code where you want to exchange parameters. Do not apply for the whole document or solution, which may have some adverse effects.
> 2.  You can adjust regular expressions for other use cases with different parameter patterns.
> 3.  There are some shortcuts to rearrange parameters in VS, but they don't work for me. Even if it does, we need to choose each equivalent method and apply these shortcuts.

**更引用:**
[断言。AreEqual 方法](https://msdn.microsoft.com/en-us/library/ms243413.aspx)
[在 Visual Studio 中使用正则表达式](https://msdn.microsoft.com/en-us/library/2k3te2cs.aspx)
[在 visual studio IDE 中有没有交换/重新排序参数的快捷方式？-堆栈溢出](http://stackoverflow.com/questions/3292292/is-there-a-shortcut-to-swap-reorder-parameters-in-visual-studio-ide)*