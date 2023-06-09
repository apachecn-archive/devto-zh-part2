# 逐步:通过 Telecheck、Payeezy 和 Direct API 接受支票。网)

> 原文：<https://dev.to/catriname/step-by-step-accepting-checks-via-telecheck-payeezy--direct-api-net--299j>

这是一个关于如何处理 Payeezy 的非常基础的教程，我发现他的文档有时很难。有些指导我只在论坛的帖子里找到，所以我记录下来...

## 得到自己需要的东西

1.  商户模拟账户
2.  开发者沙盒帐户
3.  商户令牌(演示)
4.  API 秘密(沙盒)
5.  API 密钥(沙盒

### 商户模拟账户

要进行设置，**您必须运行一个演示过程，并成功调用一个沙盒帐户**的 API。成功拨打电话后，您必须联系 Payeezy 开发人员支持，并要求他们“加载”您的真实商家帐户，然后运行以下相同的过程，但将您的 API 设置为“实时”而不是“沙盒”。

所以，首先，让我们从建立一个模拟商家账户开始。简单地 [**在这里**](https://provisioning.demo.globalgatewaye4.firstdata.com/signup) 注册，填写所有信息，你应该会收到一个用户名和密码。他们通常会要求您在首次登录时更改密码。您将需要此处的信息，以便在开发者沙盒中设置您的商家令牌。

### 开发者沙盒账号

**[点击这里报名。](https://developer.payeezy.com/apis)** 这也应该是瞬间的。

*   注册
*   转到“商家”菜单选项
*   点击“沙盒”按钮
*   点击“+添加一个演示商家”

### 商户令牌

[![](img/59fb3850afb43cb1aaca106b7fb6b1df.png)](http://www.catrina.me/wp-content/uploads/2018/03/adddemomerchant.jpg) 使用您登录的**商家模拟账户**中的信息获取商家代币(请严格按照图中所示的各个字段下的说明进行操作。指令是可靠的，唯一的小问题是您必须删除 HMAC 密钥，并创建和复制一个新的(因为这是您第一次登录-您没有旧的)。创建它，复制/保存它，返回沙盒并提交表单。你应该会看到一个屏幕，上面列出了你沙箱中的所有商家。您的商家令牌列在第三列，复制并保存。 [![](img/3f4a6f1058e808e9c592f8d83ab562b2.png)](http://www.catrina.me/wp-content/uploads/2018/03/token.jpg)

### API 密钥/秘密

*   从顶部的沙盒菜单中，选择“API”
*   单击添加新 API 按钮
*   输入任何名称
*   选择“沙盒”
*   使服从

完成后，您将进入新的 API。单击它，应该会列出 API Secret 和 API Key。还要复制保存。

# 获取编码

首先，很难在。NET / C#的文档，[但是有一个线程提供了有用的示例](https://developer.payeezy.com/content/net-integration)。我遵循这些例子，并将我发现最有用的一个复制到文件 [TestPayeezy()](https://github.com/catriname/CheckPayeezy/blob/master/TestPayeezy.cs) 中，以防它被删除。以下所有代码都可以在我的 GitHub 上找到:【https://github.com/catriname/CheckPayeezy T4】

### 基本款

至此，我创建了一个基本表单，在向用户展示之前，我检索了用户配置文件信息以自动填充基本的账单地址信息。这是我的[提前还款视图模型](https://github.com/catriname/CheckPayeezy/blob/master/ViewModels/PrePaymentVM.cs)。然后，我创建了一个[支票支付视图模型](https://github.com/catriname/CheckPayeezy/blob/master/ViewModels/CheckPaymentVM.cs)，它包括以前的预付款信息+新的支票信息。这个我通过控制器传给了 [SendToCheckAPI](https://github.com/catriname/CheckPayeezy/blob/master/Services/SendToCheckAPI.cs) 。[所有被检查的模型](https://github.com/catriname/CheckPayeezy/tree/master/API_Models/Check)被创建来填充一个事务，并且被创建和命名以便当事务模型被序列化时(JsonConvert。SeralizeObject())它将匹配 Payeezy 要求的格式。我还创建了一个[check response](https://github.com/catriname/CheckPayeezy/blob/master/API_Models/CheckReponse.cs)模型来处理 API 的响应，再次使用了他们的文档。反序列化对象将读取响应。

### 仔细查看 SendToCheckAPI

为此，我简单地使用了 TestPayeezy()中提供的例子。唯一的区别是，我没有使用 StringBuilder 来构建我的 JsonString，而是创建了一个事务对象，对它进行序列化，然后通过 CreateHMAC 方法运行它。我将响应反序列化为一个漂亮干净的 CheckResponse 对象，并快速搭建了该模型的视图，以便我可以立即查看 API 响应。*为了测试数据，我访问了这个[收款人链接](https://developer.payeezy.com/payeezy-api/apis/post/transactions-10)，并使用了左侧 JSON 中的姓名、路由号、账号、支票号、客户 id、客户 id 号。*

### 有些明白了

如果你一直收到“400 错误请求”,不要认为你的邮件头有问题，等等。我最初的 [tele_check 模型](https://github.com/catriname/CheckPayeezy/blob/master/API_Models/Check/tele_check.cs)包含了我没有填充的字段，因此填充了空字符串和 null。这导致了重复的“400 错误请求”响应，直到我从对象中移除/注释掉这些字段。如果一开始不成功就试一试。

关于这一点，当我们上线时，我们很难理解“check_type”以及它是“P”(个人)还是“C”(商业)。商业广告的必填字段似乎更敏感一些。我们首先使用 Personal，它就像 demo 一样工作，然后在开发支持的帮助下微调所需的商业领域及其值。

我们上线时的另一个问题是“客户标识”和“客户标识号”。我费了很大劲才找到这个属性的确切位置，并且它是必需的。作为参考，int 值为:

*   1 =驾照
*   2 =社会保障
*   3 =税务 ID

我的表单域占位符显示:“DL:tx 12311145/SSN 或 TAXID 中没有破折号”