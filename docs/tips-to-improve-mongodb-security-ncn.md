# 提高 MongoDB 安全性的技巧

> 原文：<https://dev.to/sandipmavani/tips-to-improve-mongodb-security-ncn>

[![Smiley face](img/d11ea2c497ae5a1efd06fd52c9d3c5af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tONfNDGo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--WZvRm2fk--/c_limit%252Cf_auto%252Cfl_progressive%252Cq_auto%252Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8qw4l1vcq9ohkgf5fppc.png)

MongoDB 提供了许多结构来提高数据的安全性。MongoDB 中数据的安全性至关重要——因此利用这些结构来减少外围应用是非常重要的。这里有 10 个技巧，可以用来提高 MongoDB 服务器在本地和云中的安全性。

1.**启用 auth**——即使您已经在可信网络中部署了 Mongodb 服务器，启用 auth 也是一个很好的安全实践。如果您的网络遭到破坏，它可以为您提供“深度防御”。编辑您的 mongod 配置文件以启用 auth

auth = true

2.**不要将生产数据库暴露在互联网上**——限制对数据库的物理访问是安全性的一个重要方面。如果没有必要，不要将您的生产数据库暴露在互联网上。如果攻击者无法物理连接到您的 MongoDB 服务器，那么您的数据会更加安全。如果你在 AWS 上，你可以把你的数据库放在 VPC 的私有子网中。阅读博客文章[在 VPC 部署 MongoDB](https://scalegrid.io/blog/deploy-mongodb-in-an-amazon-virtual-private-cloud-vpc/)了解更多信息。

3.**使用防火墙**——使用防火墙来限制哪些其他实体被允许连接到您的 mongodb 服务器。最佳实践是只允许您的应用服务器访问数据库。如果您托管在 AWS 上，请使用“安全组”来限制访问。如果你所在的提供商不支持防火墙结构，你可以使用“iptables”轻松地自行配置。参考 mongodb [文档](http://docs.mongodb.org/manual/tutorial/configure-linux-iptables-firewall/)为您的场景配置 iptables。

4.**使用密钥文件设置副本集**——指定一个共享密钥文件，以支持副本集中 mongodb 实例之间的通信。要实现这一点，将 keyfile 参数添加到配置文件中，如下所示。该文件的内容需要在所有机器上都相同。

keyFile = /srv/mongodb/keyfile

5.**禁用 http 状态接口** Mongodb 默认提供一个 HTTP 接口，默认运行在提供“home”状态页面的端口 28017 上。不建议将此接口用于生产，最好将其禁用。使用“nohttpinterface”配置设置禁用 http 接口。

nohttpinterface = true

6.**禁用 REST 接口**不建议将 monogdb REST 接口用于生产。它不支持任何身份验证。默认情况下，它是关闭的。如果您已经使用“rest”配置选项打开了它，那么您应该为生产系统关闭它。

休息=假

7.**配置 Bind_ip** 如果您的系统有多个网络接口，您可以使用“Bind_ip”选项来限制您的 mongodb 服务器只监听相关的接口。默认情况下，mongodb 将绑定到所有接口

bind_ip = 10.10.0.25，10.10.0.26

8.**启用 SSL** -如果你不使用 SSL，你的数据在你的 Mongo 客户端和 Mongo 服务器之间传输时是不加密的，容易受到窃听、篡改和“中间人”攻击。如果您通过不安全的网络(如互联网)连接到 Mongodb 服务器，这一点尤其重要。

9.**基于角色的授权**——MongoDB 支持基于角色的认证，让您对每个用户可以执行的操作进行细粒度控制。使用基于角色的结构来限制访问，而不是让所有用户都成为管理员。更多细节请参考角色[文档](http://docs.mongodb.org/manual/reference/user-privileges/#userAdminAnyDatabase)。

10.**Enterprise MongoDB&Kerberos**Enterprise MongoDB 与 Kerberos 集成进行身份验证。更多细节请参考 mongodb [文档](http://docs.mongodb.org/manual/tutorial/control-access-to-mongodb-with-kerberos-authentication/)。用户名/密码系统本质上是不安全的——如果可能，使用基于 kerb 的身份验证。

[http://mongodbwithpp . blogspot . com/2017/01/10-tips-to-improve-MongoDB-security . html](http://mongodbwithphp.blogspot.com/2017/01/10-tips-to-improve-mongodb-security.html)