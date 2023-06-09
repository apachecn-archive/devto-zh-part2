# HTML 输入元素:耳朵和眼睛

> 原文：<https://dev.to/rjdoughty/html-inputs-element--the-ears-and-eyes-l4m>

# HTML 输入元素:耳朵和眼睛

在做一个项目的时候，我在想输入标签和表单元素在开发世界中有多重要。由于这是一个博客，我试图找到一个吸引人的或迷人的短语来描述，我能想到的最好的是输入字段有点像耳朵和眼睛，接收最终用户提供的细节(我猜这会使 JavaScript 成为大脑)。

输入只是为我们提供了一种方式，允许应用程序从最终用户那里获取信息，以进行存储(POST)和/或操作(GET、PUT、DELETE)。Input 元素放在 form 元素中以收集输入。输入元素使用不同的属性来定义和创建输入规则。

## 属性

### 名称

你应该总是给输入添加一个**名称**属性。使用`<input name=” ”>`在输入标签中定义类型。name 属性用于引用 JavaScript 中的元素。

### 类型

输入类型很有用，因为它们定义输入字段并基于类型创建规则。使用`<input type=” ”>`在输入标签中定义类型。一些常用的输入类型是文本、提交和复选框。

最常见的输入类型是**文本**，如果没有指定，这是默认类型，允许一个简单的文本字段。

**日期**输入类型提供需要日期格式的字段。字段中的默认值是“mm/dd/yyyy ”,要求用户输入正确的日期格式。

**电子邮件**输入类型提供了一个需要电子邮件格式文本的字段。

输入类型**复选框**显示为一个方形框，激活时会被勾选(选中)。可以将“选中”或“未选中”的附加属性分配给复选框，以便在页面加载时强制选中该框。默认为未选中。

提交输入类型创建一个提交按钮，用于提交或发送表单信息。提交按钮中的默认文本是“提交”。

参见下面一个输入表单的代码:

```
<form>
    <input type="text" name="name" placeholder="name"/><br>
    <input type ="date" name="birthdate"/><br>
    <input type="email" name="email" placeholder="name@email.com"/><br>
    Sign me up for email updates<input type="checkbox" name="subscribe"  value="Subscriber" checked/><br>
    <input type ="submit" value="Submit">
</form> 
```

### 其他

可以使用其他属性进一步增强输入。属性可以为输入设置规则，并有助于用户体验。公共属性**值**和**占位符**将默认文本添加到输入字段。占位符为用户提供了所请求输入的提示，而 value 规定了所提供的输入值。

下图是页面上显示的表单:
[![alt text](img/85854081c46c02502145be38da826e61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ri_AEdgm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4mymz7ypb3961bg36376.png)

请继续关注未来关于如何使用 GET、POST、PUT 和 DELETE 方法的讨论....