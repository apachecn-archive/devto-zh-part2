# 角形管道:隔离单元测试

> 原文：<https://dev.to/ahasall/angular-pipes-isolated-unit-testing-1027>

[![Angular Pipes: Isolated Unit Testing](img/8a285dab07392213a3ca2fc3fd1e348c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O-duBPkX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.amadousall.com/conteimg/2018/02/isolated-unit-tests-cover.png)

在 Angular 中，管道允许我们在显示数据之前对其进行转换。Angular 自带了一系列内置管道，但有时我们需要编写自己的自定义管道。那么，在这种情况下，我们也必须为他们编写单元测试。

管道是最简单的角度概念之一。这是一个用`@Pipe` decorator 注释的类，它实现了`PipeTransform`接口。具体来说，管道类有一个方法，即转换方法:

[![Angular Pipes: Isolated Unit Testing](img/c14d110c97d68221aea850c430736897.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bfu_zE-q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.amadousall.com/conteimg/2018/02/pipe-transform-interface.png)

因为管道很少与 DOM 元素交互，所以非常容易测试。从技术上讲，我们可以在不使用角度测试工具或测试床的情况下测试它们。但是最好为管道编写一些角度测试，因为当管道与角度组件结合使用时，我们将希望检测潜在的运行时错误。

因此，我们应该编写两种类型的单元测试:

*   独立测试(无试验台)
*   集成测试(带试验台)

在本文中，我们将学习如何为我们的管道编写独立的单元测试。

在[下一篇博文](https://www.amadousall.com/angular-pipes-integrated-unit-testing/)中，我们将看到如何使用`TestBed`即角度测试来测试管道。

当我们将管道作为一个独立的 TypeScript 类进行测试时，我们会谈到独立的单元测试。

## 管道的代码(mean.pipe.ts)

首先，让我们编写一个将数组转换为其平均值的管道。

[![Angular Pipes: Isolated Unit Testing](img/eca4de5cd9bbb8a6de1f981b9d10d51a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3V141Nky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.amadousall.com/conteimg/2018/02/mean.pipe.ts-1.png)

1.  实现`PipeTransform`接口。
2.  返回数字数组的平均值。

这个管道的完整源代码可以在[这里](https://gist.github.com/amadousalldotcom/9bcbbc5ee1745bf10c890a5c423bd477)找到。

## 隔离测试管道

[![Angular Pipes: Isolated Unit Testing](img/186ef84c483238d43949d65331079eac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rmFx83rP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.amadousall.com/conteimg/2018/02/mean.pipe.spec.ts-1.png)

1.  声明一个保存管道实例的变量。
2.  在每个测试运行之前，创建一个新的`MeanPipe`实例。
3.  检查管道是否正确实例化。

注意，我们不需要一个`BeforeEach`块，因为这个管道的转换方法是一个纯粹的无状态函数。

一旦我们知道我们的管道可以被正确实例化，我们就可以开始编写测试套件了。

[![Angular Pipes: Isolated Unit Testing](img/16d914d814bb5ef032a3948f1ff57654.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IoGRB_ao--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.amadousall.com/conteimg/2018/02/mean.pipe.spec.ts-2-2.png)

1.  “错误输入”测试套件会在意外值被传递到其转换方法时检查管道的行为。
2.  “计算”测试套件检查当正确的输入传递给管道时，管道是否正确地转换了数据。

这些测试套件的完整源代码可以在[这里](https://gist.github.com/amadousalldotcom/34ba2a29df55ff57646efd9a413d170b)找到

## 总结

在本文中，我们了解到有两种方法可以对角形管道进行单元测试:隔离测试和集成测试。但是我们把重点放在了隔离测试上，并且看到了为我们的管道编写隔离测试是多么容易。

在我的[下一篇博文](https://www.amadousall.com/angular-pipes-integrated-unit-testing/)中，我们将学习如何为我们的角形管道编写集成测试。

非常感谢您的阅读！