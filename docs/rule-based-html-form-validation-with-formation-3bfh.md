# 基于规则的 HTML 表单验证和格式

> 原文：<https://dev.to/ozzyogkush/rule-based-html-form-validation-with-formation-3bfh>

即使对于相对简单的项目，一致的客户端表单验证也会变得乏味。为此，不久前我写了一个名为 [Formation](http://ozzyogkush.github.io/formation/) 的库(在 https://www.npmjs.com/package/js-formation[的 NPM 上可以找到)。](https://www.npmjs.com/package/js-formation)

Bootstrap 以及如何简单地将`data-`属性添加到 DOM 节点、添加`<script>`标签，并让库看到和做出反应，给了我灵感。这使得开发人员无需了解 JavaScript 就可以使用代码，同时提供了一个简单的 API 来挂钩已注册的更改。Formation 允许开发人员基于文本输入、复选框/组、单选按钮组和选择框来验证表单。开发人员可以添加处理程序来侦听字段和/或表单验证状态的更改，添加自定义规则并将其应用于许多字段。[参见基于文本输入的实例](http://ozzyogkush.github.io/formation/examples/text-inputs)。

目前它强制执行一个提交按钮，但这是一个基于早期观点的选择，我可能会修改。

这最初是考虑到 jQuery 和原始 DOM 操作而设计的，后来我从等式中删除了 jQuery，因为最终它是不必要的。如今，像 React 和 Vue 这样的有状态视图库已经成为标准，手动 DOM 操作已经不再是一件事了。因此，在不久的将来，我将致力于从 DOM 遍历/操作端提取验证和规则引擎，这样就可以很容易地扩展到像 React 和 Vue 这样的第三方库。它还使得重用规则/验证器引擎服务器端表单提交处理成为可能(当然是在运行节点后端的项目中)。

对于不需要 React 之类的开销，但需要让用户输入和提交数据的基本 web 项目，这可以节省大量时间和精力。通常，当构建某段代码时，存在一个维护良好的模块/包/库，它可以而且应该被重用。我想编队将会到达它被普遍使用的水平。只有时间会证明一切！

这是一个开源项目，我欢迎贡献者！在 github 上查看:[https://github.com/ozzyogkush/formation/](https://github.com/ozzyogkush/formation/)