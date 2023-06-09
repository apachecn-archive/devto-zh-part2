# Web 开发人员安全清单 V1

> 原文：<https://dev.to/sensedeep/web-developer-security-checklist-1knh>

*该清单已在[V2](https://www.sensedeep.com/blog/posts/stories/web-developer-security-checklist.html)网站开发者清单中更新。*

*最初发布于:[https://www . sense deep . com/blog/posts/stories/we B- developer-security-check list . html](https://www.sensedeep.com/blog/posts/stories/web-developer-security-checklist.html)T3】*

在云中开发安全、健壮的 web 应用程序是非常困难的。如果你认为这很容易，你要么是一种更高形式的生命，要么你前面有一个痛苦的觉醒。

如果你喝了 [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) cool-aid，并且相信你可以在一个月内创造出一个既有价值又安全的产品——在你推出你的“原型产品”之前，请三思。

在您查看下面的核对表后，请确认您跳过了许多这些关键的安全问题。最起码，对你的潜在用户要诚实，让他们知道你还没有一个完整的产品，而且提供的是一个没有完全安全保障的原型。

这份清单很简单，但绝不完整。我开发安全的 web 应用程序已经超过 14 年了，这个列表包含了我在这段时间痛苦地学到的一些更重要的问题。我希望您在创建 web 应用程序时认真考虑它们。

请评论，如果你有一个项目，我可以添加到列表中。

## **数据库**

[ ]对识别用户的数据和敏感数据(如访问令牌、电子邮件地址或计费详细信息)进行加密。

[ ]如果您的数据库支持低成本静态加密(如 [AWS Aurora](https://aws.amazon.com/about-aws/whats-new/2015/12/amazon-aurora-now-supports-encryption-at-rest/) )，那么启用它来保护磁盘上的数据。确保所有备份也加密存储。

[ ]对数据库访问用户帐户使用最低权限。不要使用数据库根帐户。

[ ]使用专门设计的密钥库来存储和分发秘密，例如[保险库](https://www.vaultproject.io/)或 [AWS 秘密管理器](https://aws.amazon.com/secrets-manager/)。不要在应用程序中硬编码秘密，也不要将秘密签入 GitHub。

[ ]通过仅使用 SQL 预准备语句来完全防止 SQL 注入。例如:如果使用 NPM，不要使用 npm-mysql，使用支持预处理语句的 npm-mysql2。

## 发展

[ ]确保对您软件的所有组件进行扫描，以查找推向生产的每个版本的漏洞。这意味着操作系统、库和包。这应该自动进入 [CI-CD](https://en.wikipedia.org/wiki/CI/CD) 流程。

[ ]保护开发系统，对生产系统中使用的系统保持同样的警惕。从安全、隔离的开发系统中构建软件。

## 认证

[ ]确保所有密码都使用适当的密码进行哈希处理，例如 [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) 。永远不要写你自己的加密和正确初始化加密与良好的随机数据。

[ ]实施简单但足够的密码规则，鼓励用户使用长的随机密码。

[ ]对所有服务提供商的登录使用多因素身份验证。

## **拒绝服务保护**

[ ]确保对您的 API 的 DOS 攻击不会使您的站点瘫痪。至少，在较慢的 API 路径(如登录和令牌生成例程)上设置速率限制器。

[ ]对用户提交的数据和请求的大小和结构实施健全性限制。

[ ]通过像 [CloudFlare](https://www.cloudflare.com/) 这样的全局缓存代理服务，使用[分布式拒绝服务](https://en.wikipedia.org/wiki/Denial-of-service_attack) (DDOS)缓解。如果您遭受 DDOS 攻击，这可以打开，否则作为您的 DNS 查找功能。

## **网页流量**

[ ]对整个站点使用 TLS，而不仅仅是登录表单和回复。永远不要只对登录表单使用 TLS。

[ ] Cookies 必须是 httpOnly 和安全的，并且由路径和域限定范围。

[ ]使用 [CSP](https://en.wikipedia.org/wiki/Content_Security_Policy) 而不允许不安全的-*后门。配置起来很痛苦，但是值得。

[ ]在客户端响应中使用 X-Frame-Option、X-XSS-Protection 标头

[ ]使用 HSTS 响应强制仅 TLS 访问。将所有 HTTP 请求重定向到作为备份的服务器上的 HTTPS。

[ ]在所有形式中使用 CSRF 令牌，并使用新的 [SameSite Cookie](https://scotthelme.co.uk/csrf-is-dead/) 响应头，该响应头一劳永逸地修复了所有新浏览器的 CSRF。

## **原料药**

[ ]确保公共 API 中没有可枚举的资源。

[ ]确保用户在使用您的 API 时得到充分的身份验证和适当的授权。

[ ]在 API 中使用金丝雀检查来检测指示攻击的非法或异常请求。

## **验证**

[ ]为快速的用户反馈进行客户端输入验证，但永远不要相信它。

[ ]使用服务器上的白名单验证用户输入的每一个细节。永远不要将用户内容直接注入到响应中。永远不要在 SQL 语句中使用用户输入。

## **云配置**

[ ]确保所有服务都有最少的开放端口。虽然隐蔽的安全性并不能起到保护作用，但使用非标准端口会让攻击者更加难以下手。

[ ]在任何公共网络上都不可见的专用 VPC 上托管后端数据库和服务。配置 AWS 安全组和对等 VPC 时要非常小心，这可能会无意中使服务对公众可见。

[ ]将逻辑服务隔离在单独的 VPC 和对等 VPC 中，以提供服务间通信。

[ ]确保所有服务只接受来自最小 IP 地址集的数据。

[ ]限制传出 IP 和端口流量，以最大限度地减少 apt 和“僵尸化”。

[ ]始终使用 AWS IAM 用户和角色，而不是根凭据。投资学习有效使用 IAM。

[ ]对所有运营和开发人员使用最低限度的访问权限。为 IAM 用户和角色提供完成任务所需的最低能力。

[ ]根据计划定期轮换密码和访问密钥。

## **基础设施**

[ ]确保您可以在不停机的情况下进行升级。确保您能够以完全自动化的方式快速更新软件。

[ ]使用 Terraform 等工具创建所有基础架构，而不是通过云控制台。基础设施应该被定义为“代码”,并且只需按一下按钮就可以重新创建。对在云中手动创建的任何资源采取零容忍态度，然后 Terraform 可以审核您的配置。

[ ]对所有服务使用集中日志记录。您不应该需要 SSH 来访问或检索日志。

[ ]除非是一次性诊断，否则不要使用服务。经常使用 SSH，通常意味着您没有自动化一项重要的任务。

[ ]不要在任何 AWS 服务组上永久打开端口 22。

[ ]创建[不可变的主机](http://chadfowler.com/2013/06/23/immutable-deployments.html)，而不是您修补和升级的长寿服务器。(参见[不可变的基础设施更安全](https://www.sensedeep.com/blog/posts/immutable-infrastructure-can-be-dramatically-more-secure.html))。

[ ]使用一个[入侵检测系统](https://en.wikipedia.org/wiki/Intrusion_detection_system)来最小化 [APTs](https://en.wikipedia.org/wiki/Advanced_persistent_threat) 。

## **操作**

[ ]关闭未使用的服务和服务器。最安全的服务器是关机的服务器。

## 测试

[ ]审核您的设计和实施。

[ ]进行渗透测试——自己动手，但也让别人而不是你来进行测试。

## **终于有了计划**

[ ]有一个描述您所防御的威胁的模型。它应该列出可能的威胁和行为者，并对其进行优先排序。

[ ]有一个经过实践的安全事件计划。总有一天，你会需要它。

## 关于 SenseDeep

SenseDeep 是 AWS 开发人员的一个观察平台，用于加速无服务器应用程序的交付和维护。SenseDeep 通过集成的无服务器开发人员工作室，在整个生命周期中帮助开发人员创建可观察、可靠和可维护的应用，该工作室包括设计和调试工具以及对无服务器应用的深入洞察。

要尝试 SenseDeep，请在浏览器中导航至:

[https://app.sensedeep.com](https://app.sensedeep.com)。

要了解有关 SenseDeep 的更多信息，请访问:

[https://www.sensedeep.com/product](https://www.sensedeep.com/product)。