# 开发 RoR 应用程序的有用宝石。

> 原文：<https://dev.to/jean182/useful-gems-for-developing-ror-application-2on4>

在这篇文章中，我想谈谈一些我认为在开发 Rails 项目时有用的宝石，它们使开发变得更容易，这是 Ruby 的哲学，让程序员快乐和高效。

第一块宝石叫做[设计](https://github.com/plataformatec/devise)。这是在 Rails 上开发常规应用程序的必备工具，它是一个由 10 个模块组成的认证应用程序，每个模块提供不同的功能，但并非所有模块都是必需的，例如 [Omniauthable](https://www.rubydoc.info/github/plataformatec/devise/master/Devise/Models/Omniauthable) 模块用于使用第三方提供商进行认证，因此它不是一个可以选择不使用的必要模块。

对我来说，它让生活变得更容易，因为当使用它时，你很确定你会有一个非常可靠的身份验证逻辑，并且它会大大减少你的工作，因为你不再需要从头开始构建身份验证。

对于 API only 应用程序设计没有现成的 JWT 支持，所以你必须自己实现，或者尝试使用这个 [gem](https://github.com/waiting-for-dev/devise-jwt) 。

我想说的第二个 gem 是一个叫做 [annotate](https://github.com/ctran/annotate_models) 的 gem，这个 gem 的功能是在你的 ruby 类下显示一个总结当前模式的注释。

只需使用命令$ annotate，您就可以让您的模型、模型规格和工厂在开始时用当前模式进行注释，如下所示:

[![Annotated-model](../Images/9584465b791eae6e086f5febe2f13a28.png "Annotated-model")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3VcA4zqP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://loserkid.io/img/annotate.png)

正如您所看到的，这非常有用，因为当您创建模型逻辑时，有时您会忘记当前模型的列名，因此拥有该功能非常酷。当你不使用这个 gem 的时候，你通常会做的是在 db/schema.rb 下，这比在模型下有那些注释更耗时。

第三个 gem 叫做 [rails-erd](https://github.com/voormedia/rails-erd) ，它基于您当前的模型生成一个实体关系图。这真的很有用，因为有时当你有很多模型时，有时你会对关联感到困惑，即使你应该在开始编码之前创建一个 ERD，拥有一个生成 ERD 的 gem 可以帮助你看到你是否在实际程序中关联了错误的东西。

[![diagram-erd](../Images/b8bc3e20815159f0e05845e89ac784c4.png "Example of an ERD diagram generated with rails-erd")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NLmwYfKN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://loserkid.io/img/blogdomain.png)

我要提到的最后一个亮点是 [simplecov](https://github.com/colszowka/simplecov) ，它的主要功能是检查您在测试文件下测试了什么以及您遗漏了什么，这很好，因为有时您会忘记测试一些模型方法或忘记测试一个关联，这就是 simplecov 派上用场的时候，因为每次您运行 specs 时，您都会在 coverage 文件夹下看到新文件，其中包含 simplecov 生成的结果的 hmtl 视图。

[![simplecov-coverage](../Images/b3602ab323ffb798e56cae0639b9e849.png "Coverage example")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u8OJcha1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://loserkid.io/img/simplecov-coverage.png)

希望你会发现这些宝石很有用，我个人认为它们很棒，好的一点是它们中的大多数可以用于任何 rails 项目。

(这是我发布在 loserkid.io 博客上的一篇文章，你可以通过[点击这里](https://loserkid.io/useful-gems-for-developing-ror-application/)在线阅读。)