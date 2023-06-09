# JSON 表单定义

> 原文：<https://dev.to/megazear7/json-form-definition-36c9>

我在网络开发领域工作，简称为网络内容管理或 WCM。经常需要解决的问题之一是定义表单的简单且可重复的方法，WCM 作者通过这些表单来创建内容。

创建任何新功能的第一步通常是定义一个表单，该表单可能包括文本字段、复选框、单选选项、专门的文本字段(如 URL、日期或其他内容的路径)。除此之外，拥有这些字段的多值列表也很常见，例如一个链接列表，每个链接都有一个相应的复选框，以确定该链接是否在新选项卡中打开。这些表单可能会变得非常复杂，用一种简单的方法来定义它们可以加速新特性的开发。

我想到，使用 JSON 作为定义表单的一种方式，将允许开发人员定义在用户与表单交互之后，他们期望从表单返回什么样的 JSON。这类似于 GraphQL 背后的思想，告诉 API 你想看到什么 JSON，然后它给你。我已经创建了一个表单定义 JSON 的示例:

```
{  "firstName":"",  "isDeveloper":true,  "height":0  } 
```

在本例中，我们有一个名为“firstName”的文本字段、一个名为“isDeveloper”的复选框和一个名为“height”的数字字段。但是，如果我们想在这些字段中添加默认值呢？你可能已经看到了我所看到的:

```
 {  "firstName":"Default first name",  "isDeveloper":true,  "height":60  } 
```

每个表单字段的默认值可以是 JSON 中字段的值。如果我们希望这个表单的用户能够定义一个联系人列表，会怎么样呢？也许在表单中有一个“+”按钮，允许他们向列表中添加新字段？

```
 {  "firstName":"Default first name",  "isDeveloper":true,  "height":60,  "contacts":[  ""  ]  } 
```

我们向 JSON 添加一个数组值，它表示一个多值的表单元素。这样，用户可以添加一个列表文本字段，每个字段代表一个联系人。然而，如果我们希望每个联系人都有一个名字和一个电子邮件地址呢？

```
{  "firstName":"Default first name",  "isDeveloper":true,  "height":60,  "contacts":[  {  "firstName":"",  "email":""  }  ]  } 
```

现在我们将一个对象而不是一个简单的字符串字段放入数组。这意味着联系人列表中的每个多值元素都有两个关联值，“名字”和“电子邮件”。

但是，如果对于我们的一个字段，我们想要显式地指定一个关联的标签、应该应用的验证或者一个占位符值，该怎么办呢？

```
{  "firstName":{  "type":"",  "defaultValue":"Default first name",  "placeholder":"Example Place holder"  },  "isDeveloper":{  "type":true,  "label":"Are you a developer?"  },  "height":60,  "contacts":[  {  "firstName":"",  "email":{  "type":"",  "validation":"email"  }  }  ]  } 
```

现在，这些对象可以代表表单的一组字段，或者如果它们具有“类型”属性，则代表给定类型的单个字段。这提供了一个入口点，用于定义诸如验证、标签和表单可能需要用于字段的其他数据。请注意，字段的默认值不再在字符串本身中传递，而是在“default value”属性中提供。这是为了我们可以保留“类型”属性的使用，您将在下面看到。

并非所有字符串都是生而平等的。如果我们想要一个字符串字段来表示 WCM 系统中另一段数据的路径，或者一个日期字段呢？

```
{  "firstName":{  "type":"",  "defaultValue":"Default first name",  "placeholder":"Example Place holder"  },  "birthDate":{  "type":"date"  },  "homePage":{  "type":"path"  },  "isDeveloper":{  "type":true,  "label":"Are you a developer?"  },  "height":60,  "contacts":[  {  "firstName":"",  "email":{  "type":"",  "validation":"email"  }  }  ]  } 
```

现在，可以指定自定义对话框字段类型，将结果值存储为字符串，但有一个自定义用户交互来确定该字符串。现在，表单生成的 JSON 会是什么样子呢？看起来几乎完全一样，除了 JSON 中的“类型”字段被替换为给定类型的值:

```
{  "firstName":"Joe Smith",  "birthDate":"5/26/1983",  "homePage":"/some/example/path/that/specifies/joe/smiths/homepage",  "isDeveloper":true,  "height":71,  "contacts":[  {  "firstName":"Steve Adams",  "email":"steve.adams@example.com"  },  {  "firstName":"Bob Greene",  "email":"bobgreen12@example.com"  }  ]  } 
```

当用户提交表单时，这个 JSON 可以存储在数据库中，或者存储在您的 WCM 系统中。

现在我只需要创建 JavaScript 库来创建表单，然后基于表单生成 JSON。如果时间允许，我可能会在未来几个月做一个副业项目。我知道这在我正在进行的一些项目中会很有用。

查看我的博客,了解更多我对技术和其他各种话题的思考。