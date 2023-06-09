# 你的代码是 S.A.F.E 吗？

> 原文：<https://dev.to/rmorschel/is-your-code-safe-365n>

[![Hacker](img/356bc49f13646560850bc7632c2d738d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GRT_LKJm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.pixabay.com/photo/2017/05/10/12/41/hacker-2300772_1280.jpg)

网站[信息是美丽的](http://www.informationisbeautiful.net/)为[世界上最大的数据泄露](http://www.informationisbeautiful.net/visualizations/worlds-biggest-data-breaches-hacks/)提供了一个有启发性的图表。这不仅表明近年来黑客入侵变得越来越普遍，而且许多黑客入侵是由**内部人员**所为。黑客已经成为一个独立的行业，如果你是一名软件开发人员，安全应该是你关注的问题。在分布式软件的世界中尤其如此，在那里你的环境通常不完全在你的控制之下，不应该盲目信任。你需要问自己:我的代码是 S.A.F.E 吗？

## S:敏感

敏感代码保护其敏感数据(密码、应用程序机密、客户端个人信息、会话令牌，...)这意味着:

*   除非对存储库有严格的访问控制，否则不要在版本控制中存储应用程序机密。理想情况下，将秘密转移到安全的服务器上，更理想的情况是，将秘密转移到像 HashiCorp 的 [Vault](https://www.hashicorp.com/blog/vault-announcement) 这样的密码管理器中。
*   加密传输中和静态的敏感数据。
*   不记录敏感数据，尤其是如果您有一个日志聚合器，可以让公司内潜在的未授权人员看到这些数据。

## 答:访问受控

无论何时远程访问您的敏感代码，您都需要对请求进行验证和授权，即

*   打电话的人是谁？
*   他们被允许这样做吗？
*   他们被允许在这些特定的数据上这样做吗？

出于审计目的，您需要记录请求，特别是身份验证或授权失败的情况。

此外，考虑对您的网络进行分区，以便不是每个人都可以访问您的敏感服务。

# F:万无一失

你会验证你的请求吗？

验证输入是很好的防御性编程实践，但在 JavaScript 和 SQL 注入攻击的世界中尤其如此。

更喜欢强类型而不是字符串。如果您必须使用字符串，那么最好使用白名单而不是黑名单检查(今天的邪恶字符集可能不是明天的)。

*稳妥地失败。*

返回错误时，不要透露黑客可能使用的系统内部工作细节(例如堆栈跟踪或过于具体的错误消息，如“密码不正确”，而不是“登录失败”)。

## E:强制执行

除非您已经检查过，否则您不知道您的代码是安全的。这意味着:

*   [威胁建模](https://www.owasp.org/index.php/Application_Threat_Modeling)您的设计。
*   将安全检查添加到代码审查中。
*   通过测试自动化您的安全检查。

注意安全。外面很危险。

注:如果您想了解更多关于应用安全的最佳实践，请访问 [OWASP](https://www.owasp.org) 网站。