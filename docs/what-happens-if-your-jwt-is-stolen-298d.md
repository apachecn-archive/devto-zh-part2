# 如果你的 JWT 被偷了会怎么样？

> 原文：<https://dev.to/oktadev/what-happens-if-your-jwt-is-stolen-298d>

[![Stolen JWT](../Images/bbacad0b12c70e0dd6e1d309a5591a38.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EJ-Laov8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/what-happens-if-your-jwt-is-stolen/stolen-jwt-813423a2a0c04d9c99e58aea4bede15081038d6e3e41b98c619734c1b8967512.png)

我们都知道如果我们的用户凭证(电子邮件和密码)被攻击者发现会发生什么:他们可以登录我们的帐户并造成严重破坏。但是许多现代应用程序都使用 JSON Web 令牌(JWT)来管理用户会话——如果 JWT 遭到破坏会发生什么？因为越来越多的应用程序使用基于令牌的身份验证，所以这个问题与开发人员的关系越来越密切，如果您正在构建任何一种使用基于令牌的身份验证的应用程序，理解这个问题非常重要。

为了帮助全面解释这些概念，我将带您了解什么是代币，如何使用代币，以及代币被盗后会发生什么。最后:我将介绍如果您的令牌被盗，您实际应该做什么，以及将来如何防止这种情况。

这篇文章的灵感来自于这个 [StackOverflow 问题](https://stackoverflow.com/questions/34259248/what-if-jwt-is-stolen)。我对这个问题的回答已经成为我迄今为止在 StackOverflow 上最受欢迎的回答之一！

## 什么是令牌？

[![Shrug](../Images/37acdd83c773e5b9008c936d65c30e26.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CaDbvtER--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/what-happens-if-your-jwt-is-stolen/shrug-1ad78de3fb2a4efbec8eb87a486bb48914454a642a4fb4d3d55dfe83db8a3d93.jpg)

web 开发环境中的令牌只不过是代表一个会话的任意值。令牌可以是类似“abc123”的字符串，也可以是类似“48ff 796 e-8c8a-46 B9-9f 25-f883c 14734 ea”的随机生成的 id。

令牌的目的是帮助服务器记住某人是谁。以 API 服务为例:如果您有一个允许您从服务器端应用程序与 API 服务对话的 API 键，API 服务使用该 API 键来“记住”您是谁，查找您的帐户详细信息，并允许(或不允许)您发出请求。在这个例子中，您的 API 密匙是您的“令牌”，它允许您访问 API。

然而，当今天大多数人谈论令牌时，他们实际上指的是 jwt(不管是好是坏)。

## 什么是 JSON Web 令牌(JWT)？

[![JSON Web Token](../Images/a78d3f30aca798bb98a2be418a8e9ea7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oclBPlK9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/what-happens-if-your-jwt-is-stolen/jwt-778843a03acacfc994840c1b614464e512ccf5f520240072b593f521d78ecba5.png)

JSON Web 令牌是特殊类型的令牌，其构造方式便于在 Web 上使用。他们有一些决定性的特征:

*   它们被表示为普通的字符串。这里有一个真实的 JWT:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IlJhbmRhbGwgRGVnZ2VzIiwiaWF0IjoxNTE2MjM5MDIyfQ.sNMELyC8ohN8WF_WRnRtdHMItOVizcscPiWsQJX9hmw 
```

因为 jwt 只是 URL 安全字符串，它们很容易通过 URL 参数等传递。

*   它们包含 JSON 编码的数据。这意味着您可以让您的 JWT 存储任意多的 JSON 数据，并且您可以将您的令牌字符串解码成一个 JSON 对象。这使得它们便于嵌入信息。
*   它们被加密签名了。理解这是如何工作的是[自己的话题](https://en.wikipedia.org/wiki/Digital_signature)。现在，只需知道这意味着任何拥有 JWT 的可信方都可以知道令牌是否被修改或更改。这意味着，如果您的应用程序或 API 服务生成一个令牌，表明某人是一个“免费”用户，而某人后来更改该令牌，表明他们是一个“管理员”用户，您将能够检测到这一点并采取相应的行动。这个属性使得 jwt 对于在难以建立信任的 web 上共享信息非常有用。

下面是一小段代码，它使用 [njwt](https://github.com/jwtk/njwt) 库在 JavaScript 中创建和验证了一个 JWT。这个例子纯粹是向您展示如何创建一个 JWT，在其中嵌入一些 JSON 数据，并验证它。

```
const njwt = require("njwt");
const secureRandom = require("secure-random");

// This is a "secret key" that the creator of the JWT must keep private.
var key = secureRandom(256, { type: "Buffer" });

// This is the JSON data embedded in the token.
var claims = {
  iss: "https://api.com",
  sub: "someuserid",
  scope: "freeUser",
  favoriteColor: "black"
};

// Create a JWT
var jwt = njwt.create(claims, key);

// Log the JWT
console.log(jwt);
// Jwt {
// header: JwtHeader { typ: 'JWT', alg: 'HS256' },
// body:
// JwtBody {
// iss: 'https://api.com',
// sub: 'someuserid',
// scope: 'freeUser',
// favoriteColor: 'black',
// jti: '903c5447-ebfd-43e8-8f4d-b7cc5922f5ec',
// iat: 1528824349,
// exp: 1528827949 },
// signingKey: <Buffer 9c e9 48 a7 b3 c9 87 be 5f 59 90 a5 08 02 9b 98 5c 5e 1c 29 3f b0 33 c5 8c c8 f9 c8 3e 35 f0 7c 20 a0 aa 65 cc 98 47 b6 31 c5 5c d6 4e 6e 25 29 2b d3 ... > }

// The JWT in compacted form (ready for sending over the network)
var token = jwt.compact();

// Log the compacted JWT
console.log(jwt.compact());
// eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2FwaS5jb20iLCJzdWIiOiJzb21ldXNlcmlkIiwic2NvcGUiOiJmcmVlVXNlciIsImZhdm9yaXRlQ29sb3IiOiJibGFjayIsImp0aSI6IjkwM2M1NDQ3LWViZmQtNDNlOC04ZjRkLWI3Y2M1OTIyZjVlYyIsImlhdCI6MTUyODgyNDM0OSwiZXhwIjoxNTI4ODI3OTQ5fQ.y7ad-nUsHAkI8a5bixYnr_v0vStRqnzsT4bbWGAM2vw

// Verify the JWT using the secret key
njwt.verify(token, key, (err, verifiedJwt) => {
  if (err) throw err;
  console.log("The JWT has been verified and can be trusted!");
  // The JWT has been verified and can be trusted!
}); 
```

## JSON Web 令牌是如何使用的？

[![How are JSON Web Tokens used?](../Images/4aa0dd56c51628766f386cd3901d73cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xmc1N-9c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/what-happens-if-your-jwt-is-stolen/how-are-jwts-used-916bd87556468089f02086c3900c8f30db0e48231272a374e7537b6eeca80338.jpg)

jwt 通常用作 web 应用程序、移动应用程序和 API 服务的会话标识符。但是，与传统的会话标识符不同，它只不过是指向服务器端实际用户数据的指针，jwt 通常直接包含用户数据。

jwt 近年来变得流行的主要原因是它们可以包含任意的 JSON 数据。JWT 相对于传统会话 ID 的优势在于:

*   jwt 是无状态的，可以直接包含用户数据
*   因为 jwt 是无状态的，所以不需要实现任何服务器端会话(没有会话数据库、会话缓存等)。)

因为 JWT 是无状态的，所以当服务器端应用程序接收到 JWT 时，它可以只使用创建它时使用的“密钥”来验证它——从而避免了与后端数据库或缓存对话的性能损失，这会增加每个请求的延迟。

也就是说，让我们看看 JWT 在现代 web 应用程序中通常是如何使用的。

1.  客户端(通常是浏览器或移动客户端)将访问某种登录页面
2.  客户端会将它们的凭证发送到服务器端应用程序
3.  服务器端应用程序将验证用户的凭证，通常是电子邮件地址和密码，然后生成包含用户信息的 JWT。嵌入在 JWT 中的信息通常是:
4.  用户的名字和姓氏
5.  用户的电子邮件地址或用户名
6.  用户的 ID(如有必要，用于服务器端查找)
7.  用户的权限(允许他们做什么？)
8.  与正在使用的应用程序相关的任何其他数据
9.  服务器端应用程序会将这个令牌返回给客户端
10.  然后，客户端将存储这个令牌，以便将来可以使用它来标识自己。对于 web 应用程序，这可能意味着客户端将令牌存储在 [HTML5 本地存储](https://www.rdegges.com/2018/please-stop-using-local-storage/)中。对于服务器端 API 客户端，这可能意味着将令牌存储在磁盘上或秘密存储中。
11.  当客户端将来向服务器发出请求时，它将在 [HTTP 授权头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization)中嵌入 JWT 来标识自己
12.  当服务器端应用程序收到一个新的请求时，它将检查 HTTP 授权头是否存在，如果存在，它将解析出令牌并使用“秘密密钥”验证它
13.  最后，如果令牌有效，服务器端应用程序将处理请求，循环将完成

简而言之:jwt 用于标识一个客户端。对客户来说，它们是通往王国的钥匙。

## 如果你的 JSON Web Token 被盗了会怎么样？

[![Angry Rage Face](../Images/96787c5e10cd76b87ce745d914bf6cbe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9VCuCM4w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/what-happens-if-your-jwt-is-stolen/angry-rage-face-ff14143170c6493b0d0090b1c7edbff81a8ed6c55325a25bb11c7ce3d12593fb.png)

总之:很烂，*真正的烂*。

因为 jwt 用于识别客户端，所以如果一个客户端被盗或被破坏，攻击者就可以完全访问用户的帐户，就像攻击者破坏了用户的用户名和密码一样。

例如，如果一个攻击者得到了你的 JWT，他们可能会开始向服务器发送请求，表明他们是你，并做一些事情，如更改服务，更新用户帐户等。一旦攻击者得到你的 JWT，游戏就结束了。

**但是**，有一件事让一个被盗的 JWT 比一个被盗的用户名和密码稍微差一点:*计时*。因为 JWTs 可以被配置为在一段时间(一分钟、一小时、一天等等)后自动过期，所以攻击者只能使用您的 JWT 来访问服务，直到它过期。

理论上，这听起来很棒，对吧？据说令牌身份验证使身份验证更加“安全”的方法之一是通过短期令牌。这是近年来基于令牌的身份认证真正起飞的核心原因之一:您可以自动终止令牌，并降低依赖永久缓存的“无状态”令牌的风险。

毕竟，在安全领域，依靠缓存数据来做出敏感决策，比如谁可以登录服务以及他们可以做什么，被认为是一件坏事。因为令牌是无状态的，并且允许相对于传统会话身份验证的一些速度改进，所以它们能够保持某种程度上“安全”的唯一方法是限制它们的寿命，以便它们在受到损害时不会造成太大的损害。

这里唯一的问题是，如果攻击者能够在第一时间窃取您的令牌，一旦您获得新令牌，他们很可能也会这样做。最常见的方式是通过中间人(MITM)连接或直接访问客户端或服务器。不幸的是，在这些场景中，即使是寿命最短的 jwt 也不会对您有任何帮助。

一般来说，令牌应该像密码一样受到保护。它们不应该公开共享，应该保存在安全的数据存储中。对于基于浏览器的应用程序，这意味着永远不要将令牌存储在 [HTML5 本地存储](https://www.rdegges.com/2018/please-stop-using-local-storage/)中，而是将令牌存储在 JavaScript 无法访问的服务器端 cookies 中。

一般来说，基于令牌的身份验证并不提供比依赖于不透明会话标识符的典型的基于会话的身份验证更多的安全性。虽然基于令牌的身份验证有很多使用案例，但是了解这项技术的工作原理以及您的弱点在哪里是非常重要的。

另一个要考虑的有趣的事情是，在某些情况下，被盗的 JWT 实际上比被盗的用户名和密码更糟糕。

让我们假设一下，你的用户名和密码已经被泄露了。在这种情况下，如果您登录的应用程序受到多因素身份验证的保护，攻击者需要绕过额外的身份验证机制才能访问您的帐户。

虽然猜测或强行破解用户名和密码是一个非常现实的场景，但是能够破坏用户的多因素身份验证设置可能非常困难。绕过基于 app 的授权、短信验证、face ID、touch ID 等因素。，比猜测用户密码更具挑战性。

正因为如此，*被入侵的 JWT 实际上比被入侵的用户名和密码*更具安全风险。想象一下上面的场景，用户登录的应用程序受到多重身份认证的保护。一旦用户登录并通过多因素验证后，他们将被分配一个 JWT 来证明他们的身份。如果 JWT 被盗，攻击者不再需要直接绕过 MFA(如果他们只有用户的用户名和密码，他们将不得不这样做)，他们现在可以作为用户直接发出请求，而无需额外的身份验证。相当大的风险。

## 如果你的 JWT 被盗了该怎么办

[![My JWT was stolen, what do I do?](../Images/3e6127db378ca82b346fbb2c92735baa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IBuK3oXU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/what-happens-if-your-jwt-is-stolen/what-do-i-do-d0f98ce43a73a34edca20f7d7f2a3f84bf2a1a47bb06e5fbb0d69b2b8b5762fa.jpg)

一旦 JWT 被盗，您将处于非常糟糕的境地:攻击者现在可以冒充客户端并在没有客户端同意的情况下访问您的服务。但是，即使你处在一个糟糕的情况下，你仍然要充分利用它。

如果客户的令牌被盗，可以采取以下几个步骤。这些建议并不适合每种类型的应用程序，但应该为您提供一些好主意，帮助您从此次安全事件中恢复过来:

*   **立即撤销受损令牌。**如果您使用服务器上的撤销列表来使令牌无效，撤销令牌可以立即将攻击者引导出您的系统，直到他们获得新的令牌。虽然这是一个临时的解决方案，但它会使攻击者的生活稍微困难一些。
*   强制您的客户立即更改密码。在网络或移动应用的环境中，强迫你的用户立即重置密码，最好是通过某种多因素认证流程，就像 [Okta 提供的那样](https://www.okta.com/products/adaptive-multi-factor-authentication/)。如果攻击者试图使用受损令牌修改用户登录凭据，强制用户更改其密码可能会阻止攻击者进入其帐户。通过要求多因素身份认证，您可以更加确信重置其凭据的用户就是他们自称的那个人，而不是攻击者。
*   检查客户的环境。用户的手机是否被盗，因此攻击者可以访问他们的预认证移动应用程序？客户端是否从手机或受感染的计算机等受损设备访问您的服务？发现攻击者是如何获得令牌的，是全面了解问题所在的唯一途径。
*   **检查您的服务器端环境。**攻击者能够破解您的令牌吗？如果是这样，这可能需要做更多的工作来修复，但是越早开始越好。

一旦您完成了这些步骤，您应该有望更好地理解令牌是如何被破坏的，以及需要做些什么来防止将来发生这种情况。

## 如何检测令牌泄露

[![Magnifying glass](../Images/22078e049f259fabc1f3f78ff2cfdc4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--04huqZME--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/what-happens-if-your-jwt-is-stolen/magnifying-glass-c3fc5120a04af94e5bc8a6ca3ab519c7a36a069aed0584c3bf04d27614099305.jpg)

当令牌泄露确实发生时，它可能会导致重大问题。特别是如果您(作为服务提供商)不能快速检测到攻击者已经损害了客户端的令牌。

如果您能够自动识别令牌何时受损，会怎么样？这将极大地提高服务的安全性，因为您可以主动阻止可疑的请求被满足，从而保护您的服务和用户。

虽然不容易，但这是绝对可能的。像 [TensorFlow](https://www.tensorflow.org/) 这样的现代机器学习工具包允许你建立功能性(尽管复杂)的管道来检测不寻常的模式并主动控制局面。

例如，您可以使用机器学习来检测不寻常的客户位置。假设您运行一个网站，您的用户从旧金山登录，几个小时以来一直在发出请求。如果您注意到请求在短时间后开始来自不同的地理区域，您可以立即阻止这些请求被满足，撤销令牌，并联系用户重置其密码，等等。

以类似的方式，您可以使用机器学习来检测异常的客户端行为。如果令牌受到损害，攻击者很可能会采取措施以某种方式滥用您的服务。如果您的用户通常每分钟在您的网站上发出五个请求，但突然您注意到用户每分钟发出 50 多个请求的大幅上升，这可能是一个很好的指标，表明攻击者已经获得了用户的令牌，因此您可以撤销令牌并联系用户重置其密码。

通过机器学习进行模式检测和识别是处理这些更复杂问题的一种奇妙的现代方法。

这正是我们在 [Okta](https://developer.okta.com/) 这里所做的——我们运行 [API 服务](https://developer.okta.com/signup/)，允许你在我们的服务中存储用户账户，我们提供开发者库来处理诸如认证、授权、社交登录、单点登录、多因素认证等事情。当用户登录由 Okta 支持的应用程序时，我们会分析大量数据点，以检测帐户是否受到威胁，提示进行多因素身份验证，执行用户拓展等。

积极主动地保护您的安全涉及到许多复杂的问题，但是做好准备总比毫无准备好得多。

**不要脸的塞**:如果你还没检查过我们的 API 服务，它是免费使用的，真的很好玩！您可以在这里创建一个账户:【https://developer.okta.com/signup/[。而且……如果你真的想尝试一下，我很想听听你的想法，所以请](https://developer.okta.com/signup/)[给我打电话](mailto:randall.degges@okta.com)，告诉我关于 Okta、令牌认证或 JSON Web 令牌的任何反馈。最后，请[关注@oktadev Twitter](https://twitter.com/oktadev) —我们在 Twitter 上发布了许多类似这样的有趣的安全相关话题。

黑客快乐，

兰德尔