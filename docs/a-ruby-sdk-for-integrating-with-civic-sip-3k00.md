# 一个用于集成 Civic SIP 的 ruby SDK

> 原文：<https://dev.to/binarystorms/a-ruby-sdk-for-integrating-with-civic-sip-3k00>

## 什么是思域

[Civic](https://www.civic.com/) 是下一代基于区块链的安全身份管理平台。它允许对用户进行身份验证，而不需要传统的物理 id、基于知识的身份验证、用户名/密码和二元硬件令牌。Civic 的安全身份平台(SIP)使用经过验证的身份在 web 和移动应用程序上进行多因素身份验证，无需用户名或密码。SIP 为合作伙伴提供以下功能:

*   安全的公共或私人 2FA 用户登录
*   已验证用户加入定制流程

## 与思域相融

目前只有 Civic 托管选项可用于合作伙伴集成。这是最简单的集成途径，因为它提供了类似于传统 OAuth2 授权代码流的流程，由 Civic 扮演授权服务器的角色。该选项提供了一个安全的解决方案，并最大限度地减少了合作伙伴所需的服务器端开发。

以下用户注册示例解释了一般的 Civic 托管选项代码流:

[![Civic SIP flow](img/0473eb0d78dbf80239ec347b3d82f541.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cf-BDqr3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ooz30lz3hjab4h5jt00.png)

如上所述，在与公民 SIP 服务的安全交互中涉及的步骤数量可能令人望而生畏。

这就是服务器端 ruby SDK 派上用场的地方。

## 开源的 Ruby SDK

我想介绍[一个开源的 Ruby SDK，用于与 Civic 的 SIP](https://github.com/BinaryStorms/civic-sip-ruby-sdk) 交互。该 SDK 抽象出 JWT 编码、解码、验证和 AES 加密/解密的所有细节。

如果您的 ruby 项目利用了这个 SDK，那么整个 Civic SIP 集成流程将如下所示:

*   **设置前端**。

```
<link rel="stylesheet" href="https://hosted-sip.civic.com/css/civic-modal.min.css">
<script src="https://hosted-sip.civic.com/js/civic.sip.min.js"></script> 
```

*   **初始化前端 JS 库**。

```
var civicSip = new civic.sip({ appId: 'YOUR_CIVIC_APPLICATION_ID' }); 
```

*   **设置签到按钮**。

```
<button id="signupButton" class="civic-button-a medium" type="button">
    <span>Log in with Civic</span>
</button> 
```

*   **设置事件处理**。

```
var button = document.querySelector('#signupButton');
    button.addEventListener('click', function () {
        civicSip.signup({ style: 'popup', scopeRequest: civicSip.ScopeRequests.BASIC_SIGNUP });
});

// Listen for data
civicSip.on('auth-code-received', function (event) {
    /*
        event: {
            event: "scoperequest:auth-code-received",
            response: "eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NksifQ.eyJqdGkiOiI2Y2EwNTEzMi0wYTJmLTQwZjItYTg2Yi03NTkwYmRjYzBmZmUiLCJpYXQiOjE0OTQyMjUxMTkuMTk4LCJleHAiOjE0OTQyMjUyOTkuMTk4LCJpc3MiOiJjaXZpYy1zaXAtaG9zdGVkLXNlcnZpY2UiLCJhdWQiOiJodHRwczovL3BoNHg1ODA4MTUuZXhlY3V0ZS1hcGkudXMtZWFzdC0xLmFtYXpvbmF3cy5jb20vZGV2Iiwic3ViIjoiY2l2aWMtc2lwLWhvc3RlZC1zZXJ2aWNlIiwiZGF0YSI6eyJjb2RlVG9rZW4iOiJjY2E3NTE1Ni0wNTY2LTRhNjUtYWZkMi1iOTQzNjc1NDY5NGIifX0.gUGzPPI2Av43t1kVg35diCm4VF9RUCF5d4hfQhcSLFvKC69RamVDYHxPvofyyoTlwZZaX5QI7ATiEMcJOjXRYQ",
            type: "code"
        }
    */

    // encoded JWT Token is sent to the server
    var jwtToken = event.response;

    // Your function to pass JWT token to your server
    sendAuthCode(jwtToken);
  });

  civicSip.on('user-cancelled', function (event) {
    /*
        event:
        {
          event: "scoperequest:user-cancelled"
        }
    */
   });

  civicSip.on('read', function (event) {
    /*
        event:
        {
          event: "scoperequest:read"
        }
    */
  });

   // Error events.
   civicSip.on('civic-sip-error', function (error) {
      // handle error display if necessary.
      console.log(' Error type = ' + error.type);
      console.log(' Error message = ' + error.message);
   }); 
```

*   **完成服务器端的流程**。在前端获得的 JWT 令牌是临时的，类似于 OAuth 授权许可。这个 JWT 令牌需要通过服务器端 SDK 进行*交换*，以完成该过程并接收用户数据。

```
require 'civic_sip_sdk'

# appId - your Civic application id
# env - your Civic app environment, :dev or :prod (default)
# private key - your Civic private signing key
# secret - your Civic secret
client = CivicSIPSdk.new_client('appId', :env, 'private key', 'secret')
user_data = client.exchange_code(jwt_token: 'your token from Civic frontend JS lib') 
```

*   **访问用户数据**。可以通过两种方式访问用户数据；或者通过迭代所有用户数据项的列表，或者通过标签名访问特定的用户数据项。
    *   通过迭代所有项目

```
# Civic userId value
user_id = user_data.user_id
# get a list of all the user data items
data_items = user_data.data_items
# you can access all the attributes in each data item
an_item = data_items.first
label = an_item.label
value = an_item.value
is_valid = an_item.is_valid
is_owner = an_item.is_owner 
```

```
* by label name 
```

```
an_item = user_data.by_label('contact.personal.email')
label = an_item.label
value = an_item.value
is_valid = an_item.is_valid
is_owner = an_item.is_owner 
```

## 下一步

如果你正在用 ruby 开发一个产品，那么 Civic SIP 是为你的用户提供安全身份服务的好方法。ruby SDK 会简化集成过程。