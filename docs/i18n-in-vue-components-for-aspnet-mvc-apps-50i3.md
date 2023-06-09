# ASP.NET MVC 应用的 Vue 组件中的 I18N

> 原文：<https://dev.to/keco39/i18n-in-vue-components-for-aspnet-mvc-apps-50i3>

当你需要编写纯英文的应用程序时，国际化可能没什么大不了的，但是在我的双语国家，忽略这一点从来都不是一个选项。由于我最近经常使用 Vue，我试着用翻译的标签创建了我的第一个表单。

使用的技术是 ASP.NET MVC，所以没有 Webpack，也没有插件的使用，这是我在搜索“Vue + i18n”时 100%找到的:插件的用法。

在 GitHub 上阅读了该插件的源代码后，我创建了一些可以用于我的 MVC 项目的东西。在我找到的关于它的不同文章中，经常出现的是使用过滤器来显示给定标签的翻译，这就是我所做的，并将在这篇博文的第二部分描述。

您可能还记得我的第一篇描述创建 Vue 组件的博文。如果没有的话，你不妨先看一下那个:[https://medium . com/@ Kevin _ cocquyt/vue-components-in-an-ASP-net-MVC-app-e9eb 0004 c 054](https://dev.to/kevincocquyt39/vue-components-in-an-aspnet-mvc-app-11jd)

### 第一步——创建组件

我创建了一个组件，它包含一个标题、一个带有标题元素的表格元素和一个带有数据的行。因为需要翻译标题标签，所以使用一个默认值，后跟一个过滤器，在该过滤器中检索给定标签的正确翻译。然后在组件本身和该函数中指定过滤器，在翻译列表中搜索作为参数传递的标签，如果找到，则返回该列表项的 value-property，否则，返回传递的值(管道符号之前的值)。我需要为每个对过滤器的调用传递“ml”列表(稍后将详细介绍)，因为过滤器不允许在其中使用数据属性(这意味着“this.ml”只返回 null 而不是实际值，“ml”在我的例子中代表 multilang)。

我本可以将我的组件命名为“vue-table-component”，这样更合适，但是我懒得更改我的截图(现在已经晚上 11 点了) :)

[![](img/2bb7f98986700d4ab07a5ff8399d01db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iUJ694LQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0TFnAOHQBw9MkEVtDam77A.png) 

<figcaption>使用过滤器找到正确的翻译，默认为初始值</figcaption>

### 第二步——注册组件

注册对于两件事是必要的:第一是确保你可以在你的 HTML 中使用组件，第二，你需要能够引用该组件以便能够传递翻译。

[![](img/ce539042e00dd458cdaf6a5d7872355c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7mmmMtlm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/668/1%2Av4H8tXixz0he1yAIaVYsJQ.png) 

<figcaption>组件的注册</figcaption>

### 第三步——传入译文

由于组件被注册并赋给了一个变量(可以是 let，甚至可以是 const 而不是 var)，现在可以引用该组件的数据属性了。

“main”div 被注册为将在其中加载组件的元素。“vue-form-component”是已创建组件的名称。“title”是一个数据属性，ref 是可以在 JavaScript 或 TypeScript 中使用的“引用名”(main.js 是包含创建和注册的文件)。

在底部，一个对象数组被分配给“ml”属性，每个对象有两个属性，每个属性都有适当的名称，但是您可以按照自己喜欢的任何方式命名它们，只要您相应地更改过滤函数。

[![](img/8e7f885b7368c3716bf8f2338c99e2b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uUTvuXKd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_-NRQa4Oy1SeHgx8cCzzag.png) 

<figcaption>传递一串翻译</figcaption>

当然，这些看起来像固定值，但是，举例来说，在我的专业项目中，我使用 MVC HTML-extension，比如@Html。Translation("number ")表示将从我们的数据库(使用 set 语言)中检索正确的翻译，因此一个或您添加的对象可能看起来像:

{ name: "label.number "，value: "@Html。翻译("数字")" }

确保从数据库返回的字符串是编码的，因为它们将在脚本中使用(单引号/双引号)。

在将翻译传递给引用的属性之后，multilang-label 在您的组件中是已知的，并且可以通过 filter-function 进行搜索。

### 结果

在呈现的 HTML 中，只有第二个和第三个标签被翻译，而第一个标签，因为在“ML”列表中没有它的翻译，只显示它的默认值(管道符号前的字符串)。

[![](img/e1274c2c6d89f9647ec17f0a9cd2ec9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ApkLaylY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsgSRaXQjioO2mckG_DJXxQ.png) 

<figcaption>HTML 渲染</figcaption>

仅此而已。

感谢插件的作者和我找到的文章的作者将我推向正确的方向，感谢我自己提出了一个可行的解决方案，所以我晚上可以再次入睡(是的，这些类型的事情让我睡不着！)

感谢阅读！

PS:这是一个解决方案，所以我猜不是解决方案，所以如果有更好的解决方案，请在评论区分享你的。

编辑:对于 web 开发人员来说，Internet Explorer 可能是一个真正的痛苦。它又开始了，因为它不支持 array.find()方法。为此，在 JS 中的某个地方注入 pollyfill 脚本([https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/find](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find))就可以了。或者使用洛达什(【https://lodash.com】T2)。

编辑 2:当我还在学习 Vue 的细节时，我了解了全局滤镜，觉得它很棒。为此，我试着在我的翻译中实现这一点，它非常有效！我用以下代码创建了一个名为“component-filters.ts”的新文件:

[![](img/5096f2cd37caa6c7f2a1afd6fc8a14c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dlPCfzTQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AAxBKv82KpPv400y4sR1U3A.png)

<figcaption>/js/component-filters.ts</figcaption>

并将脚本文件添加到包中:

[![](img/524eb7f9293c161b0015aab7974f2c23.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--16PvtRsC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2APrDv7cWm6E0FzSrQdR0lvA.png)

<figcaption>bundling</figcaption>

然后只需从组件中移除“翻译”过滤器，一切都会继续工作。