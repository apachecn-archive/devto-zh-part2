# “点网新安装”问题-“错误:值不能为空”

> 原文：<https://dev.to/dance2die/dotnet-new-install-issue--error-value-cannot-be-null-837>

[。NET CLI](https://github.com/dotnet/cli) 使得创建[一个新模板](https://blogs.msdn.microsoft.com/dotnet/2017/04/02/how-to-create-your-own-templates-for-dotnet-new/)变得很容易。
但是在创建 SPA 模板时，有时您可能会对下面这个不太有用的错误消息感到沮丧。

```
> dotnet new -i .\NewTemplate
Error: Could not install ".\NewTemplate".
Error: Value cannot be null.
Parameter name: key 
```

Enter fullscreen mode Exit fullscreen mode

## 🤔原因

据[迈克·洛尔贝茨克](https://github.com/mlorbetske)称，当其中一个`node_modules`文件夹包含一个`template.json`文件时，就会出现错误。

> TL；DR——添加到项目中的特定节点模块包含一个 template.json 文件

## 💡解决

所以如果你的模板里有一个`node_modules`文件夹，**删除它**然后你就可以开始了。

###### 注📝:的下一版本。NET CLI 模板引擎(dotnet new3)有- trace:authoring 选项，但在这种情况下没有帮助[根据迈克](https://github.com/dotnet/templating/issues/1498#issuecomment-378501905)。

## 🚪结束语

我已经在 GitHub 上报告了这个问题，Mike 正在准备一个公关来解决这个问题。
问题页面提供了重现错误以及如何解决错误的完整步骤。

****更新 2018/04/10 ****
该修复将在[“CLI after 2 . 1 . 300-preview 2”](https://github.com/dotnet/templating/issues/1498#issuecomment-380312603)中提供。

此问题是在使用 React.js 和 ES6 为[ASP.NET 核心创建新的 SPA 模板时发现的，因为默认模板使用 TypeScript。](https://www.nuget.org/packages/ReactES6.Web/)

你可以在我之前的文章[ASP.NET Core 2 react . js Template with ES6(非 TypeScript)](https://dev.to/dance2die/aspnet-core-2-reactjs-template-with-es6-not-typescript-f71) 中找到更多关于如何使用它的信息。😉。