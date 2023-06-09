# Web 开发人员安全清单 V2

> 原文：<https://dev.to/sensedeep/web-developer-security-checklist-v2-3p9b>

在云中开发安全、健壮的 web 应用程序是非常困难的。如果你认为这很容易，你要么是一种更高形式的生命，要么你前面有一个痛苦的觉醒。

如果你喝了 [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) cool-aid，并且相信你可以在一个月内创造出一个既有价值又安全的产品——在你推出你的“原型产品”之前，请三思。

在您查看下面的核对表后，请确认您跳过了许多这些关键的安全问题。最起码，对你的潜在用户要诚实，让他们知道你还没有一个完整的产品，而且提供的是一个没有完全安全保障的原型。

这份清单很简单，但绝不完整。我开发安全的 web 应用程序已经超过 14 年了，这个列表包含了我在这段时间痛苦地学到的一些更重要的问题。我希望您在创建 web 应用程序时认真考虑它们。

这是清单的第二版。它是从第一版重新组织的，并根据公众需求增加了一些新项目(谢谢)。当我试图保持列表的紧凑和集中时，如果你有你认为我应该添加到列表中的项目，请评论。

## **国书和机密**

*   [ ]使用专门设计的密钥库存储和分发机密。不要在你的应用程序中硬编码秘密，也绝对不要存储在 GitHub 中！。对于 CMS 爱好者来说，不要把你的凭证存储在文档目录下的文件中。

*   [ ]对所有服务密码和凭据使用基于团队的密码管理器，如 [1Password](https://1password.com/business/) 。不要通过电子邮件向团队成员发送密码或凭证。

*   [ ]对服务提供商的所有登录使用多因素身份验证。

## **认证**

*   [ ]确保所有密码都使用适当的密码进行哈希处理，例如 [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) 。永远不要写你自己的加密和正确初始化加密与良好的随机数据。考虑使用认证服务，如 [Auth0](https://auth0.com/) 或 [AWS Cognito](https://aws.amazon.com/cognito/) 。

*   [ ]针对登录、忘记密码和其他密码重置，使用最佳实践和经验证的组件。不要发明你自己的——很难在所有情况下都正确。

*   [ ]实施简单但足够的密码规则，鼓励用户使用长的随机密码。

*   [ ]永远不要以任何未经记录和公开的方式访问设备，包括后门帐户(如“现场服务”)。

*   [ ]以最低权限运行应用程序和容器，不要以 root 身份运行(注意:Docker 默认以 root 身份运行应用程序)。

## **数据库**

*   [ ]除非真正需要，否则不要存储敏感数据。这意味着电子邮件地址，个人身份信息和其他一般个人信息。像对待放射性废物一样对待敏感数据——也就是说，保护敏感数据需要付出真实、巨大和持续的成本，并且有一天它会伤害到你。

*   [ ]保存一份所有存储敏感信息的地方的完整列表:数据库、文件系统、Dropbox、GitHub、Vault、Office docs 甚至是纸质文件夹。这有助于管理，是 GDPR 所要求的，如果遭到黑客攻击也是必不可少的。你需要能够找到所有敏感信息。

*   [ ]如果受制于 GDPR，请确保您真正理解需求，并从一开始就进行设计。对一些人来说，这将代表着设计和思维的重大变化。参见和[GDPR](https://www.cmswire.com/customer-experience/an-introduction-to-the-gdpr/)简介。

*   [ ]如果可能，对识别用户的数据和敏感数据(如访问令牌、电子邮件地址或帐单详细信息)使用加密(这将限制查询以精确匹配查找)。

*   [ ]如果您的数据库支持低成本静态加密(如 [AWS Aurora](https://aws.amazon.com/about-aws/whats-new/2015/12/amazon-aurora-now-supports-encryption-at-rest/) )，那么启用它来保护磁盘上的数据。确保所有备份也加密存储。

*   [ ]对数据库访问用户帐户使用最低权限。不要使用数据库根帐户，并检查未使用的帐户和密码错误的帐户。

*   [ ]通过仅使用 SQL 预准备语句来完全防止 SQL 注入。例如:如果使用 NPM，不要使用 npm-mysql，使用支持预处理语句的 npm-mysql2。

## **应用程序**

*   [ ]保护开发系统，对生产系统中使用的系统保持同样的警惕。从安全、隔离的开发系统中构建软件。

*   [ ]确保对您软件的所有组件进行扫描，以查找推向生产的每个版本的漏洞。这意味着操作系统、库和包。这应该自动进入 [CI-CD](https://en.wikipedia.org/wiki/CI/CD) 流程。

*   [ ]为快速的用户反馈进行客户端输入验证，但永远不要相信它。总是在显示之前验证和编码用户输入。

*   [ ]使用服务器上的白名单验证用户输入的每一个细节。考虑使用类似于 [Swagger](https://swagger.io) 的工具从 API 规范中生成验证代码，这比手工生成的代码更可靠。

*   [ ]不要直接将用户内容注入到响应中。永远不要在 SQL 语句或其他服务器端逻辑中使用不受信任的用户输入。

*   [ ]对所有应用程序、服务器和服务使用集中日志记录。您不应该需要 SSH 来访问或检索日志。

*   [ ]记录足够详细的信息，以诊断所有操作和安全问题，并且绝不记录敏感或个人信息。考虑在 JSON 中创建带有[高基数](https://www.honeycomb.io/blog/2018/03/observability-a-manifesto/)字段的日志，而不是简单的文本行。

*   [ ]不要向用户发出暴露错误的详细信息或堆栈跟踪，不要在启用调试的情况下将您的应用程序部署到生产环境中。

## **原料药**

*   [ ]确保用户在使用您的 API 时得到充分的身份验证和适当的授权。

*   [ ]确保公共 API 中没有可枚举的资源。对于 id，考虑使用符合 RFC 4122 的 uuid 而不是整数。节点见 [NPM uuid](https://www.npmjs.com/package/uuid) 。

*   [ ]在 API 中使用金丝雀检查来检测指示攻击的非法或异常请求。

## **网络流量**

*   [ ]划分您的网络并保护敏感服务。使用防火墙、虚拟专用网络和云安全组来限制和控制进出适当目的地的入站和出站流量。AWS 和 [CloudFlare](https://www.cloudflare.com/waf/) 都有出色的产品。

*   [ ]对整个站点使用 TLS，而不仅仅是登录表单和回复。永远不要只对登录表单使用 TLS。作为过渡，使用 strict-transport-security 头对所有请求强制 HTTPS。

*   [ ] Cookies 必须是 httpOnly 和安全的，并且由路径和域限定范围。

*   [ ]使用 [CSP](https://en.wikipedia.org/wiki/Content_Security_Policy) 而不允许不安全的-*后门。配置起来很痛苦，但是值得。对 CDN 内容使用 CSP [子资源完整性](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)。

*   [ ]在客户端响应中使用 X-Frame-Option、X-XSS-Protection 标头。使用[https://observatory.mozilla.org](https://observatory.mozilla.org)给你的网站打分。

*   [ ]使用 HSTS 响应强制仅 TLS 访问。将所有 HTTP 请求重定向到作为备份的服务器上的 HTTPS。

*   [ ]在所有形式中使用 CSRF 令牌，并使用新的 [SameSite Cookie](https://scotthelme.co.uk/csrf-is-dead/) 响应头，该响应头一劳永逸地修复了所有新浏览器的 CSRF。

*   [ ]删除其他可以使黑客更容易识别您的堆栈和软件版本的标识标头。

*   [ ]不要在 URL 中使用带有敏感数据或令牌的 GET 请求，因为这些数据和令牌会被记录在服务器和代理上。

## **云配置**

*   [ ]确保所有服务都有最少的开放端口。虽然隐蔽的安全性并不能起到保护作用，但使用非标准端口会让攻击者更加难以下手。

*   [ ]在任何公共网络上都不可见的专用 VPC 上托管后端数据库和服务。配置 AWS 安全组和对等 VPC 时要非常小心，这可能会无意中使服务对公众可见。

*   [ ]在独立于生产资源使用的 AWS 帐户中创建测试和试运行资源。

*   [ ]将逻辑服务隔离在单独的 VPC 和对等 VPC 中，以提供服务间通信。

*   [ ]确保所有服务只接受来自最小 IP 地址集的数据。

*   [ ]限制传出 IP 和端口流量，以最大限度地减少 apt 和“僵尸化”。

*   [ ]始终使用 AWS IAM 角色，而不是根凭据。

*   [ ]对所有运营和开发人员使用最低限度的访问权限。

*   [ ]根据计划定期轮换密码和访问密钥。

## **基础设施**

*   [ ]确保您可以在不停机的情况下进行升级。确保您能够以完全自动化的方式快速更新软件。

*   [ ]使用 [Terraform](https://www.terraform.io) 等工具创建所有基础架构，而不是通过云控制台。基础设施应该被定义为“代码”,并且只需按一下按钮就可以重新创建。对在云中手动创建的任何资源采取零容忍态度，然后 Terraform 可以审核您的配置。

*   [ ]除非是一次性诊断，否则不要使用服务。经常使用 SSH，通常意味着您没有自动化一项重要的任务。

*   [ ]不要在任何 AWS 服务组上永久打开端口 22。如果必须使用 SSH，请仅使用公钥认证，而不要使用密码。

*   [ ]创建[不可变的主机](http://chadfowler.com/2013/06/23/immutable-deployments.html)，而不是您修补和升级的长寿服务器。(参见[不可变的基础设施更安全](https://www.sensedeep.com/blog/posts/stories/immutable-infrastructure-can-be-dramatically-more-secure.html))。

*   [ ]如果不使用不可变的基础架构(坏)，请确保您有一个自动系统来修补和更新所有服务器，并定期更新您的 ami 和轮换您的服务器，以防止长期 apt。

*   [ ]关闭未使用的服务和服务器。最安全的服务器是关机的服务器。安排开发服务器在不需要时关闭。

*   [ ]使用一个[入侵检测系统](https://en.wikipedia.org/wiki/Intrusion_detection_system)来最小化 [APTs](https://en.wikipedia.org/wiki/Advanced_persistent_threat) 。

## **拒绝服务保护**

*   [ ]确保对您的 API 的 DOS 攻击不会使您的站点瘫痪。至少，在较慢的 API 路径和认证相关的 API(如登录和令牌生成例程)上设置速率限制器。考虑在前端 API 上使用 CAPTCHA 来保护后端服务免受 DOS 攻击。

*   [ ]对用户提交的数据和请求的大小和结构实施健全性限制。

*   [ ]执行[混乱测试](https://boyter.org/2016/07/chaos-testing-engineering/)以确定您的服务在压力下的表现。

*   [ ]考虑通过像 [CloudFlare](https://www.cloudflare.com/) 这样的全局缓存代理服务，使用[分布式拒绝服务](https://en.wikipedia.org/wiki/Denial-of-service_attack) (DDOS)缓解。如果您遭受 DDOS 攻击，这可以打开，否则作为您的 DNS 查找功能。

## **黑掉自己**

*   [ ]审核您的设计和实施。

*   [ ]进行渗透测试——自己动手，但也请其他人进行 pen 测试。

*   [ ]在正常使用之外主动测试您的应用。考虑一下 [OWASP 测试清单](https://github.com/0xRadi/OWASP-Web-Checklist)来指导你的测试黑客。

## **事件应对**

*   [ ]就安全社会工程中使用的危险和技术对员工(尤其是高级员工)进行培训。

*   [ ]有一个描述您所防御的威胁的模型。它应该列出可能的威胁和行为者，并对其进行优先排序。

*   [ ]建立一个标准的电子邮件帐户和网页，专门供用户报告安全问题([security@example.com](mailto:security@example.com)和/security)。

*   [ ]有一个经过实践的安全事件计划。总有一天，你会需要它。

## 安全是一段旅程

最重要的是，记住安全性是一个旅程，不能在发货前就“固化”到产品中。我希望这个清单能够在整个开发生命周期中提示您提高服务的安全性。

该清单的第 1 版可在[V1 网站开发者安全清单](https://www.sensedeep.com/blog/posts/stories/web-developer-security-checklist-v1.html)中找到

## 关于 SenseDeep

SenseDeep 是 AWS 开发人员的一个观察平台，用于加速无服务器应用程序的交付和维护。SenseDeep 通过集成的无服务器开发人员工作室，在整个生命周期中帮助开发人员创建可观察、可靠和可维护的应用，该工作室包括设计和调试工具以及对无服务器应用的深入洞察。

要尝试 SenseDeep，请在浏览器中导航至:

[https://app.sensedeep.com](https://app.sensedeep.com)。

要了解有关 SenseDeep 的更多信息，请访问:

[https://www.sensedeep.com/product](https://www.sensedeep.com/product)。