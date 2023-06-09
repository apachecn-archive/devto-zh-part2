# 微软奥尔良—代码生成问题？

> 原文：<https://dev.to/kritner/microsoft-orleanscode-generation-issue-20n1>

### 微软奥尔良—代码生成问题？

在处理帖子“[Microsoft Orleans—Reporting Dashboard](https://medium.com/@kritner/microsoft-orleans-reporting-dashboard-16465d255199?source=friends_link&sk=42db313c0775bef7cd9d8a55865bf033)”时，我遇到了一个问题，代码生成似乎停止了“生成”。

下面是代码生成工作的时间(在前面提到的帖子开始之前)和它在撰写帖子时停止工作的时间的比较:

[krit ner-Blogs/OrleansGettingStarted](https://github.com/Kritner-Blogs/OrleansGettingStarted/compare/2ebd478cba2f5a6a6c679b8b6e6c56caf5518f11...fe51806c602273afcd29ae3b126e9b964ad21659)

正如您所看到的，两者之间没有太大的变化，但是不管出于什么原因，SiloHost 不再能够实例化我的粒度的实例，或者至少客户端说 SiloHost 不能。

当问题不存在时(如在[提交](https://github.com/kritner-blogs/OrleansGettingStarted/commits/2ebd478cba2f5a6a6c679b8b6e6c56caf5518f11)中)，运行应用程序看起来像:

[![](img/9d67e3422b4a3e5a1c8c11052bce2d08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5nmM0f74--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ATLWQpPccjkhIX_kZ.png)

当它存在时，(像在[提交](https://github.com/kritner-blogs/OrleansGettingStarted/commits/fe51806c602273afcd29ae3b126e9b964ad21659)中一样)，它看起来像:

[![](img/37c6e0996ab9aca08926113168a30f0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t4i0lsmd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AOtkzzvNwD8ywyiVw.png)

我已经打开了一个 [GitHub 问题](https://github.com/dotnet/orleans/issues/5154)，试图澄清我所经历的事情；但在此期间，有一个变通办法。

### 变通办法

我的印象是包含微软。orleanscodegenerator . build nu get 包将自动运行代码生成，尽管我在上面提交的 github 问题中似乎并不总是如此。

为了避免代码生成不触发，我采取了几个不同的步骤。(注意，我猜其中的一些步骤可以省略，但我主要是尝试暴力破解，一旦成功就不管了):

*   利用。ClientBuilder 和 SiloBuilder 上的 ConfigureApplicationParts 方法。
*   让 grain 接口实现一个空接口，以便在 ClientBuilder 中注册。
*   让 grain 实现实现一个空接口，用于在 SiloBuilder 中注册。

#### 晶粒/晶粒界面变化

我用于颗粒界面的界面是在 Kritner 下创建的。OrleansGettingStarted。粒度接口:

```
public interface IGrainInterfaceMarker { } 
```

grain 实现接口是在 Kritner 下创建的。OrleansGettingStarted .谷物:

```
public interface IGrainMarker { } 
```

我拥有的 grain 接口——IHelloWorld 和 IVisitTracker 进行了更新，以额外实现 IGrainInterfaceMarker。对于 grain 实现也是如此，但是实现 IGrainMarker。

#### ClientBuilder 和 SiloHostBuilder 更新

既然我们的粒度和接口都实现了各自新的接口标记，我们只需要在 ClientBuilder 和 SiloHostBuilder 上注册这些接口类型，如下所示:

客户端构建器:

```
.ConfigureApplicationParts(parts => parts.AddApplicationPart(typeof(IGrainInterfaceMarker).Assembly).WithReferences()) 
```

SiloHostBuilder:

```
.ConfigureApplicationParts(parts =>
{
 parts.AddApplicationPart(typeof(IGrainMarker).Assembly).WithReferences();
}) 
```

现在已经完成了这些更改，可以从客户端成功地在集群上调用这些颗粒了！万岁！

进行此更正的提交位于此处:[https://github . com/krit ner-Blogs/OrleansGettingStarted/pull/6/commits/9c 271 f 085d 22 a 66 F8 E8 d3c 3165 EDA 863 e 5269508](https://github.com/Kritner-Blogs/OrleansGettingStarted/pull/6/commits/9c271f085d22a66f8e8d3c3165eda863e5269508)

#### GitHub 问题

我在 GitHub 问题上收到了 Sergey 的回复:

问题:是什么触发了代码生成？第 5154 期点网/奥尔良

基本上把在奥尔良仪表板，有效地关闭了“自动扫描仪”，我正在利用以前。将名为的 Orleans Dashboard 添加到 AddApplicationPart 的行为，在幕后，反过来关闭了自动扫描。我猜这背后的原因是“开发者正在注册一些东西(即使它是奥尔良仪表板)，所以没有必要对要拾取的颗粒进行自动扫描。

相关:

*   [GitHub 问题](https://github.com/dotnet/orleans/issues/5154)
*   [微软奥尔良入门](https://medium.com/@kritner/getting-started-with-microsoft-orleans-882cdac4307f?source=friends_link&sk=1fc3451d71a19dcb49f2c8bbeb6b079e)
*   [微软奥尔良——再利用谷物和谷物状态](https://medium.com/@kritner/microsoft-orleans-reusing-grains-and-grain-state-136977facd42?source=friends_link&sk=f19cfa3f17665c3d700bfe0df56e27a9)
*   [更新新奥尔良项目，为新奥尔良范例做好更多准备！](https://medium.com/@kritner/updating-orleans-project-to-be-more-ready-for-new-orleans-examples-2105b29a46fd?source=friends_link&sk=61d1f591e5b5c498688439db50ad310e)
*   [微软奥尔良—报告仪表板](https://medium.com/@kritner/microsoft-orleans-reporting-dashboard-16465d255199?source=friends_link&sk=42db313c0775bef7cd9d8a55865bf033)
*   解决方法提交—[https://github . com/krit ner-Blogs/OrleansGettingStarted/pull/6/commits/9c 271 f 085d 22 a 66 F8 E8 d3c 3165 EDA 863 e 5269508](https://github.com/Kritner-Blogs/OrleansGettingStarted/pull/6/commits/9c271f085d22a66f8e8d3c3165eda863e5269508)