# 10 个常见的面试问题

> 原文：<https://dev.to/sharadtricks/10-frequently-asked-angularjs-interview-questions--2i8j>

这些天 AngularJs 是每个人都想学习的热门话题之一，因为 AngularJS 的开发者从公司获得了更多的报酬。在这篇文章中，我将分享 10 个常见的面试问题和答案

## 1.Angular 中有哪些表达？

AngularJs 表达式用于将应用程序数据绑定到 html。表达式写在像。表达式的行为与 ng-bind 指令相同。AngularJS 应用程序表达式是纯 javascript 表达式，并在使用它们的地方输出数据。

## 2.Angular 需要 Jquery 吗？

不，Angular 不需要 Jquery。它独立于 Jquery。

## 3.MVVM 比 MVC 有什么优势？

以下是 MVVM 相对于 MVC 的一些优势:-
1 .增加视图的“可融合性”(使用 Expression Blend 设计视图的能力)
2。“无外观”视图逻辑

1.  没有重复的代码来更新视图
2.  易测性
3.  设计人员-开发人员工作流程

## 4.解释$scope。$apply()有效

$scope。$apply 重新评估所有已声明的 ng 模型，并将更改应用于任何已更改的模型(即，分配新值)
解释:$scope。$apply()是不应明确使用的核心角度函数之一，它强制角度引擎对所有观察变量和所有外部变量运行，并对它们的值应用更改

## 5.什么是拦截器？它的常见用法有哪些？

拦截器是一个中间件代码，所有的$http 请求都通过它。

拦截器是一个在$httpProvider 中注册的工厂。有两种类型的请求通过拦截器，请求和响应(分别为 requestError 和 response error)。这段代码对于所有请求/响应中的错误处理、认证或中间件非常有用。

## 6.提及使用 AngularJS 的优势是什么？

AngularJS 有几个优点，我们列举如下:

1.  AngularJS 支持 MVC 模式
2.  使用 AngularJS 可以进行两种方式的数据绑定
3.  它有预定义的表单验证
4.  它支持客户机-服务器通信
5.  它支持动画

## 7.什么是数据绑定？解释 AngularJs 中的双向数据绑定？

1.  数据绑定意味着模型和视图组件之间的数据自动同步。

2.  AngularJS 支持双向数据绑定，它将模型视为真实的单一来源，视图是模型的投影。这意味着当我们改变模型时，视图得到反映，反之亦然。

3.  在传统的概念中，HTML 页面是通过与服务器端程序交互来决定的，而当我们使用单页面应用程序时，页面是在没有任何服务器端交互的情况下决定的。因此，需要的网页部分将被更新。

## 8.区分 AngularJS 表达式& JavaScript 表达式？

AngularJS 表情:

1.  表达式根据$scope 进行计算。

2.  表达式求值对 null 和 undefined 是宽容的。

3.  在 AngularJS 中，循环、条件和异常是不需要写的。

4.  AngularJS 支持过滤器在显示前格式化数据。

JavaScript 表达式:

1.  表达式是根据窗口对象计算的。

2.  未定义的属性会产生类型错误或引用错误

3.  在 AngularJS 中，循环、条件和异常都需要编写。

4.  没有这样的功能。

## 9.AngularJs 中的模型是什么？

1.  模型是 JavaScript 对象，它通过文本字段、文本区域和选择等指令将视图数据绑定到模型中。

2.  它提供所需的验证行为，号码，网址，电子邮件，最小和最大长度等。

## 10.区分工厂、服务和提供商？

工厂:

当你使用工厂时，你创建一个对象，给它添加属性，然后返回同一个对象。当您将此服务传递到控制器中时，对象上的那些属性现在将通过您的工厂在该控制器中可用。

服务:

当您使用 Service 时，它是用“new”关键字实例化的。因此，您将向“this”添加属性，服务将返回“this”。当您将服务传递到控制器中时,“this”上的那些属性现在将通过您的服务在该控制器上可用。

提供商:

提供商是您可以传递到您的中的唯一服务。config()函数。如果希望在服务对象可用之前为其提供模块范围的配置，请使用提供程序。

从[https://www . online interview questions . com/angular-js-interview-questions/](https://www.onlineinterviewquestions.com/angular-js-interview-questions/)阅读更多内容