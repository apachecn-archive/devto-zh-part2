# 利用 UI 扩展和 Lambda 函数充分利用 Marketo

> 原文：<https://dev.to/contentful_blog/making-the-most-of-marketo-with-ui-extensions-and-lambda-functions-52b6>

[![Marketo forms, Contentful UI Extensions and Lambda functions](../Images/aec120fc1194a6615fa2ac35c0cc58c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KucGJFUB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/4ePBTSoNs4w6QgOQkAyUKI/846d320c26c17c3d869ef7d3db626e9b/20180706_Marketo_UI_Extensions.png)

Marketo 提供了一套营销自动化工具，解决了营销的不同子领域，因此可以被成熟的营销部门的各个分支广泛使用。我们在 Contentful 使用 Marketo 作为解决方案之一来处理我们的营销人员所执行的职责，例如我们的登录页面上的表单。

作为这样一个全面的平台，Marketo 有一个陡峭的学习曲线，乍一看，它的一些功能似乎很难实现。在我们的表单中，由需求生成团队等作者处理，将 Marketo 表单的代码嵌入到页面中的现成过程既繁琐又耗时。

这个过程是一个复杂的序列:获取嵌入代码并将其粘贴到页面上的 HTML 块或 markdown 字段中，然后在需要表单的每个页面上重复这个过程。表单的常规外观也有些通用，所以需要一定程度的定制来使它们看起来更有吸引力。

我们找到了一个快速的解决方法，将手动重复从等式中去掉，并使用 [UI 扩展](https://www.contentful.com/blog/2017/10/09/creating-ui-extensions-with-contentful/?utm_campaign=customize-integrate-marketo-forms-ui-extensions-lambda&utm_medium=referral&utm_source=devto&utm_content=customize-integrate-marketo-forms-ui-extensions-lambda&utm_term=)在我们的 Contentful 系统中用易于使用的选择下拉列表填充(在[我们的概念文档](https://www.contentful.com/developers/docs/concepts/uiextensions/?utm_campaign=customize-integrate-marketo-forms-ui-extensions-lambda&utm_medium=referral&utm_source=devto&utm_content=customize-integrate-marketo-forms-ui-extensions-lambda&utm_term=)中阅读更多)。我们还设法设计了表单的样式，使其更符合 Contentful 自己的品牌。

## 美化市场形态

[![Setting up a form in Marketo](../Images/bd2f61052e9c4bcb629a78f31cf2334f.png)T2】](//images.ctfassets.net/fo9twyrwpveg/6IWw7kgZUs8CocwmKsKQIw/2fb2f88e7083987ecfa3d03e2b95021d/marketo_forms_1.png)

首先也是最重要的，Marketo 表单在表单元素上使用内嵌样式，并且有一个简单的默认设计。这些表单也明显是专有的，具有非常独特的标记模式，这让我们不得不重新思考我们的表单，以便与 Marketo 一起工作。

为了确保我们从 Marketo 中删除了所有潜在的冲突样式，我们找到了一个现有的[函数](https://codepen.io/mirshko/pen/LrZaWB)，我们将其修改得更加简洁，它使用页面上的一个`div`来获取表单的 ID(由我们的静态站点生成器使用来自 UI 扩展的内容丰富的数据生成)。在那里，我们从 Marketo 加载表单，去掉 Marketo 的 JavaScript 添加的所有样式和冲突的 CSS，留给我们一个无样式的 HTML 表单。

通过我们新的干净的 HTML 表单和 UI 扩展，我们能够使用 SASS `@extend`和我们自己的表单设计风格来设计 Marketo 表单，配置为与 Marketo 表单兼容。

```
.custom_mkto_form {
  // ...
  .mktoLabel {
    @extend .label;
    display: inline-flex;
  }

  .mktoButton {
    @extend .btn;
    @extend .btn--blue;
    @extend .btn--large;
  }

  .mktoErrorMsg {
    @extend .error-message;
  }
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了一个表单，它既便于作者在页面上使用，又有很好的特定品牌风格。

## UI 扩展和 Lambda 化险为夷

由于在需要表单的每个页面上手动嵌入表单也是一个繁琐耗时的过程，我们通过在 Contentful 中构建一个 UI 扩展来解决这个问题。这为作者提供了选择表单的能力，他们可以通过类似下拉菜单的选择列表在登录页面上使用表单。

然而，首先有一个障碍需要跨越。内容丰富的 UI 扩展只执行客户端 API 调用，但是对 Marketo API 的调用必须在服务器端完成，这是由于 [Marketo 的 CORS】](https://docs.marketo.com/display/public/DOCS/Configure+Protocols+for+Marketo)和服务器端点的限制。

为了做到这一点，我们构建了一个 Lambda 函数，托管在 Netlify 上，以利用他们令人敬畏的[新函数特性](https://www.netlify.com/docs/functions/)。UI 扩展将对该函数执行 HTTP 调用，该函数将返回 Marketo 实例中所有表单的有效负载，然后解析该有效负载并生成所有表单的下拉选择，供编辑人员在登录页面上使用。

[![Installing a UI extension in Contentful](../Images/958c21a781f56473e75f4a6f220368dd.png)T2】](//images.ctfassets.net/fo9twyrwpveg/39UbV7o93qkG4kSGKYeWkk/74639beac16bbaff26abff29e6258478/marketo_forms_2a.png)

您可以通过利用我们新的 UI 扩展示例安装程序来使用这个 UI 扩展。只需在 Contentful 应用程序中进入设置，点击扩展，然后*添加扩展*。从那里，点击*安装一个示例*，从列表中找到 Marketo 表单，然后*安装*。

下一步是用 Lambda 函数的 URL 端点填充所需的 *Lambda 函数 URL* 安装参数。你可以在我们的扩展库中找到这个 Lambda 函数[的代码。](https://github.com/contentful/extensions/blob/master/samples/marketo-forms/lambda-function.js)

最好是在 [Netlify Functions](https://www.netlify.com/docs/functions/) 中创建这个 Lambda 函数，直接使用 [AWS Lambda](https://aws.amazon.com/lambda/) ，或者[其他无服务器功能平台](https://thepowerofserverless.info/services.html)中的任何一个。并不是所有的 Lambda 函数服务都是一样的，可能需要修改代码以适应您选择的平台。

设置起来相对容易；[跟随我们的教程](https://github.com/contentful/extensions/tree/master/samples/marketo-forms#usage)学习如何获得 Marketo 凭证和 Lambda 函数所需的环境变量。要使用它，当您为新的登录页面创建内容模型时，选择 *JSON 对象字段*，并继续将外观选项卡设置为 *Marketo 表单*。

现在，当您的编辑选择在登录页面上使用的表单时，您的开发人员将有一个很好的表单数据 JSON 转储，可以与[Marketo Forms JavaScript API](http://developers.marketo.com/javascript-api/forms/)一起工作和集成。

## 试用 UI 扩展

UI 扩展是使用 Contentful 定制和扩展您的编辑体验的好方法；任何具有一些 JavaScript 知识的人都可以创建他们自己的扩展，给你留下一个难以置信的可访问的、功能强大的特性和无限的可能性。如果你还没有的话，先创建一个[免费的内容丰富的账户](https://www.contentful.com/sign-up/?utm_campaign=customize-integrate-marketo-forms-ui-extensions-lambda&utm_medium=referral&utm_source=devto&utm_content=customize-integrate-marketo-forms-ui-extensions-lambda&utm_term=)，然后发现 UI 扩展...以及我们的内容基础设施与您的代码和静态站点项目的配合有多好。