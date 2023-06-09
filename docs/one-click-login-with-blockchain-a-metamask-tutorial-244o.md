# 区块链一键式登录:元掩码教程

> 原文：<https://dev.to/amaurym/one-click-login-with-blockchain-a-metamask-tutorial-244o>

*这篇文章最初出现在 [Toptal 博客](https://www.toptal.com/ethereum/one-click-login-flows-a-metamask-tutorial)上。*

在线用户越来越抵制传统的电子邮件/密码注册程序。事实证明，通过脸书、谷歌或 GitHub 的一键式社交登录功能是一个更可取的选择。然而，这是有代价的。

**社交媒体登录整合的优势:**

*   不再有繁琐的填表。
*   无需记住另一个用户名/密码对。
*   整个过程只需几秒钟，而不是几分钟。

**社交媒体登录整合的缺点:**

*   由于用户的信息是从外部提供商加载的，这就引发了对提供商如何使用所有这些个人数据的巨大隐私担忧。例如，在撰写本文时，脸书正面临[数据隐私问题](https://www.reuters.com/article/us-facebook-privacy-costs-analysis/privacy-issues-emerge-as-major-business-risk-for-facebook-idUSKBN1GW01F)。

本文介绍了一种新的登录方法:使用[元掩码扩展](https://metamask.io)的一键式密码安全登录流程，所有数据都存储在我们自己的后端。我们称之为:“用元掩码登录”。

一张图胜过千言万语，下面是我们将要构建的登录流程的演示:

[![An animation showing the MetaMask Login Demo.](img/e66bd5c2292b5dbb52c3718131a3742a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oImpkeDo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://uploads.toptal.io/blog/image/125794/toptal-blog-image-1522395423193-b3227ea1f43c6cbb9f78e090bd7bb2ee.gif)

好看吗？我们开始吧！

## 如何为一键登录流程使用元掩码

基本思想是，通过使用私钥对一段数据进行签名，可以很容易地从加密的角度证明帐户的所有权。如果您成功地签署了我们后端生成的一份精确的数据，那么后端将认为您是该公共地址的所有者。因此，我们可以构建一个基于消息签名的身份验证机制，将用户的公共地址作为他们的标识符。

如果看起来不清楚，没关系，因为我们会一点一点地解释:

*   [元掩码浏览器扩展](#the-metamask-browser-extension)
*   [登录流程如何工作](#how-the-login-flow-works)
*   [登录流程的工作原理](#why-the-login-flow-works)
*   [让我们一起建造它](#lets-build-it-together)
*   [今天就可以生产了](#its-production-ready-today)
*   [手机上的缺点](#shortcomings-on-mobile)

请注意，虽然我们将使用连接到[以太坊](https://www.toptal.com/ethereum)区块链(MetaMask，以太坊公共地址)的工具，但这个登录过程实际上并不需要区块链:它只需要它的加密功能。也就是说，随着元掩码成为[这样一个流行的扩展](https://twitter.com/metamask_io/status/942816957920829440)，现在似乎是引入这个登录流的好时机。

## 元掩码浏览器扩展

如果您已经知道什么是元掩码，可以跳过这一部分。

[MetaMask](https://metamask.io) 是一个浏览器插件，可作为 [MetaMask Chrome 扩展](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn)或 [Firefox 插件](https://addons.mozilla.org/en-US/firefox/addon/ether-metamask/)使用。在其核心，它充当一个以太坊钱包:通过安装它，你将获得一个独特的以太坊公共地址，用它你可以开始发送和接收以太或令牌。

但是 MetaMask 做的不仅仅是以太坊钱包。作为一个浏览器扩展，它可以与您正在浏览的当前网页进行交互。它通过在你访问的每个网页中注入一个名为 [web3.js](https://github.com/ethereum/web3.js/) 的 JavaScript 库来做到这一点。一旦注入，`web3`对象将通过该网站的 JavaScript 代码中的`window.web3`可用。如果你安装了 MetaMask，在 Chrome 或 Firefox DevTools 控制台中输入`window.web3`就可以看到这个物体的样子。

Web3.js 是以太坊区块链的 JavaScript 接口。有以下功能:

*   获取链的最新块(`web3.eth.getBlockNumber`)
*   检查 MetaMask ( `web3.eth.coinbase`)上的当前活动帐户
*   获取任意账户的余额(`web3.eth.getBalance`)
*   发送交易(`web3.eth.sendTransaction`)
*   用当前帐户的私钥签署消息(`web3.personal.sign`)
*   ...还有[多得多的](https://github.com/ethereum/wiki/wiki/JavaScript-API)

安装 MetaMask 后，任何前端代码都可以访问所有这些功能，[与区块链](https://www.toptal.com/ethereum-smart-contract)交互。它们被称为 *dapps* 或 *DApps* (用于分散式应用——有时甚至被称为“应用”)。

web3.js 中的大部分函数都是读函数(get block，get balance 等。)，而`web3`会立即给出回应。然而，一些功能(如`web3.eth.sendTransaction`和`web3.personal.sign`)需要当前帐户用它的私钥签署一些数据。这些函数触发 MetaMask 显示一个确认屏幕，再次检查用户是否知道她或他正在签署什么。

让我们看看如何使用元掩码来实现这一点。要进行一个简单的测试，请在 DevTools 控制台中粘贴以下行:

```
web3.personal.sign(web3.fromUtf8("Hello from Toptal!"), web3.eth.coinbase, console.log); 
```

Enter fullscreen mode Exit fullscreen mode

这个命令的意思是:用 coinbase 账号(即当前账号)给我的消息签名，从 utf8 转换成 hex，作为回调，打印签名。将出现一个元掩码弹出窗口，如果您对其进行签名，将会打印签名的消息。

[![MetaMask confirmation popup](img/51778a21e24e5ed823b09b2851635397.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hdet0axU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://uploads.toptal.io/blog/image/125793/toptal-blog-image-1522395402560-444f356ee68405e0968139c7703811ef.png)

我们将在登录流程中使用`web3.personal.sign`。

关于这一部分的最后一点:MetaMask 将 web3.js 注入到你当前的浏览器中，但是实际上还有其他独立的浏览器也注入 web3.js，例如 [Mist](https://github.com/ethereum/mist) 。然而，在我看来，MetaMask 今天为普通用户探索 dapps 提供了最好的 UX 和最简单的过渡。

## 登录流程如何工作

先说*如何*。*如何*将有希望让你相信它是安全的，所以我将保持*为什么*部分简短。

如概述中所述，我们将忘记区块链。我们有一个传统的 Web 2.0 客户端-服务器 RESTful 架构。我们将做一个假设:所有访问我们前端网页的用户都安装了元掩码。基于这一假设，我们将展示无密码加密安全登录流程是如何工作的。

### 第一步:修改用户模型(后端)

首先，我们的`User`模型需要有两个新的必填字段:`publicAddress`和`nonce`。另外，`publicAddress`需要是唯一的。您可以保留通常的`username`、`email`和`password`字段——特别是如果您想实现与电子邮件/密码登录并行的元掩码登录——但是它们是可选的。

注册过程也会略有不同，因为如果用户希望使用元掩码登录，注册时`publicAddress`将是必填字段。请放心，用户永远不需要手动输入他们的`publicAddress`，因为它可以通过`web3.eth.coinbase`获取。

### 第二步:生成随机数(后端)

对于数据库中的每个用户，在`nonce`字段中生成一个随机字符串。比如`nonce`可以是一个大的随机整数。

### 步骤 3:用户获取他们的 Nonce(前端)

在我们的前端 JavaScript 代码中，假设元掩码存在，我们可以访问`window.web3`。因此，我们可以调用`web3.eth.coinbase`来获取当前元掩码帐户的公共地址。

当用户点击登录按钮时，我们向后端发出一个 API 调用来检索与他们的公共地址相关联的随机数。类似带有过滤参数`GET /api/users?publicAddress=${publicAddress}`的路由应该可以做到。当然，由于这是一个未经认证的 API 调用，所以后端应该配置为只显示这条路由上的公共信息(包括`nonce`)。

如果前面的请求没有返回任何结果，这意味着当前的公共地址还没有注册。我们需要首先通过`POST /users`创建一个新账户，在请求体中传递`publicAddress`。另一方面，如果有结果，那么我们存储它的`nonce`。

### 第四步:用户签署随机数(前端)

一旦前端在先前 API 调用的响应中接收到`nonce`，它将运行以下代码:

```
web3.personal.sign(nonce, web3.eth.coinbase, callback); 
```

Enter fullscreen mode Exit fullscreen mode

这将提示 MetaMask 显示一个用于签署消息的确认弹出窗口。随机数将显示在这个弹出窗口中，这样用户就知道她或他没有签署一些恶意数据。

当她或他接受它时，回调函数将被调用，带签名的消息(称为`signature`)作为参数。然后前端对`POST /api/authentication`进行另一个 API 调用，传递一个带有`signature`和`publicAddress`的主体。

### 第五步:签名验证(后端)

当后端接收到一个`POST /api/authentication`请求时，它首先从数据库中获取与请求体中给出的`publicAddress`相对应的用户。特别地，它获取相关联的现时。

有了随机数、公共地址和签名，后端就可以[用密码验证](https://en.wikipedia.org/wiki/Digital_signature)随机数已经被用户正确签名。如果是这种情况，那么用户已经证明了对公共地址的所有权，我们可以认为她或他已经过身份验证。然后，可以将 JWT 或会话标识符返回到前端。

### 第六步:更改 Nonce(后端)

为了防止用户使用相同的签名再次登录(以防泄露)，我们确保下次相同的用户想要登录时，她或他需要签署一个新的 nonce。这是通过为该用户生成另一个随机的`nonce`并将其保存到数据库中来实现的。

*瞧！这就是我们如何管理一次性签名的无密码登录流程。*

## 登录流程的工作原理

根据定义，身份验证实际上只是帐户所有权的证明。如果你使用一个公共地址来唯一地标识你的账户，那么证明你拥有它就很简单了。

为了防止黑客获得某个特定消息和您的签名(但不是您的实际私钥)，我们强制消息签名为:

1.  由后端提供，并且
2.  有规律的变化

在我们的解释中，我们在每次成功登录后更改它，但是也可以设想基于时间戳的机制。

[![Overview of the six steps of the MetaMask login flow.](img/522c5563024a11cee83f8d294483ac73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2Sr70s1x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://uploads.toptal.io/blog/image/125792/toptal-blog-image-1522395353253-70fb1c40e9527154c2774507b63eac63.png)

## 让我们一起建造吧

在这一部分，我将一个接一个地完成上面的六个步骤。我将展示一些代码片段，说明我们如何从头开始构建这个登录流，或者将它集成到现有的后端，而不需要太多的努力。

我为本文创建了一个小的演示应用程序。我使用的堆栈如下:

*   Node.js、Express 和 SQLite(通过 Sequelize ORM)在后端实现 RESTful API。如果认证成功，它将返回一个 JWT。
*   在前端反应单页应用程序。

我尽量少用图书馆。我希望代码足够简单，以便您可以轻松地将其移植到其他技术栈。

整个项目可以在[这个 GitHub 库](https://github.com/amaurym/login-with-metamask-demo)中看到。这里举办了一场演示[。](http://amaurym.com/login-with-metamask-demo/)

### 第一步:修改用户模型(后端)

需要两个字段:`publicAddress`和`nonce`。我们将`nonce`初始化为一个随机的大数字。每次成功登录后，应更改该号码。我还在这里添加了一个可选的`username`字段，用户可以对其进行更改。

```
const User = sequelize.define('User', {
  nonce: {
    allowNull: false,
    type: Sequelize.INTEGER.UNSIGNED,
    defaultValue: () => Math.floor(Math.random() * 1000000) // Initialize with a random nonce
  },
  publicAddress: {
    allowNull: false,
    type: Sequelize.STRING,
    unique: true,
    validate: { isLowercase: true }
  },
  username: {
    type: Sequelize.STRING,
    unique: true
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，我将`publicAddress`字段设置为小写。一个更严格的实现将增加一个验证功能，检查这里的所有地址是否都是有效的以太坊地址。

### 第二步:生成随机数(后端)

这是在上面模型定义中的`defaultValue()`函数中完成的。

### 步骤 3:用户获取他们的 Nonce(前端)

下一步是在后端添加一些样板代码来处理`User`模型上的 CRUD 方法，我们在这里不会这么做。

切换到前端代码，当用户点击登录按钮时，我们的`handleClick`处理程序执行以下操作:

```
class Login extends Component {
  handleClick = () => {
    // --snip--
    const publicAddress = web3.eth.coinbase.toLowerCase();

    // Check if user with current publicAddress is already present on back end
    fetch({% raw %}`${process.env.REACT_APP_BACKEND_URL}/users?publicAddress=${publicAddress}`{% endraw %})
      .then(response => response.json())
      // If yes, retrieve it. If no, create it.
      .then(
        users => (users.length ? users[0] : this.handleSignup(publicAddress))
      )
      // --snip--
  };

  handleSignup = publicAddress =>
    fetch({% raw %}`${process.env.REACT_APP_BACKEND_URL}/users`{% endraw %}, {
      body: JSON.stringify({ publicAddress }),
      headers: {
        'Content-Type': 'application/json'
      },
      method: 'POST'
    }).then(response => response.json());
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们用`web3.eth.coinbase`检索元掩码活动帐户。然后我们检查这个`publicAddress`是否已经存在于后端。如果用户已经存在，我们就检索它；如果不存在，我们就用`handleSignup`方法创建一个新的帐户。

### 第四步:用户签署随机数(前端)

让我们用我们的`handleClick`方法前进。我们现在拥有一个由后端给出的用户(无论是检索到的还是新创建的)。特别是，我们有他们的`nonce`和`publicAddress`。因此，我们准备使用`web3.personal.sign`用与这个`publicAddress`相关联的私钥对 nonce 进行签名。这在`handleSignMessage`功能中完成。

请注意，`web3.personal.sign`将字符串的十六进制表示作为它的第一个参数。我们需要使用`web3.fromUtf8`将 UTF 8 编码的字符串转换成十六进制格式。此外，我决定签署一个更加用户友好的句子，而不是只签署 nonce，因为它将显示在元掩码确认弹出窗口中:`I am signing my once-time nonce: ${nonce}`。

```
class Login extends Component {
  handleClick = () => {
    // --snip--
    fetch({% raw %}`${process.env.REACT_APP_BACKEND_URL}/users?publicAddress=${publicAddress}`{% endraw %})
      .then(response => response.json())
      // If yes, retrieve it. If no, create it.
      .then(
        users => (users.length ? users[0] : this.handleSignup(publicAddress))
      )
      // Popup MetaMask confirmation modal to sign message
      .then(this.handleSignMessage)
      // Send signature to back end on the /auth route
      .then(this.handleAuthenticate)
      // --snip--
  };

  handleSignMessage = ({ publicAddress, nonce }) => {
    return new Promise((resolve, reject) =>
      web3.personal.sign(
        web3.fromUtf8({% raw %}`I am signing my one-time nonce: ${nonce}`{% endraw %}),
        publicAddress,
        (err, signature) => {
          if (err) return reject(err);
          return resolve({ publicAddress, signature });
        }
      )
    );
  };

  handleAuthenticate = ({ publicAddress, signature }) =>
    fetch({% raw %}`${process.env.REACT_APP_BACKEND_URL}/auth`{% endraw %}, {
      body: JSON.stringify({ publicAddress, signature }),
      headers: {
        'Content-Type': 'application/json'
      },
      method: 'POST'
    }).then(response => response.json());
} 
```

Enter fullscreen mode Exit fullscreen mode

当用户成功地签署了消息后，我们转移到`handleAuthenticate`方法。我们只需向后端的`/auth`路由发送一个请求，发送我们的`publicAddress`以及用户刚刚签名的消息的`signature`。

#### 第五步:签名验证(后端)

这是稍微复杂一点的部分。后端在`/auth`路由上接收到一个包含一个`publicAddress`和一个`signature`的请求，需要验证这个`publicAddress`是否已经签署了正确的`nonce`。

第一步是从数据库中检索具有所述`publicAddress`的用户；只有一个，因为我们将`publicAddress`定义为数据库中的唯一字段。然后我们将消息`msg`设置为“我正在签署我的……”，就像步骤 4 中的前端一样，使用这个用户的 nonce。

下一步是验证本身。这里涉及到一些加密技术。如果你喜欢冒险，我推荐你阅读更多关于[椭圆曲线签名](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)的内容。

总结一下这个块，它做的是，给定我们的`msg`(包含`nonce`)和我们的`signature`,`ecrecover`函数输出用于签署`msg`的公共地址。如果它与来自请求体的`publicAddress`匹配，那么发出请求的用户成功地证明了他们对`publicAddress`的所有权。我们认为它们是经过认证的。

```
User.findOne({ where: { publicAddress } })
  // --snip--
  .then(user => {
    const msg = {% raw %}`I am signing my one-time nonce: ${user.nonce}`{% endraw %};

    // We now are in possession of msg, publicAddress and signature. We
    // can perform an elliptic curve signature verification with ecrecover
    const msgBuffer = ethUtil.toBuffer(msg);
    const msgHash = ethUtil.hashPersonalMessage(msgBuffer);
    const signatureBuffer = ethUtil.toBuffer(signature);
    const signatureParams = ethUtil.fromRpcSig(signatureBuffer);
    const publicKey = ethUtil.ecrecover(
      msgHash,
      signatureParams.v,
      signatureParams.r,
      signatureParams.s
    );
    const addressBuffer = ethUtil.publicToAddress(publicKey);
    const address = ethUtil.bufferToHex(addressBuffer);

    // The signature verification is successful if the address found with
    // ecrecover matches the initial publicAddress
    if (address.toLowerCase() === publicAddress.toLowerCase()) {
      return user;
    } else {
      return res
        .status(401)
        .send({ error: 'Signature verification failed' });
    }
  }) 
```

Enter fullscreen mode Exit fullscreen mode

身份验证成功后，后端会生成一个 JWT 并发送给客户端。这是一个经典的认证方案，所以我不会把代码放在这里。

#### 第六步:更改 Nonce(后端)

出于安全原因，最后一步是更改 nonce。身份验证成功后，添加以下代码:

```
// --snip--
.then(user => {
  user.nonce = Math.floor(Math.random() * 1000000);
  return user.save();
})
// --snip-- 
```

Enter fullscreen mode Exit fullscreen mode

没那么难吧。同样，如果你想看看整个应用程序是如何连接的(JWT 生成、CRUD 路由、本地存储等。)，可以随意看一下 [GitHub 回购](https://github.com/amaurym/login-with-metamask-demo)。

## 今天就可以生产了

虽然区块链可能有其缺陷，仍然处于婴儿阶段，但我不能强调这种登录流程如何能够在任何现有的网站上实现。这里列出了为什么这种登录流程优于电子邮件/密码*和*社交登录的理由:

*   **更高的安全性**:通过公钥加密证明所有权无疑比通过电子邮件/密码或第三方证明所有权更安全——尤其是因为 MetaMask 将凭证存储在本地计算机上，而不是在线服务器上，这使得攻击面更小。
*   **简化的 UX** :这是一次点击(好吧，也许是两次点击)的登录流程，在几秒钟内完成，不需要键入或记住任何密码。
*   **增加隐私**:不需要电子邮件，不涉及第三方。

当然，元掩码登录流可以很好地与其他传统登录方法并行使用。需要在每个帐户和它持有的公共地址之间进行映射。

但是这种登录流程并不适合所有人:

*   **用户需要安装元掩码**:没有元掩码或支持`web3`的浏览器，这个登录流程显然无法工作。如果你的观众对加密货币不感兴趣，他们甚至会考虑安装 MetaMask 的可能性很小。随着最近的加密热潮，让我们希望我们正在走向一个 Web 3.0 互联网。
*   **一些工作需要在后端完成**:正如我们已经看到的，实现这个登录流程的简单版本非常简单。然而，要将它集成到一个现有的复杂系统中，需要在所有涉及身份验证的方面进行一些更改:注册、数据库、身份验证路由等。尤其是因为每个帐户都将与一个或多个公共地址相关联。
*   它在手机上不工作:这值得有自己的一节——继续读下去。

## 手机上的缺点

正如我们所见，`web3`是这个登录流的先决条件。在桌面浏览器上，MetaMask 注入它。然而，移动浏览器上没有扩展，所以这个登录流程不能在 mobile Safari、Chrome 或 Firefox 上开箱即用。有一些独立的移动浏览器注入了`web3`——基本上是浏览器中的元掩码。在我写这篇文章的时候，它们还处于非常早期的阶段，但是如果你有兴趣，可以看看[密码](https://www.cipherbrowser.com)、[状态](https://status.im)和 [Toshi](https://www.toshi.org) 。“使用元掩码登录”适用于这些移动浏览器。

关于移动应用，答案是肯定的，登录流程是可行的，但是还有很多基础工作要做。基本上，你需要自己重建一个简单的以太坊钱包。这包括公共地址生成、种子字恢复和安全私钥存储，以及`web3.personal.sign`和确认弹出窗口。幸运的是，有图书馆可以帮助你。重点关注的关键领域自然是安全性，因为应用程序本身持有私钥。在桌面浏览器上，我们将这个任务委托给元掩码。

因此，我认为简单的答案是否定的，这种登录流程在今天的移动设备上不起作用。人们正在朝这个方向努力，但目前简单的解决方案仍然是移动用户的并行传统登录方法。

## 让您的用户使用元掩码登录

在本文中，我们介绍了一个单击式的、密码安全的登录流程，不涉及第三方，称为“使用元掩码登录”。我们解释了后端生成的随机随机数的数字签名如何证明帐户的所有权，从而提供身份验证。我们还探索了这种登录机制与传统的电子邮件/密码或社交登录相比在桌面和移动设备上的利弊。

尽管今天这种登录流的目标受众仍然很少，我真诚地希望你们中的一些人受到启发，在自己的 web 应用程序中提供与传统登录流并行的 MetaMask 登录——如果你们这样做，我很想知道。如果你有任何问题，请在下面的评论中联系我们。