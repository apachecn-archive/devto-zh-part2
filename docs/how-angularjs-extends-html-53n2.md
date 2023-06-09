# AngularJS 如何扩展 HTML？

> 原文：<https://dev.to/shahidmansuri/how-angularjs-extends-html-53n2>

众所周知，AngularJS 为您的应用程序扩展了 HTML 词汇。AngularJS 允许你编写自定义指令，允许你用新的标签和属性来扩展 HTML。指令可以在项目内部和项目之间重用，并且可以在各种项目中验证、维护和重用。

[![angularjs extends html main](img/1195ee3c0e5d5382388f78e99010be22.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ia_igFA1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-main.jpg)

如果实现得当，可以对它们进行改进和重新部署，而对使用它们的应用程序只需稍作修改或不作修改。

为了让您更好地理解我在这里的意思，让我从一段简单的 HTML 代码开始:

[![angularjs extends html](img/b45407c27cf7cbbce57dd5ddf67a16b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DC0g-h2y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-1.jpg)

## AngularJS 扩展了 HTML

AngularJS 用 ng 指令扩展了 HTML。ng-app 指令定义了一个 AngularJS 应用程序。ng-model 指令将 HTML 控件(input、select、textarea)的值绑定到应用程序数据。ng-bind 指令将应用程序数据绑定到 HTML 视图。

[![angularjs extends html](img/bb9c532272fcf51001eded222e440871.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sxBxRcua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-2.jpg)

网页载入后 AngularJS 启动。ng-app 指令声明 AngularJS，div 元素是 AngularJS 应用程序的“所有者”。ng-model 指令将输入字段的值附加到应用程序变量名上。ng-bind 指令将 p 元素的 innerHTML 附加到应用程序变量名上。

## angularjs 指令

AngularJS 允许您使用称为指令的新属性来扩展 HTML。AngularJS 有一套内置指令，可为您的 web 应用程序添加功能。AngularJS 还允许您陈述自己的指令。

AngularJS 指令是带有 ng 前缀的 HTML 属性。ng-app 指令准备 AngularJS 应用程序。ng-init 指令准备应用程序数据。ng-model 指令将 HTML 控件的值附加到应用程序数据上。

[![angularjs extends html](img/e021a93f93c4eaca0970c8c1a2956bc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y5jJxHHx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-3.jpg)

ng-app 指令还告诉 AngularJS，div 元素是 AngularJS 应用程序的“所有者”。

该表达式是一个数据绑定表达式。AngularJS 中的数据绑定将 AngularJS 表达式与 AngularJS 数据连接在一起。这里绑定了 ng-model="firstName "。在以下示例中，两个文本字段与两个 ng-model 指令放在一起:

[![angularjs extends html](img/949f1cb63f2f9508ef90d54c43b49cdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4sosHjcy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-4.jpg)

## 重复 HTML 元素

顾名思义，ng-repeat 指令重复 HTML 的元素:

[![angularjs extends html](img/d759b0564dd2c708a1f8f797d0716c49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9JOkRWUW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-5.jpg)

ng-repeat 指令实质上是为一个组中的每个项目复制一次 HTML 元素。

## ng-app 指令

ng-app 指令概述了 AngularJS 应用程序的根元素。加载网页时，ng-app 指令应常规准备应用程序。

[![angularjs extends html cta](img/9965851a54c1b951d4b8f5294a179805.png)T2】](https://www.peerbits.com/blog/react-vs-angular-which-one-popular-javascript-developers.html)

## ng-init 指令

ng-init 指令概述了 AngularJS 应用程序的初始值。通常，您不会使用 ng-init。您将使用一个控制器或模块来代替它。

## ng-model 指令

ng-model 指令将 HTML 控件(input、select、textarea)的值附加到应用程序数据中。

## 创建新指令

除了所有内置的 AngularJS 指令之外，您还可以生成自己的指令。新的指令正在形成。指示功能。若要引发新指令，请使用与新指令相同的标记来命名 HTML 元素。

指令必须使用 camel case 名称 w3TestDirective，但是在引发它时，您必须使用—分离的名称 w3-test-directive:>

[![angularjs extends html](img/fe78ef777306f11fc8de513851796c30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UPi3oaIg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-6.jpg)

您可以使用以下命令调用指令:

[![angularjs extends html](img/5529adb12d36e1d57861741629ae8324.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T8el2ks2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-7.jpg)

## 限制

您可以将指令限制为只能由某些方法调用。通过添加值为“A”的 restrict 属性，该指令只能由属性引发:

[![angularjs extends html](img/1c336163439a386672b6988429f14ae2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9WRIJVxE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-8.jpg)

## ng-model 指令

使用 ng-model 指令，您可以将输入字段的值附加到 JavaScript 变量上。

[![angularjs extends html](img/01e1c757d98c986a0d44ef04399b9773.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8Tr8ySbY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-9.jpg)

## 双向装订

模型的变化反映在视图中，而视图的变化会更新模型。这是即时自动发生的，这确保了健壮的 web 应用程序的模型和视图始终保持同步。

[![angularjs extends html](img/581323b133ae0b21dd7c33c74c47bb11.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nCwYNtL2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-10.jpg)

## 验证用户输入

ng-model 指令可以为应用程序数据提供类型认证(数字、电子邮件、必填):

[![angularjs extends html](img/b9e344d2641dec9cb5b5f3bcbbfd767a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wq8JZuQz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-11.jpg)

在上面的实例中，只有当 ng-show 属性中的表达式创建 true 时，才会显示 span。

## 申请状态

ng-model 指令可以提供应用程序数据的状态(无效、脏、接触、错误):

[![angularjs extends html](img/2650e534e415f78615f8d9a650c7ac36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JpB4e6ge--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peerbits.com/wp-content/uploads/2018/04/angularjs-extends-html-12.jpg)

## 角度控制器

[控制器控制 AngularJS](https://www.peerbits.com/blog/reasons-behind-growing-popularity-of-angularjs.html) 中的应用。由于模型和视图的即时同步，控制器可以完全从视图中分离出来，只需专注于模型数据。由于 AngularJS 中的数据绑定，视图将反映控制器中所做的任何更改。

[![Technologies supply chain cta1](img/c429d7b64a7813b40a2db5996ac4a83a.png)T2】](https://www.peerbits.com/request-quote.html)

AngularJS 如何扩展 HTML？最早出现在[上](http://www.peerbits.com)。