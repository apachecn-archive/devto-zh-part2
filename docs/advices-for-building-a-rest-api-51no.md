# 构建 Rest API 的建议

> 原文：<https://dev.to/ogfris/advices-for-building-a-rest-api-51no>

# 选择语言:

[![languages](img/23ba6ae13ae85ec0c446a2174b029183.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XfauJiaM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.talentinternational.com/wp-content/uploads/2017/03/NewsJack-Image.png%3Fx96169)

如果你已经熟悉了一种特定的语言，那就继续使用它，但我推荐的是 go，因为 Go 就是为此目的而构建的，使用 Go 时你不需要任何框架。

### 其他可以使用的语言:

*   PHP(拉勒尔/symphony/send)
*   Java/Kotlin (Play/Spring)
*   Ruby (Ruby on rails)
*   Python (Django)
*   Javascript (Node.js)
*   C#(。网)

# 数据库:

[![database](img/be80bea05c4ffcd4f21b411d315e49ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PX4T32qd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://yaffetidbits.typepad.com/.a/6a00d8347d063969e201bb091e1152970d-800wi)

有多种选择，我推荐 maria-db，它基本上是 mysql，但无论你选择什么，都不要为此使用 SQLite3，因为当你想在多个服务器上托管你的后端时，你会发现自己被困住了。

要操作它有两种选择，要么使用 ORM(我推荐 Go 使用 [GORM](http://gorm.io/) )要么使用对于初学者来说更快但更难的原始 sql。

# 安全:

[![security](img/6bf8cb11056f26b73e3fb23b6ffc8e71.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m0GKq7J_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.diony-security.com/wp-content/uploads/2016/08/Dollarphotoclub_52367572-1.jpg)

如果你打算做一些显式的事情，比如编辑数据库或者返回私有数据，你应该使用一个通过 throw header 传递的私有密匙。初学者通常会犯的错误示例:

### 1)

当发出“忘记密码”请求时，不要将密钥返回给前端，而只是将其存储在数据库中。

### 2)

当发出“登录”请求时，让前端将电子邮件/用户名和密码发送到后端，并在后端检查用户是否进行了过多的登录尝试，如果登录错误，则返回尽可能少的信息。

### 3)

使用 [jwt](https://jwt.io/) 。在操作登录用户的数据时，检查令牌是否正确是非常重要的。

# 数据:

重要的是返回一个前端可以读取的数据，最常用的是 [JSON](https://www.json.org/) (推荐)和 [YAML](http://yaml.org/) 。
但是你可以通过使用一种被许多[企业](https://graphql.org/users/)信任的查询语言 [GraphQL](https://graphql.org/) 来为用户和所有者简化所有这些查询工作。
[![GraphQL](img/f7ea74bdabd35eea59b01bd4ff63418d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FKILcCkN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A49DDRZhUWvVnH-QNHuSUSw.png)

# 贡献:

请务必在评论中给出建议！