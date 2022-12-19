# 使用无服务器防止表单中出现重复条目

> 原文：<https://dev.to/picsoung/prevent-duplicate-entries-on-your-forms-using-serverless--1cea>

<figure>[![](../Images/19e17f07a17bf074aa3d6de3352d1375.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8IcxpJsH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AtQgn3k8gqNFRNmvk) 

<figcaption>【猴子看镜子】作者[安德烈·木桐](https://unsplash.com/@andremouton?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

这是我们的客户反复提出的一个问题:如何防止人们重复填写一个表单？

直到现在，当他们查看结果时，我们告诉他们放弃重复的，但是现在我们可能有一个更强大的解决方案，使用我们自己的 API 和一点无服务器逻辑。

我可能有一种总是试图使用技术解决问题的倾向，所以当我的同事告诉我这个重复的问题时，我知道我们可以使用我们的 API 做一些事情。

你可以在这里查看一个工作原型[。试着回答一次，再试一次，应该就被屏蔽了。](https://picsoung.typeform.com/to/czF813)

### 一般原理🗺

对于本例，如果用户的电子邮件地址已经与之前的回复相关联，我们将阻止用户再次填写表单。

我们将使用本机逻辑跳转功能。如果电子邮件是*“授权”*，我们将进入下一个问题，如果不是，我们将进入一个语句块，说*“对不起，你已经填写了这张表格”*。

您可以手动添加电子邮件地址列表，但我们希望自动化，对不对？😄

为了实现自动化，我们将使用 webhooks。当一个新的响应被提交时，它将触发一个 webhook。webhook 将从有效负载中提取电子邮件，创建一个新的逻辑块，并使用我们的 [Create API](https://developer.typeform.com/create/) 更新 Typeform 上的逻辑。

这里有一个关于它如何工作的图表:

<figure>[![](../Images/b22b7b4266ed37d0c8f759a658bfe7d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D3JEPfDT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/909/1%2AK3jKvYQk7xxxa2k8JxIEWw.gif) 

<figcaption>一张 gif 胜过千言万语</figcaption>

</figure>

### 准备好你的字体💄

本教程的第一步是准备您的类型表单，使其与我们的集成一起工作。如果您还没有电子邮件阻止，现在就像这样在您的表单中添加一个:

<figure>[![](../Images/84f60986efcf874c090764c09c6fa4d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--15N-IHvn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3_WPEzlwgEKitPgff-LUjw.gif) 

<figcaption>拖拽一个邮件块到你的表单中</figcaption>

</figure>

我们还需要添加一个语句块。如果用户已经填写了类型表单，我们将在这里重定向用户，所以提供一个好的消息😁

**添加逻辑块的基底⚖️**

现在进入逻辑面板，这样就可以添加我们表单的逻辑基底了。选择 email 块，并添加一个新的约束，如果 email 等于【test@test.com】[，该约束将重定向到语句块(可以稍后删除)。在所有其他情况下，跳到你的下一个问题。](mailto:test@test.com)

在语句块中，添加一个总是重定向到电子邮件块的逻辑。它应该是这样的:

<figure>[![](../Images/8d465b7aecdd83424877cc77d2d2387d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bBCxUxvT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/472/1%2ApZwGCuF3C6t2MjuE9JZdsA.png) 

<figcaption>逻辑图为我们的形式</figcaption>

</figure>

测试一下🏄‍

为了确保逻辑工作，让我们测试一下！去填写你的表格，在电子邮件模块中使用*[【test@test.com】](mailto:test@test.com)*，你应该被重定向到声明模块，然后再回到电子邮件模块。

有用吗？是时候实现自动化了😜

### 一点代码🤓

我们现在想添加一小段代码，每当有人第一次回答表单时，它会自动在我们的逻辑中添加一条新规则。

为了简化开发和部署，我们将使用 AWS Lambda 和无服务器框架，所以请确保在继续之前，您已经配置了 [AWS CLI](https://aws.amazon.com/cli/) 并安装了[无服务器](http://serverless.com/)框架。

然后，在您的机器上本地克隆我们的 repo。

```
git clone https://github.com/picsoung/tf-lambda-limit-one-entry.git
cd tf-lambda-limit-one-entry
npm install 
```

Enter fullscreen mode Exit fullscreen mode

在该文件夹中，您将找到一个 serverless.yml 文件，其中包含我们功能的配置说明。

如果你打开它，你会看到一些我们需要的环境变量的占位符。

```
functions:
  addLimitLogic:
    handler: handler.addLimitLogic
    environment:
      FORM_ID: '' # Which form you want to update
      FIELD_ID: '' # Which field
      FIELD_REF: ''
      ALREADY_FILLED_FIELD_REF: ''
      TF_TOKEN: '' #Typeform API token
    events:
      - http: POST addLimitLogic 
```

Enter fullscreen mode Exit fullscreen mode

#### 获取 TF_TOKEN 值🔑

您需要为您的帐户创建一个个人 API 令牌。

在 Typeform 上，进入*我的账户>个人代币。*
创建一个新的令牌并将其值复制到`serverles.yml`文件中。

#### 获取 FORM_ID 值📃

您可以通过查看 typeform 的公共 URL 来找到 FORM_ID 的值。
它应该是`https://{username}.typeform.com/to/{form_id}`的形式。
将`form_id`复制到`serverless.yml`文件中。

#### 获取字段标识、字段引用和已填充字段引用值📃

为了获得剩余的值，我们将在浏览器中调用以下 URL:[https://api.typeform.com/forms/{form_id}](https://api.typeform.com/forms/%7Bform_id%7D)(用之前找到的值替换`form_id`)。

您应该会看到一个 JSON 文件，这是您的类型的定义，它以脚本的方式描述了所有的字段和逻辑。

从这个 JSON 文件中，我们将提取我们感兴趣的值。

`FIELD_ID``FIELD_REF`对应邮件块的`id`和`ref`属性。

找到电子邮件块并复制值。

`ALREADY_FILLED_FIELD_REF`对应于语句块的`ref`属性。找到语句块并复制它的引用值。

`serverless.yml`中的所有环境变量现在都应该有一个值。

我们准备把这个功能部署到云端。

#### 部署无服务器功能⏫

回到您的终端，运行以下命令:

```
sls deploy 
```

Enter fullscreen mode Exit fullscreen mode

这将运行一堆脚本，最后它会以`https://{something}.execute-api.{aws_region}.amazonaws.com/{env}/addLimitLogic`的形式给你一个来自 AWS 的 URL。

复制此 URL。

#### 给我们的表单添加 Webhook🔗

这是最后的步骤:

*   将我们在前面创建的 URL 作为 webhook 添加到我们的表单中。
*   在你的 Typeform 仪表板上，进入表单的 *Integrate* 选项卡，选择 *Webhooks。*
*   在那里，粘贴 URL 并打开切换。Webhooks 旁边的气泡现在应该是绿色的。

嘣！您现在拥有了一个连接到 webhook 的类型表单，它将自动添加逻辑块！

### 欣喜！😍

现在一切都设置好了，让我们试试我们的集成是否工作正常。

1.  第一次使用电子邮件地址填写表格。
2.  提交后，转到您的 Typeform 创建界面并检查逻辑面板。你应该看到一个新的规则。
3.  尝试使用相同的电子邮件地址再次填写表单，您现在应该会看到您之前定义的消息。

您的表单现在受到保护，不会出现基于电子邮件地址的重复条目！

### 更进一步🚀

我只是想展示一个简单的例子，这样你们都有一个基础来提出更复杂的用例。还有许多其他事情可以做。

在这个例子中，我们使用电子邮件作为唯一的标识符，但是，正如你可能已经猜到的。很容易找到两次填写表单的变通方法。相反，您可以使用一个随机 id，并将其作为`hidden_field`传递。

请记住，如果你使用它的电子邮件限制，该限制将只在你把它放在适当的位置。如果您想限制已经填写表单的人，那么编写一个脚本来从以前的回复中提取电子邮件并创建相应的逻辑块可能是值得的。

我希望你喜欢这个教程，请让我知道如果有什么是坏的或不清楚的，并随时建议下一个项目的想法！

黑客快乐\m/

<figure>[![🎵 Sweet dreams are made by geeks, who are we to ditch a PR? We travel to events and assorted meetups. Everybody is looking for swag.](../Images/ef35dcb0312ee4a91414f6b9d8c2ff79.png)](https://developer.typeform.com/?utm_source=dev.to&utm_medium=engineering-blog-article&utm_campaign=engineering-blog-links)T4】🎵甜蜜的梦是由极客创造的，我们是谁呢？我们去参加活动和各种聚会。每个人都在寻找赃物。</figure>

* * *