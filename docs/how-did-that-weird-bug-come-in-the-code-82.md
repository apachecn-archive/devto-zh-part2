# 代码中那个奇怪的 bug 是怎么来的

> 原文：<https://dev.to/adityasridhar/how-did-that-weird-bug-come-in-the-code-82>

有没有花一整个星期去找出窃听器在哪里？

想知道为什么在开发阶段没有发现这个错误吗？

那么这篇文章肯定会对你有用😃

这篇文章将解释如何找到代码中错误的来源，以及编写代码的最佳实践😃

# 哪种类型的 bug 很难找到？

假设代码有 100000 行代码。

现在代码在运行时不会抛出任何错误。这很好😃。没有人喜欢错误，对吗？

现在，您的一位客户联系您的开发团队，说他们无法在您的应用程序中执行某些操作。

现在你需要找出为什么代码会这样做。但是正如我已经提到的，代码不会抛出任何错误。

现在的问题是，你如何在 100000 行代码中找出问题所在😕

一个错误现在看起来没那么糟糕，因为它至少给了你一些关于什么可能出错的信息😃

现在，你如何找到这个 bug？

调试拯救👍

# 排除故障

[![Debugging](img/3a22a9da5fbfb2f875f8537ba126d199.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FUO4hU1w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ao3bb4v6d5svcoc6zmw1.jpg)

<figcaption>“selective focus photography of woman holding clear glass ball” by [Anika Huizinga](https://unsplash.com/@iam_anih) on [Unsplash](https://unsplash.com/)</figcaption>

## 什么是调试？

嗯，顾名思义，它是去窃听。调试是一个过程，在这个过程中，你检查代码，找出错误在哪里。

## 你用什么工具调试？

你猜对了。这是一个调试器😃

根据代码使用的语言，您首先需要选择正确的调试器工具。如果您使用的是 Eclipse，它会自动附带 java 调试器。如果您正在使用 javascript，您可以使用任何 web 浏览器附带的调试器等等。

## 你在调试的时候到底在做什么？

使用调试器，您可以在代码中设置检查点，然后在调试模式下运行代码。

假设您在代码的第 10 行设置了一个检查点。现在，当您运行代码时，代码将停止运行并暂停在第 10 行。

现在，在这种状态下，您可以检查代码中的变量，看看是否有任何异常。您可以检查变量包含哪些值。可以验证数组或对象的内容是否合适等等。

如果任何变量有一个奇怪的值，那么你有一个可能的嫌疑人😃。使用这些信息，您可以在该变量出现的任何地方设置检查点，并不断重复这个过程，直到找到错误的真正来源😃

## 调试似乎很容易，问题是什么？

问题是你有 100000 行代码。最初的检查点在哪里？

可能这些代码是由多个开发人员多年来编写的，没有一个人知道整个代码库。那么，您如何知道将初始检查点放在哪里呢？

事实是这样的

*为了容易地调试代码，代码必须以这样一种方式编写，即首先它是可调试的。*

为了调试代码，您需要在很高的层次上理解代码的各个部分在做什么。

但是为了理解代码，在编写代码时必须牢记一些最佳实践。我将在这里提到一些最佳实践。

# 使代码模块化

[![Modular](img/58bed8e0e9dcc2454a453815735a6650.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HuvACRbL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/djksjl8o37tm278a2omu.jpg)

<figcaption>“four person holding assorted-color jigsaw puzzles inside room” by [rawpixel](https://unsplash.com/@rawpixel) on [Unsplash](https://unsplash.com/)</figcaption>

简单是最复杂的——达芬奇

想象一下，有一个包含全部 100000 行代码的文件。阅读这样的代码是不可能的。

相反，将代码分成多个模块是一个很好的实践，这样每个模块完成一个特定的任务。

这个想法也可以扩展。首先，应用程序可以分成许多更大的模块，每个更大的模块又可以分成许多更小的模块。

例如，假设你正在建立一个电子商务网站。该应用程序可以分为如下更大的模块。

1.  登录/注册页面
2.  主页
3.  购物车
4.  搜索选项
5.  推荐选项等等

这些是更大的模块，因为它们执行大任务。这可以分成许多更小的模块

例如，注册页面可以分为

1.  用于读取用户输入的模块
2.  用于验证用户输入的模块
3.  用于检查用户名是否已经存在于系统中的模块
4.  一个模块来检查密码是否强等等。

以这种方式划分代码使其可读性更强，并有助于使代码更易于调试。

# 正确的命名约定

[![Naming Conventions](img/fa565bb4128cdfb4de30ed269b35a37f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZXoAFd_I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tgu8l6ugvad818jpsfik.jpg)

<figcaption>“signage on post during daytime” by [Adi Goldstein](https://unsplash.com/@adigold1) on [Unsplash](https://unsplash.com/)</figcaption>

让我们以下面的代码为例

```
function abcd(c) {
    //Some main logic here
    return z;
} 
```

我们不知道上面的代码试图做什么，因为它没有一个合适的命名约定。让我们重写代码

```
function validateUsername(username){
    //Some main logic here
    return isValid;

} 
```

这段代码比前一段代码更有意义。这段代码试图验证用户名。

拥有适当的命名约定使得代码更容易阅读。反过来，这使得调试代码变得更加容易。

# 证明文件

[![Documentation](img/0b6699b3edacf55e4ea5435a7308e436.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fEebVsHN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ikxfng6icjj2e88m2j8.jpg)

<figcaption>“person typing on brown typewriter” by [rawpixel](https://unsplash.com/@rawpixel) on [Unsplash](https://unsplash.com/)</figcaption>

所以你已经写完了你的代码，一切都正常了。伟大的😃

现在是写文档的时候了😕

我知道，我知道。您可能会想“嘿，代码正在工作，为什么要记录它”。文档是确保其他人能够理解你写的代码的东西。

事实上，如果你在 6 个月后看你自己的代码，如果没有正确的文档，你将没有任何线索😃

考虑下面的代码。

```
function cleanData(data){
    //cleaning logic
    return cleanData;

} 
```

在上面的代码中，命名约定是好的。但是上面的代码想要清理的是什么呢？。

```
/**
* Function to clean input data
* 
* 1\. If any of the rows have null, 
*    replace with 0
* 2\. Ensure that 'id' value of a row 
*    is not null. If it is, then 
*    skip row
*
* @param {Object} data  : Input Data.
* @return {Object} : Returns an object 
*                    which contains clean 
*                    data.
* 
*/
function cleanData(data){
    //cleaning logic
    return cleanData;
} 
```

上面的代码有文档。现在已经有点清楚 cleanData 函数在做什么了(这个文档可以做得更好)。您可能会觉得这里的文档比代码本身还要大😃。对于较小的函数，可以使用简单形式的文档。但是对于更大的函数，需要一个合适的文档。

我知道写文档是额外的努力。但是从长远来看，你会喜欢文档的😃

文档有助于调试，因为它有助于理解一段代码做什么，而无需深入研究代码。

# 单元测试

[![Unit Tests](img/e17e73a18d653e4ad55d44808e02f9d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lBayxc5z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c749gveawe9gi2qji2yb.jpg)

<figcaption>“electronic circuit boards near tester” by [Nicolas Thomas](https://unsplash.com/@nicolasthomas) on [Unsplash](https://unsplash.com/)</figcaption>

例如，考虑下面的代码。

```
function sum(num1, num2){
    return num1+num2;
} 
```

这个函数计算两个数的和，并且运行良好。

假设有人错误地将上面的代码更改为下面的代码。

```
function sum(num1, num2){
    return num1*num2;
} 
```

现在代码是错误的，因为它返回的是`num1*num2`而不是`num1+num2`。

单元测试自动捕捉这样的问题，而不需要有人手动检查代码。

因此，单元测试是一段代码，它将通过为 num1 和 num2 给出不同的值来测试 sum 函数，并查看是否得到了正确的输出。

单元测试确保这些小问题在开发阶段就被发现。如果在开发过程中没有发现这些问题，那么它们会堆积起来，在生产中产生一个重大的 bug。所以写单元测试总是更好。😃

## 希望这篇文章有用。从长远来看，编码最佳实践肯定会有很大帮助，而且它们肯定会使调试更容易。😃

### 请随时在 [LinkedIn](https://www.linkedin.com/in/aditya1811/) 与我联系，或者在 [twitter](https://twitter.com/adityasridhar18) 关注我。

### 如果你喜欢这篇文章，你可以去我的网站[https://adityasridhar.com](https://adityasridhar.com)看看其他类似的文章