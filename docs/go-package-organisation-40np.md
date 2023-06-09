# 去包装组织

> 原文：<https://dev.to/dnnrly/go-package-organisation-40np>

# 走包组织

> 这更多的是我的笔记、资源列表和一些附带的讨论。

## 包命名

[这里的](https://golang.org/doc/code.html#PackageNames)是一些关于软件包名称的“官方”文档。简而言之就是`main`函数需要在`package main`中。包名不需要唯一，但是完整的导入路径需要唯一。

一些包命名约定可以在[这里](https://talks.golang.org/2014/organizeio.slide#6)找到。

[这个](https://dave.cheney.net/2014/12/01/five-suggestions-for-setting-up-a-go-project)说的是关于包的组织，但是它包括包的命名，以及名字和位置对包的影响。短版；小写，不用麻烦在包类型中重复包名。但是值得一读这篇文章以及戴夫·切尼写的其他文章。

## 包装布局

关于放下包裹，有 3 件有趣的事情值得一读:

*   [标准 Go 项目布局(Github)](https://github.com/golang-standards/project-layout)
*   [标准封装布局](https://github.com/golang-standards/project-layout)

这两页是将哪种代码放在哪个包中的有趣背景。简短版:

*   放置可执行的命令(即与您的`main`功能)一起放在`cmd`下的子包中
*   别忘了把你导入的 Go 包放在`vendor`下。这是推荐的管理依赖性的惯用方法，也是互联网上许多讨论的主题
*   `examples`用代码示例告诉人们如何使用你的代码

还有很多其他的小宝石，但这是它的症结所在。

我疯狂搜索的最喜欢的结果是这个。在这里，我们有 5 个具体的规则，围绕着它有很好的推理。对我来说，重要的几点是:

1.  根包应该包含服务的域对象和接口
2.  根据依赖关系组织子包，包括`net/http`这样的包

同样，建议您将`main`包放在`cmd`下的子包中。但是这一次，建议您在这里进行依赖注入。

本文没有涉及的一点是业务逻辑位于何处。页面上的例子在一个`postgres`子包和根包之间展开。我的直觉是，需要对你的依赖相关的子包和根包中的内容进行一定的解释。

## 真实例子

### 水牛

[水牛城](https://gobuffalo.io)恰好是我目前最喜欢的围棋项目(当我[介绍 Gostars](https://dev.to/dnnrly/gostars-first-steps-13l9-temp-slug-7283914) 的时候，你可能已经注意到了)。问题是，水牛城根本不会接近这个组织。领域模型位于`models`下，处理程序和业务逻辑位于`app`下。在那里也有放置 HTML 模板和静态资产的目录。

这种布局可能是因为这个库继承了 Ruby On Rail 的传统。这样做的一个副作用是，您将遇到上面文章中讨论的几个问题。请注意，但请记住，Buffalo 提供了一种非常强大的方法来编写您的 web 应用程序。

### 果阿

[Goa](https://goa.design/) 是一个不同的网络框架。同样，它遵循自己的组织。`app`包含处理程序以及请求和响应对象，`controllers`包含具有业务逻辑的实际控制者。

### Kubernetes

这似乎遵循了上面的 Gihub 项目的例子。