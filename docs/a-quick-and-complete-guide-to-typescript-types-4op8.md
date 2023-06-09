# 打字稿类型的快速完整指南

> 原文：<https://dev.to/bnevilleoneill/a-quick-and-complete-guide-to-typescript-types-4op8>

[![](img/be9cd2085eee4088024a6a86124d2b56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jb8EEYVT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkAvyOmNO4q1PAa-qEyrc5g.jpeg) 

<figcaption>照片由[杯先生/杨奇煜·巴拉](https://unsplash.com/photos/YmsmIv1IMRU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/type?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

TypeScript 是 JavaScript 的类型化超集，它编译成普通 JavaScript。TypeScript 是开源的，由微软开发和维护。TypeScript 可用于开发客户端和服务器端执行的 JavaScript 应用程序。

TypeScript 是一种静态类型语言。如果变量的类型在编译时已知，那么语言就是静态类型的。对于一些语言，这意味着你作为程序员必须指定每个变量的类型(例如:Java，C，c++)；其他语言提供了某种形式的*类型推断*，即类型系统推断变量类型的能力。

声明类型可以防止许多运行时错误，并允许 ide 施展魔法，向您展示错误所在。如果您来自 Java 这样的类型化语言背景，您会习惯于看到这样的例子:

[https://medium . com/media/f04e 0490 D1 C4 db 6233 BF 78 Abd 53 FBD 63/href](https://medium.com/media/f04e0490d1c4db6233bf78abd53fbd63/href)

在上面的例子中，如果一个字符串被添加到数组中，编译器将抛出一个编译错误。这就是 TypeScript 给 JavaScript 带来的，错误和类型检查。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### 使用类型

在 TypeScript 中，变量的类型在变量声明之前在右侧定义。如果我们想定义变量名的类型，它看起来会像下面的代码片段:

t1【https://medium . com/media/7 cedecfc 1 ea 76 D7 e 54d 1 de 91511 CD 01/href】

可以使用的类型有:

*   声明变量时
*   在函数参数中
*   对函数的返回值进行类型检查

### 变量声明

在 TypeScript 中声明变量时，我们使用 let 和 const 关键字。你可以输入校验数组，字符串，数字等。

像 JavaScript 一样，TypeScript 允许你处理数组值。数组类型可以用两种方式之一编写。在第一个示例中，使用元素类型后跟[]来表示该元素类型的数组:

[https://medium . com/media/4c 16687 C2 DBF 060 c 6 DBC 43 EC 3 e 8605 a5/href](https://medium.com/media/4c16687c2dbf060c6dbc43ec3e8605a5/href)

此外，我们可以使用通用数组类型 Array，其中 elementType 是数组中包含的元素的类型。一个例子是这样的:

[https://medium . com/media/30 A8 e 67 f 303 f 6 C3 b 04 daf 9 B1 c 8d 12 a4f/href](https://medium.com/media/30a8e67f303f6c3b04daf9b1c8d12a4f/href)

现在，如果您的数组将包含几个类型，元组开始发挥作用。

**元组**元组允许你声明一个数组，其中固定数量的元素的类型是已知的，但不需要相同。例如，您可能希望将一个值表示为一对字符串和一个数字:

[https://medium . com/media/70 d6b 7398 E4 B3 ee 10 D4 E5 e 4004 aa 316/href](https://medium.com/media/70d6b7398ee4b3ee10d4e5e4004aa316/href)

最后一个例子是一个多于两个字符的数组，这没有出错，因为我们提供了额外的元素，或者是字符串，或者是数字。如果将一个布尔值添加到数组中，将会抛出一个错误。

[https://medium . com/media/18769 EDF 4 e 59 e 38300386839 b 22 EDF C2/href](https://medium.com/media/18769edf4e59e38300386839b22edfc2/href)

**布尔值**布尔值是最基本的数据类型。这不是真的就是假的。

[https://medium . com/media/Fe 714 e 7 b 389 aa 6 c 3797091 C9 a5a 626 b 8/href](https://medium.com/media/fe714e7b389aa6c3797091c9a5a626b8/href)

TypeScript 中的字符串有三种用法:

*   双引号。
*   单引号。
*   模板文字。

双引号:

[https://medium . com/media/41 b 25 f 65335 f 9 a 16 DC 0930 ab 1b 63494 b/href](https://medium.com/media/41b25f65335f9a16dc0930ab1b63494b/href)

单引号:

[https://medium . com/media/9639 f 4237 EC 9 a2 CDC 2 b 5256 ca 9 EC 8 c 47/href](https://medium.com/media/9639f4237ec9a2cdc2b5256ca9ec8c47/href)

[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals):这些是允许嵌入表达式的字符串文字。您可以使用多行字符串和字符串插值功能。在 ES2015 规范的早期版本中，它们被称为“模板字符串”。这些字符串用反斜线/反引号(
)括起来

```
 ) character, and embedded expressions are of the form ${ expr }.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/b3eacea0b81994f911b9b778d6322b49/href">https://medium.com/media/b3eacea0b81994f911b9b778d6322b49/href</a></iframe>

**Number** In JavaScript, all numbers have the definitive type of number. All JavaScript numbers are floating point values, it is the same with TypeScript. TypeScript also supports binary and octal literals alongside hexadecimal and decimal values.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/d042fef7a78711e7f43b99a1d1dccfa9/href">https://medium.com/media/d042fef7a78711e7f43b99a1d1dccfa9/href</a></iframe>

**Enum** An enum is a friendly way of naming sets of numeric values. Enums begin numbering from 0 but you can manually set the value of one of the members.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/a330f533ce63cd635330af8a4ca45444/href">https://medium.com/media/a330f533ce63cd635330af8a4ca45444/href</a></iframe>

Or we can manually set the values of the enum:

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/5b9c0079b3b5379313dd7360dc58914a/href">https://medium.com/media/5b9c0079b3b5379313dd7360dc58914a/href</a></iframe>

A handy feature of enums is that you can also go from a numeric value to the name of that value in the enum. For example, if we had the value 6 but weren’t sure what that mapped to in the Car enum above, we could look up the corresponding name.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/d7870d8e03e495ec6578f3e4a058c9c3/href">https://medium.com/media/d7870d8e03e495ec6578f3e4a058c9c3/href</a></iframe>

**Any** There are time where we may not know the types we are working with. That’s when we can use the any type. The any type lets us opt out of type checking.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/e59c8eae0424d018300e224d68f7b5ae/href">https://medium.com/media/e59c8eae0424d018300e224d68f7b5ae/href</a></iframe>

The any type is very flexible. Even more so than the JavaScript object. With the any type, you can continuously opt in and opt out of type checking in your code.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/94f392b81a96cb07ec5839a015bc679a/href">https://medium.com/media/94f392b81a96cb07ec5839a015bc679a/href</a></iframe>

**Void** Void is almost a direct opposite of any, it depicts the absence of a type. It is commonly used to define the return type of a function.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/c2f84494a4c10e3846bed43f83102e0d/href">https://medium.com/media/c2f84494a4c10e3846bed43f83102e0d/href</a></iframe>

When declaring variables, defining the variable type as void isn’t really useful as you can only set the variable to either undefined or null.

**Null and Undefined** Null and Undefined both have their respective types named after them. These types aren’t useful on their own because we can only assign Null and Undefined to a variable defined as a Null or Undefined type.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/b551bd8021732be551c919a0b82967f3/href">https://medium.com/media/b551bd8021732be551c919a0b82967f3/href</a></iframe>

Naturally, null and undefined are subtypes of any types. So you can assign null or undefined to number or string.

**Never** The never type represents the type of values that never occur. For instance, never is the return type for a function expression or an arrow function expression that always throws an exception or one that never returns; Variables also acquire the type never when narrowed by any type guards that can never be true.

The never type is a subtype of, and assignable to, every type; however, _no_ type is a subtype of, or assignable to, never (except never itself). Even any isn’t assignable to never. Some examples of functions returning never:

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/d0f6f7905e14f928569ff6c45a4da3ba/href">https://medium.com/media/d0f6f7905e14f928569ff6c45a4da3ba/href</a></iframe>

**Types on Functions** We can define types for function parameters and function return values. We did something similar above when we used void on a function that doesn’t any values.

<iframe src="" width="0" height="0" frameborder="0" scrolling="no"><a href="https://medium.com/media/c4f7f5437aeb3b780496b38ab06d2af6/href">https://medium.com/media/c4f7f5437aeb3b780496b38ab06d2af6/href</a></iframe>
### Conclusion

We had a quick overview of TypeScripts types. There are more advanced applications of types in TypeScript, like creating declaration files etc. You can read more about creating declaration files [here](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html).

### Plug: [LogRocket](http://logrocket.com), a DVR for web apps

[![](https://cdn-images-1.medium.com/max/1024/1*s_rMyo6NbrAsP-XtvBaXFg.png)](http://logrocket.com)

[LogRocket](https://logrocket.com) is a frontend logging tool that lets you replay problems as if they happened in your own browser. Instead of guessing why errors happen, or asking users for screenshots and log dumps, LogRocket lets you replay the session to quickly understand what went wrong. It works perfectly with any app, regardless of framework, and has plugins to log additional context from Redux, Vuex, and @ngrx/store.

In addition to logging Redux actions and state, LogRocket records console logs, JavaScript errors, stacktraces, network requests/responses with headers + bodies, browser metadata, and custom logs. It also instruments the DOM to record the HTML and CSS on the page, recreating pixel-perfect videos of even the most complex single page apps.

Try it for free.

* * * 
```