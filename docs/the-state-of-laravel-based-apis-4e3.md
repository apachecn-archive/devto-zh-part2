# 基于 Laravel 的 API 的状态——个人研究

> 原文：<https://dev.to/phprio/the-state-of-laravel-based-apis-4e3>

> **友好声明:**这是一个快速而肮脏的帖子，是我在研究 API 资源以构建一个我正在工作的项目的 API 时整理的，所以我很抱歉如此直接和中肯，以及级别和要点的数量:)
> 它是按照“这里只是为了完整性”到“可能是我们将要使用的”的顺序写的。

# TL；速度三角形定位法(dead reckoning)

*   GraphQL 应该是唯一的选择，如果你的前端需要它的话。需要在雄辩的模型和 GraphQL 相关代码之间进行大量的样板文件和手动同步。
*   JSON-API 是可能面对 GraphQL 的最受尊敬的标准，但是在 Laravel-land 上，它使您的文档更少。你可能需要为 API Blueprint 或者类似的东西编写单独的文档，这也可能会花费你一些时间。您还可以发现如何通过特定包(将 JSON-API 与 Dingo 连接起来)生成蓝图，但是...祝你好运。
*   手写 RESTful API...我需要对此发表评论吗？是手写的。对于中型或大型 API 来说，这当然不是一个好的选择。`EOF`。

# 开场白:我们的基本 API 需求

*   应该有内置的文档，不依赖于开发人员手工编写(想想在你的 IDE 中自动完成的 PHPDocs)
*   它需要非常快，因为它将用于对速度敏感的用户界面
    *   在这里，我想到了 GraphQL 或 JSON-API 的字段选择特性
    *   显然，一个简单的缓存层可能也会有所帮助

* * *

# 研究和比较

## API 类型选项 1: [GraphQL](https://graphql.org/learn/)

> **简短的结论。**构建起来似乎并不简单，因为需要编写大量的样板文件来重复来自雄辩的信息:GraphQL 类型、查询和变异。这种情况在两个包中都存在，而且它们都没有提供样板文件生成器。

### 有用的包

*   [由 Folkore 工作室制作](https://github.com/Folkloreatelier/laravel-graphql)
*   [根据民间传说改编的](https://github.com/rebing/graphql-laravel)

* * *

## API 类型选项 2: RESTful

### 子类型 1: [JSON-API](http://jsonapi.org/)

> 结论:使用起来也不是那么简单，但似乎比 GraphQL 更简单。然而，文档可能是一个弱点。

#### 有用的包

##### [Nil 的 Laravel5-json-api](https://github.com/nilportugues/laravel5-jsonapi)

*   实现了`include`、`fields`、`sort`、`page`
*   通用控制器几乎没有样板文件
    *   然而，你仍然需要[配置变形金刚](https://github.com/nilportugues/laravel5-jsonapi#step-3-definition)，尽管它们没有重复来自雄辩模型的太多信息
*   不支持文档生成。一点也不。

##### [laravel 5-JSON-API-dingo](https://github.com/nilportugues/laravel5-jsonapi-dingo)

###### 好点

理论上，将两个包的优点结合在一起，包括 Dingo 的特性(稍后将讨论 Dingo ),如 Auth、版本、速率限制、Docs 和 Routes 到 laravel5-json-api 中(从现在开始称为 L5JA)。

###### 坏点

*   [不清楚文档是如何生成的](https://github.com/nilportugues/laravel5-jsonapi-dingo/issues/8)，因为 Dingo 依赖于注释，而 L5JA 理想地基于通用控制器实现。可能还有其他集成问题，因为除了基本的路由示例之外，示例没有清楚地定义如何将两个包放在一起。
*   [背后似乎也缺少支持](https://github.com/nilportugues/laravel5-jsonapi-dingo/issues/7)。

##### [cloud creative 的 Laravel-json-api](http://laravel-json-api.readthedocs.io/en/latest/)

这个有很长的文档；虽然看起来很完整，但阅读所有内容并实现可能需要一些时间(我花了一些时间查看文档，看看有哪些可用的特性)。至少有两个空白页，还有一个简单的“待办事项”注释。

###### 好点

*   还有一些没有样板文件的通用控制器，以及其他东西(适配器、模式)的基本代码生成
*   尽管如此，适配器和模式仍然需要一些填充和与雄辩模型的手动同步
*   也为 API 提供了一流的验证规则(可能会在代码中重复出现，就像雄辩的细节一样....)
*   似乎有一个不错的认证基础设施

###### 坏点

*   文档中没有明确提到字段的选择，缺少排序和“稀疏字段集”的文档(如果这就是我所说的“字段选择”的话)？)([关于话题](https://github.com/cloudcreativity/laravel-json-api/issues/193)的问题)
*   [过滤必须是手写的](http://laravel-json-api.readthedocs.io/en/latest/fetching/filtering/#using-filter-parameter)同样，也没有自动的 API 将过滤器参数转换成雄辩的查询(很像 GraphQL 查询)...伤心)。
*   此外，没有提到文档生成。有一个[老问题](https://github.com/cloudcreativity/laravel-json-api/issues/94)说 Swagger 不能与 JSON-API 一起工作，主要开发人员没有时间致力于此(我们都知道开源是如何诞生和消亡的，对吧？相关项目生命周期中开发人员的空闲时间...)

### 子类型 2:简单的“自带标准”JSON API

> 结论:在我们的例子中最容易构建，因为我们可能最终只需要几个只读端点。可能需要一些样板文件来支持字段选择或高级查询功能，但是可以生成蓝图文档文件，甚至可以用其他生成器生成 HTML 文档。

#### 有用的包

##### [野狗](https://github.com/dingo/api/wiki)

###### 好点

*   完整的功能集:身份验证、速率限制助手、错误、路由、文档生成器(通过蓝图文件)
*   蓝图文件可以和许多解析器一起使用，然后生成 HTML 文档或类似的东西。选项和相关资源列在这篇文章的底部。

###### 坏点

良好的...你必须手写你所有的控制器和动作。并定义所有过滤器(如果有)。诸如此类的。

##### [莱斯特](https://github.com/Luracast/Restler)

> 这实际上与 Laravel 无关，但是可以很容易地使 concertified 与它一起工作，所以作为 Laravel 主应用程序的一个配套项目，它是值得一提的。

###### 好点

*   完整的特性集，包括基于 PHP 文档块和方法参数的 Swagger 文档(也用于自动请求验证)

###### 坏点

被抛弃了:

*   停止了对 PHP 5.4 的支持，但可能会大量使用 PHP 7 一级类型
*   停在一个旧版本的 Swagger 上，缺少了它的一些特性——这可能是这个包被放弃的原因之一，因为 Swagger 有时被认为是一个非常复杂的怪物。

###### 示例实现

很久以前，我开始用一个雄辩的扩展(也有点废弃)和 Restler 构建一个 [API 服务器](https://bitbucket.org/dal-glossary/server/src/master/)。它包括一些基本控制器，这些控制器只需要几行代码就可以完成 CRUD 操作。

## 其他有用的资源

*   [养蜂场](https://apiary.io/) -完整的工具集(需要澄清！)来定义、记录和处理 API。
*   Apimatic 的 transformer - UI 和 API 可以翻译几乎任何文档格式。可以集成到 API 生成管道中。
*   解析 Blueprint/Swagger 文档并自动运行测试，以确保后端符合定义的文档。
*   *【可能过时】* -根据蓝图文件生成邮递员集合
*   PHPDraft -来自蓝图文件的基本 HTML 生成器
*   [Laravel 蓝图文档](https://github.com/M165437/laravel-blueprint-docs) -在您的 Laravel 项目中生成集成文档，使用其路由和刀片模板进行进一步定制。