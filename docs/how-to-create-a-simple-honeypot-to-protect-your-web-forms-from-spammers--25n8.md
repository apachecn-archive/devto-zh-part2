# 如何创建一个简单的蜜罐来保护你的表单免受垃圾邮件发送者的攻击

> 原文：<https://dev.to/felipperegazio/how-to-create-a-simple-honeypot-to-protect-your-web-forms-from-spammers--25n8>

我觉得这个帖子会很短很有用。这里的目标是演示一种简单的技术来帮助您阻止可能攻击您的网站表单的垃圾邮件发送者和机器人。这意味着是垃圾邮件预防的一个额外的层，而不是主要的资源。使用它与其他工具一样 reCaptcha 等。由于其简单性，我真的建议您遵循这种表单模式，因此，让我们编写代码:

假设你有下面的表单结构:

```
<form id="myformid" action="/myformaction">
    <label for="name">Your Name</label>
    <input type="text" id="name" name="name" placeholder="Your name here" required maxlength="100">
    <label for="email">Your E-mail</label>
    <input type="email" id="email" name="email" placeholder="Your e-mail here" required>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

大多数简单的机器人会搜索常见的模式，如标签通用名称，输入 id，输入通用属性，必填字段等，然后机器人会用假信息填充它们，试图发送给你和你的客户，垃圾邮件或一些恶意代码。最常见的搜索字段是名为“电子邮件、电话、地址”的字段...

因此，让我们作弊，通过将表单结构改为:
来创建一个简单的蜜罐

```
 <style>
    .ohnohoney{
        opacity: 0;
        position: absolute;
        top: 0;
        left: 0;
        height: 0;
        width: 0;
        z-index: -1;
    }
</style>

<form id="myformid" action="/myformaction">
    <!-- Real fields -->
    <label for="nameaksljf">Your Name</label>
    <input type="text" id="nameksljf" name="nameksljf" placeholder="Your name here" required maxlength="100">
    <label for="emaillkjkl">Your E-mail</label>
    <input type="text" id="emaillkjkl" name="emaillkjkl" placeholder="Your e-mail here" required>
    <!-- H o n e y p o t -->
    <label class="ohnohoney" for="name"></label>
    <input class="ohnohoney" autocomplete="off" type="text" id="name" name="name" placeholder="Your name here">
    <label class="ohnohoney" for="email"></label>
    <input class="ohnohoney" autocomplete="off" type="email" id="email" name="email" placeholder="Your e-mail here">
</form> 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看变化:

首先，我们创建了一个隐藏东西的类。的。ohnohoney 类。现在重要的是指出一些事情:

1.  不要使用 display:none，一些机器人不能访问没有 display 的字段，其他人只是知道他们不应该填充 display none 字段。不要在类名中使用“hidden ”,一些高级的机器人可以识别它。

2.  比我们创造了“真正的领域”。这是可见的字段，必须与您的后端数据相关。此字段必须将标识更改为哈希。通常我一起使用“nameHASH”模式。不要使用“名称散列”或类似的方法，简单的分割会暴露真正的字段名。现在，机器人无法识别这些字段是什么，它们只知道表单中有一些必须填写的字段，可能是按照“类型”模式。

3.  通过创建“h o n e y p o t”字段，我们将能够识别垃圾邮件发送者。重要的是:让标签为空，使用你的‘ohnohoney’类来隐藏所有的假输入。尽可能把你的虚假输入变得最简单、最普通、最有吸引力。使用简单和常见的名称，如“电子邮件，电话，姓名等”，禁用自动完成(因此，浏览器不会填写)，禁用规则，但保留类型。

现在我们的表单中有两个部分:实字段，我们的输入由哈希和奇怪的名称保护(您可以根据自己的喜好实现哈希或奇怪的名称)。还有我们的蜜罐(不要写“蜜罐”，最好把字母分开，以免被认出来)。现在在您的后端:

1.  验证是否填写了“h o n e y p o t”字段。如果是，那么恭喜你，你捕获了一个垃圾邮件。他们中的大多数将填充所有这些字段而不区分它们。所以，你所要做的就是检查你的“h o n e y p o t”字段是否被填写，如果是，那就是垃圾邮件。如果你愿意，你可以在客户端进行检查，如果是 ajax 表单，这将避免使用服务器资源来计算无用的数据(但无论如何要保持后端验证)。当你发现垃圾邮件时，不要发送数据，想怎么处理就怎么处理。如果诸如“电子邮件、电话等”的名称对您的后端很重要，只需使用数组记录这些名称。

下面是一个简单实现这种技术的单文件回购:
[【https://github.com/felippe-regazio/php-honeypot-example】](https://github.com/felippe-regazio/php-honeypot-example)

请记住:这只是一个简单的层，以简单的方式防止攻击，一些技术甚至可以识别这种模式，所以使用所有的武器，你可以反对它。但是我相信这个简单的模式可以避免至少 50%的垃圾邮件出现在你的网页中。