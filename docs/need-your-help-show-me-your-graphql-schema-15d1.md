# 需要你的帮助:给我看看你的 GraphQL 模式

> 原文：<https://dev.to/phortx/need-your-help-show-me-your-graphql-schema-15d1>

你好社区！

我需要你帮我。我是 [Vuex-ORM-GraphQL](https://dev.to/phortx/vue-3-graphql-kj6) 的维护者。涵盖设计 GraphQL 模式的所有不同方法是一项艰巨的工作。如果您能针对您的 GraphQL API 运行下面的自省查询，并将结果发送给我，那就帮了我大忙了。

这样我就可以分析有哪些不同的模式设计，并编写单元测试来确保我的插件可以处理所有这些设计:)

免责声明:请确保可以将模式发送给我！不要给我发送任何你可能因此被判刑的秘密或敏感信息。但是我保证会对你发给我的东西保密，我会在分析后马上删除它。我不会将它们用于任何其他目的或分发它们。

这是自省模式，你可以通过使用 GraphiQL 来运行它。

```
{  __schema  {  types  {  name  description  fields(includeDeprecated:  true)  {  name  description  args  {  name  description  type  {  name  kind  ofType  {  kind  name  ofType  {  kind  name  ofType  {  kind  name  }  }  }  }  }  type  {  name  kind  ofType  {  kind  name  ofType  {  kind  name  ofType  {  kind  name  }  }  }  }  }  inputFields  {  name  description  type  {  name  kind  ofType  {  kind  name  ofType  {  kind  name  ofType  {  kind  name  }  }  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

将结果作为私人要点发送到 [bk@itws.de](mailto:bk@itws.de) 。

非常感谢您的帮助！