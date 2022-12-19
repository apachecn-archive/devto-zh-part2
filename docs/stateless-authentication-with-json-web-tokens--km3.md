# 使用 JSON Web 令牌的无状态身份验证

> 原文：<https://dev.to/yos/stateless-authentication-with-json-web-tokens--km3>

> 在 [yos.io](https://yos.io/) 找到更多有趣的文章

[![Stateless Authentication with JSON Web Tokens](../Images/8fbfe77461ebb222a478345b58bb4f68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bJHgfreb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/DBtJbeO.jpg)

无论你是在编写一个公共 API 还是一个内部微服务，获得正确的身份验证都可以决定你的 API 的成败。让我们来看看一个基于 JSON Web 令牌的认证系统。

我们将从基本的身份验证和 JWT 概念开始，然后通过大量代码示例详细介绍身份验证服务的设计。

> 在我们开始之前，一些定义是适当的:
> 
> *   **凭证**:描述身份的事实
> *   **认证**:验证凭证以识别实体
> *   **授权**:验证一个实体被允许访问一个资源或者执行一个动作

## 什么是 JSON Web 令牌？

JSON Web 令牌(JWT 发音为“jot”)是一种紧凑且独立的方式，用于安全地传输信息，并以 JSON 对象的形式表示各方之间的声明。

这是一个编码的 JSON Web 令牌:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI1MWQ4NGFjMS1kYjMxLTRjM2ItOTQwOS1lNjMwZWJiYjgzZGYiLCJ1c2VybmFtZSI6Imh1bnRlcjIiLCJzY29wZXMiOlsicmVwbzpyZWFkIiwiZ2lzdDp3cml0ZSJdLCJpc3MiOiIxNDUyMzQzMzcyIiwiZXhwIjoiMTQ1MjM0OTM3MiJ9.cS5KkPxtEJ9eonvsGvJBZFIamDnJA7gSz3HZBWv6S1Q 
```

Enter fullscreen mode Exit fullscreen mode

如图所示的 JSON Web 令牌是一个由三个组件**组成的字符串，每个组件由一个`.`(句点)字符分隔。** 

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJqdGkiOiI1MWQ4NGFjMS1kYjMxLTRjM2ItOTQwOS1lNjMwZWJiYjgzZGYiLCJ1c2VybmFtZSI6Imh1bnRlcjIiLCJzY29wZXMiOlsicmVwbzpyZWFkIiwiZ2lzdDp3cml0ZSJdLCJpc3MiOiIxNDUyMzQzMzcyIiwiZXhwIjoiMTQ1MjM0OTM3MiJ9
.
cS5KkPxtEJ9eonvsGvJBZFIamDnJA7gSz3HZBWv6S1Q 
```

Enter fullscreen mode Exit fullscreen mode

`Base64Url`解码一个 JSON Web 令牌会得到以下结果:

```
{
  "alg": "HS256",
  "typ": "JWT"
}
.
{
  "jti": "51d84ac1-db31-4c3b-9409-e630ebbb83df",
  "username": "hunter2",
  "scopes": ["repo:read", "gist:write"],
  "iss": "1452343372",
  "exp": "1452349372"
}
.
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
) 
```

Enter fullscreen mode Exit fullscreen mode

JSON Web Tokens 由以下三个组件组成:**头**、**负载**和**签名**。令牌的构造如下:

1.  您生成了一个任意 JSON 数据的声明(**有效负载**，在我们的例子中，它包含了用于身份验证的所有必需的用户信息。一个**报头**通常定义签名算法`alg`和令牌类型`typ`。

2.  你用一些元数据来修饰它，比如索赔什么时候到期，受众是谁等等。这些被称为*声明*，在 [JWT IETF 草案](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)中定义。

3.  数据(包括**报头**和**有效载荷**)然后用基于散列的消息认证码(HMAC)秘密进行加密签名。这个**签名**被用来验证 JWT 的发送者就是它所说的那个人，并确保消息没有被以这种方式改变。

4.  然后，对**报头**、**有效载荷**和**签名**进行`Base64`编码，并用句点连接在一起以分隔字段，这就产生了我们在第一个示例中看到的令牌。

> jwt 也可以使用秘密(使用 HMAC 算法)或使用 RSA 的公钥/私钥对进行签名。

出于身份验证的目的，JWT 充当凭据/身份对象，客户端必须向网关守护设备显示该对象，以验证您是否被允许访问您想要访问的受保护资源。它可以由可信方签名，并由网守验证。

> JSON Web 令牌可以跨不同的编程语言工作。您应该能够找到可以用来签名和验证为您的堆栈编写的令牌的客户端。

## 认证流程

使用 jwt 的一个主要用例是验证请求。用户登录后，每个后续请求都可以包含访问以前无法访问的受保护资源和服务的 JWT。

为了说明，让我们设想一个包含用户受保护资源的一组微服务的身份验证层。

我们的认证流程发生在以下各方之间:

*   **资源所有者**(用户):拥有被共享资源的一方。让我们称我们的用户**为汤姆**。
*   **资源服务器**:保存受保护资源的服务。我们的 **WalletService** 持有钱包资源，这是一个用户的数字钱包。
*   **授权服务器**:验证用户身份的服务。让我们称之为**授权服务**。
*   **客户端**:代表资源所有者向资源服务器发出请求的应用(web/mobile/others)。让我们有一个 **WalletApp** Android 应用程序。

> 如果您熟悉 OAuth2，我们的流程类似于[资源所有者密码凭证授予](https://oauthlib.readthedocs.org/en/latest/oauth2/grants/password.html)流程。根据您的用例，其他流可能更适合您的应用程序。

我们的整个流程如下:

1.  资源所有者 Tom 希望通过客户端查看他的数字钱包的内容。
2.  客户端与 WalletService 对话，请求 Tom 的钱包资源。
3.  不幸的是，钱包是一种受保护的资源。客户端需要传递访问令牌才能继续。
4.  客户端与 AuthService 对话，请求访问令牌。AuthService 通过要求用户的凭证来响应。
5.  客户端将资源所有者 Tom 重定向到 AuthService，这给 Tom 提供了拒绝或接受客户端访问请求的选项。
6.  AuthService 验证 Tom 的凭证，将她重定向回客户机，并向客户机授予一个授权码。
7.  客户端向 AuthService 提供授权代码，如果成功，则向客户端返回访问令牌(JWT)。
8.  WalletApp 向 WalletService 提供访问令牌，请求 Tom 的钱包资源。每当客户端想要访问受保护的路由或资源时，它应该发送 JWT，通常在使用`Bearer`模式的`Authorization`报头中，例如`Authorization: Bearer <token>`
9.  WalletService 验证令牌，解码 JWT，并解析其内容。
10.  (可选，参见[撤销令牌](#revoking-tokens) ) WalletService 要求 AuthService 验证令牌。
11.  如果访问令牌对所请求的操作和资源有效，WalletService 将 Tom 的钱包返回给 WalletApp 客户端。
12.  沃勒塔普给汤姆看他的钱包。

> 请注意，资源所有者不会直接向客户端共享他们的凭据。相反，用户通知授权者，客户端可以访问他们请求的任何内容，并且客户端使用授权码单独进行身份验证。有关更多细节，请查看 [OpenID 连接](http://openid.net/specs/openid-connect-core-1_0.html)规范。

在本文中，我们主要关注第 8 步到第 12 步。

## 最低可行的认证服务

让我们使用 plain old Node + Express 为上面的流程开发一个认证服务。当然，您可以随意使用自己的认证服务。

我们至少需要一个端点:

| HTTP 动词 | 上呼吸道感染 | 描述 |
| --- | --- | --- |
| 邮政 | /会话 | 注册 |

```
// Authentication Service API Login endpoint

var _ = require('underscore');
var Promise = require('bluebird');
var express = require('express');
var router = express.Router();

var models = require('../models');
var User = models.User;
var JWT = require('../utils/jwt');

// Login
router.post('/sessions', function(req, res, next) {
  var params = _.pick(req.body, 'username', 'password', 'deviceId');
  if (!params.username || !params.password || !params.deviceId) {
    return res.status(400).send({error: 'username, password, and deviceId ' +
                                'are required parameters'});
  }

  var user = User.findOne({where: {username: params.username}});
  var passwordMatch = user.then(function(userResult) {
    if (_.isNull(userResult)) {
      return res.status(404).send({error: 'User does not exist'});
    }
    return userResult.comparePassword(params.password);
  });

  Promise.join(user, passwordMatch, function(userResult, passwordMatchResult) {
    if (!passwordMatchResult) {
      return res.status(403).send({
        error: 'Incorrect password'
      });
    }

      var userKey = uuid.v4();
      var issuedAt = new Date().getTime();
      var expiresAt = issuedAt + (EXPIRATION_TIME * 1000);

      var token = JWT.generate(user.username, params.deviceId, userKey, issuedAt, expiresAt);

      return res.status(200).send({
            accessToken: token;
      });
  })
    .catch(function(error) {
      console.log(error);
      next(error);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
//lib/utils/jwt.js

var _ = require('underscore');
var config = require('nconf');
var jsrsasign = require('jsrsasign');

var sessionKey = require('../utils/sessionKey');
var JWT_ENCODING_ALGORITHM = config.get('jwt:algorithm');
var JWT_SECRET_SEPARATOR = config.get('jwt:secret_separator');

function JWT() {
  this.secretKey = config.get('jwt:secret');
}

// Generate a new JWT
JWT.prototype.generate = function(user, deviceId, userKey, issuedAt,
                                  expiresAt) {
  if (!user.id || !user.username) {
    throw new Error('user.id and user.username are required parameters');
  }

  var header = {
    alg: JWT_ENCODING_ALGORITHM, typ: 'JWT'
  };
  var payload = {
    username: user.username,
    deviceId: deviceId,
    jti: sessionKey(user.id, deviceId, issuedAt),
    iat: issuedAt,
    exp: expiresAt
  };
  var secret = this.secret(userKey);
  var token = jsrsasign.jws.JWS.sign(JWT_ENCODING_ALGORITHM,
                         JSON.stringify(header),
                         JSON.stringify(payload),
                         secret);
  return token;
};

// Token Secret generation
JWT.prototype.secret = function(userKey) {
  return this.secretKey + JWT_SECRET_SEPARATOR + userKey;
};

module.exports = new JWT(); 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们现在可以在成功登录后返回访问令牌。在接下来的章节中，我们将介绍我们的认证系统的附加功能，以及编写一个认证中间件，我们可以轻松地使用它来保护未来微服务的路线。

但是首先，让我们了解一下为什么我们使用 jwt 而不是常规的明文令牌。

## 使用 jwt 进行认证的好处

与不透明的 OAuth2 `Bearer`令牌相比，使用 JSON Web 令牌作为身份对象有几个优点:

**1。细粒度的访问控制**:您可以在*令牌本身*中指定详细的访问控制信息，作为其有效负载的一部分。与创建具有非常具体的权限的 AWS 安全策略一样，您可以将令牌限制为只授予对单个资源的读/写访问权限。相比之下，API 键倾向于进行粗略的全有或全无访问。

[![](../Images/020abe2a7baffe73f15148ec225e872b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o0giU7qe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://help.github.com/assets/images/help/settings/token_scopes.gif)

您可以用包含一组带有 jwt 的动态范围的私有声明来填充您的令牌。比如:

```
{
  "jti": "51d84ac1-db31-4c3b-9409-e630ebbb83df",
  "username": "hunter2",
  "scopes": ["repo:read", "gist:write"],
  "iss": "1452343372",
  "exp": "1452349372"
} 
```

Enter fullscreen mode Exit fullscreen mode

您的认证中间件可以解析这个 JWT 元数据并执行验证*，而不需要*向授权服务器发出请求。API 端点将简单地检查是否存在正确的范围属性，如下所示。

我们在上一节中已经介绍了这一点，并附有代码示例。

**2。可内省的**:JSON Web 令牌带有一个类似头部的元数据，可以方便地进行客户端验证，不像普通文本`Bearer` OAuth 令牌，不调用我们的数据库就无法解码和检查。

**3。Expirable** : JSON Web 令牌可以通过`exp`属性拥有内置的过期机制。`exp`(到期时间)声明确定了到期时间，在该时间或之后，不得接受 JWT 进行处理。

**4。无状态**:完成一个特定请求所需的所有信息都随请求一起发送，包括一个`Authorization` HTTP 头，它包含我们的 JWT，作为一个“身份对象”因为有效载荷包含了我们认证用户所需的所有信息，所以我们可以避免重复调用我们的数据库。

**5。加密**:虽然 JWT 的签名可以防止恶意方篡改它，但令牌的报头只进行`Base64`编码。在处理令牌中的机密标识符时，应该使用`AES`对令牌进行加密。

此时你可能会想:

> “哇，太棒了！我可以实现完全无状态的身份验证，而不必存储任何会话信息！”

上述情况是真实的，因为您*可以*对`exp`到期时间声明执行客户端验证，以使过期的令牌无效。

然而，我们注意到一些我们当前设计没有解决的问题:

> 当你想让一个用户退出一个应用程序时会发生什么？假设您更新了令牌的模式。如何处理仍未过期的旧令牌？当您将此更新部署到应用程序时，如何使当前会话失效？当你存储会话时。

此时，*我们没有办法让我们的授权服务器使尚未过期的会话无效*。

## 撤销令牌

纯无状态方法的一个问题是，我们无法在颁发的令牌过期之前撤销/使其无效。换句话说，*我们不能手动注销一个用户*。如果恶意方设法获得令牌，并且我们知道恶意方拥有令牌，那么我们就只能坐以待毙。我们没有办法取消对已经发行的令牌的访问。

我们可以拥有在验证期间清除任何过期会话令牌的客户端逻辑。然而，客户端安全性是不够的。为了帮助防止令牌滥用，我们需要能够撤销已经颁发的令牌。

> 恶意方有可能看到您的服务的请求头。Tou 应该使用 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) 进行客户端-服务器和服务内部的通信，以确保没有人监听你的网络请求。话虽如此，我们希望我们的身份验证系统是安全的，即使攻击者能够拦截客户端和服务器之间的网络通信。

根据您的使用情况，我们可以采用两种方法来支持两种不同的令牌失效功能。这两种方法都需要使用 Redis 等附加存储来存储某种形式的令牌标识符。

> Redis 是一个非常棒的键值存储，对于保存临时数据(比如我们的令牌)非常有用。它包括令牌自动删除或过期等功能，可以处理大量写入，并且是可水平扩展的。

这两种方法都需要我们的验证中间件向授权服务器请求令牌验证。让我们来看看如何实现它们:

**1。为了能够撤销属于单个用户**的所有令牌，我们可以简单地用属于该用户的私有秘密对 jwt 进行签名。您可以动态生成这些密码，也可以使用密码的散列。

然后，在我们的令牌验证过程中，我们可以从 DB/service 中检索这个私有秘密(在我们的例子中是从`KeyService`)来验证令牌的签名。

可以通过更改或删除该用户的密码来撤销令牌，从而使属于该用户的所有已颁发令牌失效。

**2。为了能够撤销单个令牌**，用户可以在不同的设备上拥有多个令牌，这将要求我们为每个 JWT 生成一个唯一的`jti`标识符，我们可以使用它作为`KeyService`中的标识符来检索动态生成的、会话特定的秘密，该秘密是为了签名和验证单个令牌而创建的。

```
 // Verify JWT
  KeyService.get(payload.jti)
    .then(function(userKey) {
      var authenticated = JWT.verify(token, userKey);
      if (authenticated) {
        return next();
      }

      return next(new Error('403 Invalid Access Token'));
    }); 
```

Enter fullscreen mode Exit fullscreen mode

标识符值的分配方式必须确保相同的值被意外分配给不同的数据对象的概率可以忽略不计；如果应用程序使用多个发行者，还必须防止不同发行者产生的值之间的冲突。有一种方法可以帮助减少冲突，那就是使用`uuids`而不是`integers`作为你的标识符。

> 对于这两种方法，我们还需要防止[重放攻击](https://en.wikipedia.org/wiki/Replay_attack)，方法是将唯一标识符包含在`jti`中，并将时间戳包含在`iat`(发布于)声明中，作为 JWT 有效负载的一部分。这确保了生成的每个令牌都是唯一的。

我们需要添加额外的端点:

| HTTP 动词 | 上呼吸道感染 | 描述 |
| --- | --- | --- |
| 邮政 | /会话 | 注册 |
| 得到 | /sessions/:id | 检索特定于用户/会话的私有秘密 |
| 删除 | /sessions/:id | 注销 |

我们的认证中间件将主要使用`GET`端点来检索用于签署 JWT 的秘密，并验证签名是否有效。

`DELETE`端点将在特定设备处改变或移除用于用户会话的秘密，使得 JWT 签名验证失败并且触发`403 Forbidden`响应。

我们还创建了一个服务包装器，用方法`get`、`set`和`delete` :
来存储用于签署 jwt 的用户/会话特定秘密

```
// KeyService.js, a key storage backed by Redis

// KeyService stores and manages user-specific keys used to sign JWTs
var redis = require('redis');
var Promise = require('bluebird');
var config = require('nconf');
var uuid = require('node-uuid');

var JWT = require('../utils/jwt');
var EXPIRATION_TIME = config.get('key_service:expires_seconds');
var sessionKey = require('../utils/sessionKey');
Promise.promisifyAll(redis.RedisClient.prototype);

function KeyService() {
  this.client = redis.createClient(config.get('key_service:port'),
                                   config.get('key_service:host'));
  this.client.on('connect', function() {
    console.log('Redis connected.');
  });
  console.log('Connecting to Redis...');
}

// Retrieve a JWT user key
KeyService.prototype.get = function(sessionKey) {
  return this.client.getAsync(sessionKey);
};

// Generate and store a new JWT user key
KeyService.prototype.set = function(user, deviceId) {
  var userKey = uuid.v4();
  var issuedAt = new Date().getTime();
  var expiresAt = issuedAt + (EXPIRATION_TIME * 1000);

  var token = JWT.generate(user, deviceId, userKey, issuedAt, expiresAt);
  var key = sessionKey(user.id, deviceId, issuedAt);

  var setKey = this.client.setAsync(key, userKey);
  var setExpiration = setKey.then(this.client.expireAsync(key,
                                  EXPIRATION_TIME));
  var getToken = setExpiration.then(function() {
    return token;
  });

  return getToken;
};

// Manually remove a JWT user key
KeyService.prototype.delete = function(sessionKey) {
  return this.client.delAsync(sessionKey);
};

module.exports = new KeyService(); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，过期机制是内置的，它利用 Redis' [`EXPIRE`](https://redis.io/commands/expire) 功能来自动删除已过期的会话，从而使任何用该秘密签名的已颁发令牌无效。

这是我们的主路由器，更新后可以处理额外的端点并与`KeyService` :
对话

```
// Authentication Service API endpoints

var _ = require('underscore');
var Promise = require('bluebird');
var express = require('express');
var router = express.Router();

var models = require('../models');
var User = models.User;
var KeyService = require('../services/KeyService');
var ErrorMessage = require('../utils/error');

// Register
router.post('/users', function(req, res, next) {
  var params = _.pick(req.body, 'username', 'password');
  if (!params.username || !params.password) {
    return res.status(400).send({error: 'username and password ' +
                                'are required parameters'});
  }

  User.findOrCreate({
    where: {username: params.username},
    defaults: {password: params.password}
  })
  .spread(function(user, created) {
    if (!created) {
      return res.status(409).send({error: 'User with that username ' +
                                  'already exists.'});
    }
    res.status(201).send(user);
  })
  .catch(function(error) {
    res.status(400).send(ErrorMessage(error));
  });
});

// Login
router.post('/sessions', function(req, res, next) {
  var params = _.pick(req.body, 'username', 'password', 'deviceId');
  if (!params.username || !params.password || !params.deviceId) {
    return res.status(400).send({error: 'username, password, and deviceId ' +
                                'are required parameters'});
  }

  var user = User.findOne({where: {username: params.username}});
  var passwordMatch = user.then(function(userResult) {
    if (_.isNull(userResult)) {
      return res.status(404).send({error: 'User does not exist'});
    }
    return userResult.comparePassword(params.password);
  });

  Promise.join(user, passwordMatch, function(userResult, passwordMatchResult) {
    if (!passwordMatchResult) {
      return res.status(403).send({
        error: 'Incorrect password'
      });
    }

    return KeyService.set(userResult, params.deviceId)
        .then(function(token) {
          res.status(200).send({
            accessToken: token
          });
        });
  })
    .catch(function(error) {
      console.log(error);
      next(error);
    });
});

// Get Session
router.get('/sessions/:sessionKey', function(req, res, next) {
  var sessionKey = req.params.sessionKey;
  if (!sessionKey) {
    return res.status(400).send({error: 'sessionKey is a required parameters'});
  }

  KeyService.get(sessionKey)
    .then(function(result) {
      if (_.isNull(result)) {
        return res.status(404).send({error: 'Session does not exist or has ' +
                                    'expired. Please sign in to continue.'});
      }
      res.status(200).send({userKey: result});
    })
    .catch(function(error) {
      console.log(error);
      next(error);
    });
});

// Logout
router.delete('/sessions/:sessionKey', function(req, res, next) {
  var sessionKey = req.params.sessionKey;
  if (!sessionKey) {
    return res.status(400).send({error: 'sessionKey is a required parameter'});
  }

  KeyService.delete(sessionKey)
    .then(function(result) {
      if (!result) {
        return res.status(404).send();
      }
      res.status(204).send();
    })
    .catch(function(error) {
      console.log(error);
      next(error);
    });
});

module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

## 更新认证流程

下面是我们更新后的支持撤销已颁发令牌的流程:

[![](../Images/329f11332546ea985253c5acf1afdb82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CM3RgfYf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/rYWf4tn.png)

我们在令牌验证过程中引入了一些额外的步骤(这发生在我们的中间件中)，令牌验证过程与外部私有秘密存储器`KeyService`通信，以检索解码和验证 JWT 签名所需的秘密。

正如我们已经讨论过的，这允许我们引入过期和手动撤销已经颁发的令牌的能力，代价是增加了一些复杂性。

## 一个最小可行的认证中间件

除了我们的 AuthService，我们可以也应该编写一个配套的 Node.js 模块，其他开发人员可以使用它轻松地将身份验证添加到他们的微服务中。例如:

```
var auth = require('auth');
router.post('/protected', auth.isAuthenticated, function(req, res, next) {
  res.status(200).send();
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 仅供参考，经过 isAuthenticated 的中间件会检查该路由的每个传入请求中的 Authorization: Bearer 头是否有效。下面提供了源代码。

你也可以像这样保护所有路线:

```
var auth = require('auth');
app.use(auth.isAuthenticated); 
```

Enter fullscreen mode Exit fullscreen mode

`isAuthenticated`中间件可以这样写:

```
// index.js

var base64url = require('base64url');
var JWT = require('./lib/utils/jwt');
var KeyService = require('./lib/services/KeyService');

function isAuthenticated(req, res, next) {
  // Guard clauses
  var authorization = req.headers.authorization;
  if (!authorization || !(authorization.search('Bearer ') === 0)) {
    return next(new Error('401 Missing Authorization Header'));
  }
  var token = authorization.split(' ')[1];
  if (!token) {
    return next(new Error('401 Missing Bearer Token'));
  }

  // Unpack JWT
  var components = token.split('.');
  var header = JSON.parse(base64url.decode(components[0]));
  var payload = JSON.parse(base64url.decode(components[1]));
  var signature = components[2];

  // Verify JWT
  KeyService.get(payload.jti)
    .then(function(userKey) {
      var authenticated = JWT.verify(token, userKey);
      if (authenticated) {
        return next();
      }

      return next(new Error('403 Invalid Access Token'));
    });
}

module.exports = {
  isAuthenticated: isAuthenticated
}; 
```

Enter fullscreen mode Exit fullscreen mode

> `KeyService`是 Redis 存储上的包装器，用于存储特定于会话的用户密钥，由令牌的`jti`声明中包含的`uuid`标识符索引。我们以前见过这种情况，除了为我们的中间件定义的操作是严格只读的。

```
// KeyService stores and manages user-specific keys used to sign JWTs
var redis = require('redis');
var Promise = require('bluebird');
var config = require('nconf');

Promise.promisifyAll(redis.RedisClient.prototype);

function KeyService() {
  this.client = redis.createClient(config.get('key_service:port'),
                                   config.get('key_service:host'));
  this.client.on('connect', function() {
    console.log('Redis connected.');
  });
  console.log('Connecting to Redis...');
}

// Retrieve a JWT user key
KeyService.prototype.get = function(sessionKey) {
  return this.client.getAsync(sessionKey);
};

module.exports = new KeyService(); 
```

Enter fullscreen mode Exit fullscreen mode

`JWT`是`jsrsasign`加密库的轻量级包装器。我们使用`jsrsassign`加密库来验证我们的 jwt:

> 选择用于签署和验证 jwt 的加密库时要小心。务必检查 [jwt.io](https://jwt.io/) 是否有任何安全漏洞。

```
// lib/utils/jwt.js

var _ = require('underscore');
var config = require('nconf');
var jsrsasign = require('jsrsasign');
var base64url = require('base64url');

var JWT_ENCODING_ALGORITHM = config.get('jwt:algorithm');
var JWT_SECRET_SEPARATOR = config.get('jwt:secret_separator');

function JWT() {
  this.secretKey = config.get('jwt:secret');
}

JWT.prototype.verify = function(token, userKey) {
  var secret = this.secret(userKey);
  var isValid = jsrsasign.jws.JWS.verifyJWT(token,
                                            secret,
                                            {
                                              alg: [JWT_ENCODING_ALGORITHM],
                                              verifyAt: new Date().getTime()});
  return isValid;
};

JWT.prototype.secret = function(userKey) {
  return this.secretKey + JWT_SECRET_SEPARATOR + userKey;
};

module.exports = new JWT(); 
```

Enter fullscreen mode Exit fullscreen mode

> 问:为什么在计算`secret`时，我们同时使用全局秘密和用户特定秘密？
> 
> 答:有了一个全局秘密，我们就可以通过简单地改变这个秘密值来轻易地使属于所有用户的所有令牌无效，从而使所有已经颁发的令牌的 JWT 签名无效。

以这种方式为横切关注点(如身份验证)编写模块，可以节省开发时间和未来微服务的工作量。随着您编写越来越多的可重用模块，您可以用越来越丰富的功能快速引导新服务。共享模块还有助于保持所有不同服务的行为一致。

## 其他 JWT 用例

JSON Web Tokens 可以在各方之间安全地传输信息，因为它的签名可以让我们确信它的发送者就是我们所期望的人。涉及 JWTs 的其他用例包括在重置密码链接中作为令牌。我们可以使用 JWTs 创建签名的超链接，而不需要在数据库中存储密码重置令牌。

## 在关闭

我介绍了一种使用 JSON Web 令牌构建身份验证层的方法。我们还检查了一些设计决策，以帮助防止一些安全漏洞。

虽然 JWTs 看起来是一种相当合理的身份验证方法，但重要的是，我们不要忽视我们从旧的身份验证方案中吸取的经验教训，这些经验是我们多年的战斗经验。

通过这个过程，我希望我已经与您分享了使用 JWTs 的客户端身份验证方案有其自身的风险和局限性，需要在实现之前进行彻底的调查。

请在下面的评论中告诉我你的想法！

## 补充阅读

*   [JWT IETF 草案](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)
*   [jwt.io](https://jwt.io/)
*   [JWT 闪电谈话](https://youtu.be/eWUkxzyB1Rk)
*   [关于代币和饼干你应该知道的十件事](https://auth0.com/blog/2014/01/27/ten-things-you-should-know-about-tokens-and-cookies/)
*   [微服务的无状态认证](https://www.slideshare.net/alvarosanchezmariscal/stateless-authentication-for-microservices)
*   [拥有 JWT 的无状态令牌](https://jonatan.nilsson.is/stateless-tokens-with-jwt/)
*   [使用 JSON Web 令牌作为 API 密钥](https://auth0.com/blog/2014/12/02/using-json-web-tokens-as-api-keys/)

> 在 [yos.io](https://yos.io/) 找到更多有趣的文章