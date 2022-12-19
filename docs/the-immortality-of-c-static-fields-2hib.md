# C#静态字段的不朽性

> 原文：<https://dev.to/collinbarrett/the-immortality-of-c-static-fields-2hib>

[![Static Hair](img/50e740180413b401911da56aec26c511.png)T2】](https://collinmbarrett.com/immortality-static-fields/)

在过去的两周里，我的团队两次尝试使用静态场。第一个是由你的 truly 无意中引入的，并在我们的最新版本中造成了一点混乱。后者正在争论是否要利用一个来执行数据的内存缓存。在这两种情况下，而且可以说在几乎所有可能的情况下，使用静态(非常数)字段可能都不是一个好的选择。我澄清了非常量，因为 [`const`本质上也是静态的](https://stackoverflow.com/questions/408192/why-cant-i-have-public-static-const-string-s-stuff-in-my-class/408201#408201)，但是使用起来通常更安全，因为它是编译时常量，因此被烧录到构建工件中，而不是设置在内存中。

## 案例一:减少重复

作为我们上一个版本中错误修复的一部分，我将一些业务逻辑从视图层移回到一个新的服务类中。这个类`new` -ed 了五个不同的存储库，每个都使用同一个`DataContext`实例。(五个存储库对于一个类来说可能太多了，我可以将服务模块化更多。然而，重构是迭代的。)服务类有两个不同的构造函数，所以我没有在两个构造函数中对上下文和每个库进行`new`，而是尝试在声明时对它们进行实例化。

主要是，我想替换这个:

```
public class MyServiceClass{private readonly DataContext \_context;private readonly Lazy&lt;MyRepo1&gt; \_myRepo1;private readonly Lazy&lt;MyRepo2&gt; \_myRepo2;private readonly Lazy&lt;MyRepo3&gt; \_myRepo3;private readonly Lazy&lt;MyRepo4&gt; \_myRepo4;private readonly Lazy&lt;MyRepo5&gt; \_myRepo5;private readonly MyParamClass \_myParamClass;public MyServiceClass(){\_context = new DataContext();\_myRepo1 = new Lazy&lt;MyRepo1&gt;(() =&gt; new MyRepo1(context));\_myRepo2 = new Lazy&lt;MyRepo2&gt;(() =&gt; new MyRepo2(context));\_myRepo3 = new Lazy&lt;MyRepo3&gt;(() =&gt; new MyRepo3(context));\_myRepo4 = new Lazy&lt;MyRepo4&gt;(() =&gt; new MyRepo4(context));\_myRepo5 = new Lazy&lt;MyRepo5&gt;(() =&gt; new MyRepo5(context));}public MyServiceClass(MyParamClass myParamClass){\_myParamClass = myParamClass;\_context = new DataContext();\_myRepo1 = new Lazy&lt;MyRepo1&gt;(() =&gt; new MyRepo1(context));\_myRepo2 = new Lazy&lt;MyRepo2&gt;(() =&gt; new MyRepo2(context));\_myRepo3 = new Lazy&lt;MyRepo3&gt;(() =&gt; new MyRepo3(context));\_myRepo4 = new Lazy&lt;MyRepo4&gt;(() =&gt; new MyRepo4(context));\_myRepo5 = new Lazy&lt;MyRepo5&gt;(() =&gt; new MyRepo5(context));}} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个:

```
public class MyServiceClass{private readonly DataContext \_context = new DataContext();private readonly Lazy&lt;MyRepo1&gt; \_myRepo1 = new Lazy&lt;MyRepo1&gt;(() =&gt; new MyRepo1(context));private readonly Lazy&lt;MyRepo2&gt; \_myRepo2 = new Lazy&lt;MyRepo2&gt;(() =&gt; new MyRepo2(context));private readonly Lazy&lt;MyRepo3&gt; \_myRepo3 = new Lazy&lt;MyRepo3&gt;(() =&gt; new MyRepo3(context));private readonly Lazy&lt;MyRepo4&gt; \_myRepo4 = new Lazy&lt;MyRepo4&gt;(() =&gt; new MyRepo4(context));private readonly Lazy&lt;MyRepo5&gt; \_myRepo5 = new Lazy&lt;MyRepo5&gt;(() =&gt; new MyRepo5(context));private readonly MyParamClass \_myParamClass;public MyServiceClass(){}public MyServiceClass(MyParamClass myParamClass){\_myParamClass = myParamClass;}} 
```

Enter fullscreen mode Exit fullscreen mode

然而，在声明时初始化存储库会在`context`参数上抛出一个编译器错误。

> 无法在静态上下文中访问非静态字段“context”。

我走得有点太快了，这看起来像是一个简单的解决办法。只需在`DataContext`声明上填充一个`static`关键字，看着红色的曲线逐渐消失，就可以称之为完成。

```
public class MyServiceClass{private static readonly DataContext context = new DataContext();...} 
```

Enter fullscreen mode Exit fullscreen mode

单个`static`关键字引起的症状花费了我们团队一周或更多的人时来解决。由于我们的应用程序运行在多租户环境中，第一个实例化服务类的客户机将它们的`DataContext`加载到静态字段中。当另一个租户的用户发出下一个请求时，服务类的一个新实例被创建，但是`context`的静态值仍然存在。

在不知道原因的情况下，您可以理解为什么这个问题看起来不仅是间歇性的，而且在我们的开发机器上无法重现，因为我们没有在多租户环境中开发。如果环境只需要一个单一的值`DataContext`，谁会在乎`DataContext`是否是静态的？

值得庆幸的是，服务类在尝试写回数据库之前，在存储库检索中抛出了各种错误，否则这个问题的代价可能会高得多。

**更新 7.18.18** : [开发者社区成员 Asti](https://dev.to/asti) 指出，我也可以将我的第二个构造函数改为如下形式，以减少重复:

```
public MyServiceClass(MyParamClass myParamClass) : this(){\_myParamClass = myParamClass;} 
```

Enter fullscreen mode Exit fullscreen mode

## 案例二:跨请求缓存

我们的应用程序使用 [Telerik Reporting](https://www.telerik.com/products/reporting.aspx) 为我们的用户生成各种报告。这个框架的架构是这样的，报表查看器加载客户端，然后为它需要显示的每个报表回调服务器。

一位同事正在对同一查看器实例中显示的一组三个报告执行升级。数据的一个子集在三个报告之间共享，因此他建议使用一个静态字段在内存中缓存数据，这样对每个后续报告的请求就不需要再次从数据库中检索数据。

了解了我们最近在“案例 1”中所做的事情，我们不得不出于多种原因快速地将它击落。

*   功能性:我们目前正在努力改造我们的应用程序，使其能够实现负载平衡。静态字段只保存在单个节点的内存中，这在负载平衡的情况下会失败。
*   安全性:如果多个用户试图同时运行同一个报告，可能会导致冲突甚至安全漏洞(查看另一个客户的数据)。
*   性能:它将永久地为缓存的数据集使用大量内存，而我们只是以一种短暂的方式(不超过几秒钟)需要这些内存。

## 使用静态字段的后果

*   它永久地分配内存，仅供该字段使用。
*   它的值永远存在，超过其包含类的任何给定实例的生存期。
*   在负载平衡的环境中，它不能跨节点访问。
*   它在多租户环境的 AppDomain 中的所有租户之间持续存在。

## TL；速度三角形定位法(dead reckoning)

使用非常数的静态字段应该是最后的选择，并且应该充分理解使用它们的后果。

*<small>免责声明:所表达的观点仅是我个人的观点，不代表我的雇主的观点或意见。</small>T3】*