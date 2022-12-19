# React、Redux 和 Asp.Net 的翻译

> 原文：<https://dev.to/craignicol/translations-with-react-redux-and-aspnet-1a17>

在最近的一个项目中，这是我第一次在 anger 中使用 React 和 Redux，我们需要支持两种不同的语言。Net 后端的电子邮件和 pdf，并在反应前端。

由于我们使用的翻译器习惯于 resx 文件，所以我们想使用它们作为主源文件。在其他项目中，我们通过预编译步骤将 javascript 翻译成静态 javascript 文件，但是由于 redux 有自己的存储，我决定看看我们是否可以使用它来存储和处理翻译。

这种方法的优点是，当选择一种新语言时，我们可以使用 redux 状态自动翻译站点，而不必重新加载任何页面。对于依赖于最新数据且必须通过 por 数据连接进行操作的应用，避免重载至关重要。

此处显示的 redux 示例使用了 TypeScript，这无疑对我们的开发过程有所帮助，但是在获得 **dotnet 新的 react-redux** 模板以及许多第三方 react 和 redux 库与 TypeScript 配合使用时会遇到很多问题。您可以让它工作，但在大多数情况下，它肯定不是开箱即用的解决方案。

该解决方案由 3 部分组成:

1.  Asp.Net 核心 2.0 控制器将 resx 数据翻译成 JSON，
2.  用于检索数据并将其填充到存储中的 Redux 配置；和
3.  一个 React 函数，从存储中读取翻译并呈现给用户。

下面的解决方案试图从用户头中获取本地化，因此您需要在您的 **Startup.cs** 中启用本地化，但是根据您的使用情况，您可能希望将用户语言设置保存在用户的帐户数据中，或者保存在 cookie 或他们浏览器的其他存储中。

**本帖中的代码示例，[请看 WordPress](https://craignicol.wordpress.com/2018/08/03/translations-with-react-redux-and-asp-net/) 上的原文 T3】**