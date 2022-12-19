# 在中应用装饰器模式。使用 Autofac

> 原文：<https://dev.to/engberrg/apply-the-decorator-pattern-in-net-using-autofac-gl3>

[![](../Images/37be4d8c483946721b66ea7fe94af0e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5-6i416_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2ACl1FLSzewuybqI18Gliv8w.png) 

<figcaption>俄罗斯娃娃——比喻扩展给定功能的行为。图片借用自 [Maxpixel](http://maxpixel.freegreatpicture.com/Traditional-Russian-Toy-Russian-Doll-Russian-1090697) 。</figcaption>

我提倡在开发应用程序时遵循良好的设计原则和结构。

随着开发过程的进行，我们会在应用程序中添加新的功能:这可能是客户的功能请求，也可能是您建议的改进。无论如何，这些即将到来的变化也将改变已经给出的代码库。当引入变更时，我们希望尽可能简单。

在这篇文章中，我将向您展示一个例子，在您想要将功能扩展到一个现有特性的场景中，如何通过强大的 IoC 容器 [Autofac](https://autofac.org/) 利用装饰器模式。

假设您将要构建一个服务于产品的服务。我们将这个服务称为 **IProductRepository** ，为了简单起见，我们只使用一个名为 *GetById* 的方法。它可能看起来像这样，并连接了一个实现: