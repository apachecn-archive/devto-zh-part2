# Reactjs 示例中的简单表单验证

> 原文：<https://dev.to/skptricks/simple-form-validation-in-reactjs-example-204l>

[下载源代码](https://www.skptricks.com/2018/06/simple-form-validation-in-reactjs-example.html)

本教程解释了如何在 reactjs 中验证简单的用户注册表单。表单验证是 web 开发中最重要的部分，通过表单验证，我们可以使用有效的检查点或验证规则来限制无效的条目，并在一定程度上验证用户的详细信息。这里我们使用简单的用户注册表单，并使用 RegisterForm 组件执行客户端验证。让我们看下面 React 用户注册表单:
[![](../Images/77d95d3e1ae6e9d8f414c6a33e51cb08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8P_lvys0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-y_2762nutx4/WxzvrgXfEYI/AAAAAAAABjc/XEcspuqeLK4T893Cn2cpAXmcBrdie682ACLcBGAs/s400/1.PNG)

[ReactJs 表单验证示例](https://www.skptricks.com/2018/06/simple-form-validation-in-reactjs-example.html)

除了上面的表单错误信息验证，我们已经介绍了一些其他的检查点或验证规则，让我们一个一个地看看更多的验证。

姓名字段:该字段只接受有效用户名，包括用户的名、中间名和姓。电子邮件字段:该字段只接受用户的有效电子邮件地址。
手机字段:该字段接受有效的 10 位用户手机号码。
密码字段:该字段只接受用户密码详情。该验证使用户密码非常强大，并符合以下格式:
必须至少有 8 个字符
至少有 1 个特殊字符来自@ # $ %&T5 至少有 1 个数字、1 个小写字母、1 个大写字母

[https://www.youtube.com/embed/ofcolqx5cYM](https://www.youtube.com/embed/ofcolqx5cYM)