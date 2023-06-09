# 保护 C#/。具有由 NodeJS 签名的公钥-私钥签名的 jwt 的. NET WebAPI

> 原文：<https://dev.to/codeprototype/securing-cnet-webapi-with-public-private-key-signed-jwts-signed-by-nodejs-43g6>

*本文在[媒体](https://medium.com/@kevinle/securing-c-net-webapi-with-public-private-key-signed-jwts-signed-by-nodejs-4fd60d97b7f4)T3 交叉发布*

在本文中，我将展示如何实现和保护 C#/。NET(以下我只说 C#) WebAPI。为了保护 WebAPI，我们将使用 JWT。JWT 由 NodeJS 后端使用私钥签名。WebAPI 将使用公钥验证 JWT。

我想说清楚，所以让我澄清一些我更喜欢使用的术语。我说的`client`是指手机 app、web 应用、Postman 等客户端应用。另一方面，`user`是使用这些`clients`的人类。当`client`向服务器发送登录请求时，它实际上是在代表`user`做这件事，后者在移动应用程序上输入他/她的名字并点击提交按钮。

因此，客户端首先向 NodeJS 服务器的`/login`端点发出请求。这个 NodeJS 服务器是授权服务器。它的工作是在登录正确的情况下发布 JWT。假设是这样，一旦客户端获得 JWT，客户端就可以将这个 JWT 存储在内存、本地存储、cookie 或其他地方。现在，客户端想要访问 C# WebAPI 提供的资源。因此，当它发送一个请求时，它会在请求头的`Authorization`属性中包含一个 JWT。C# WebAPI 是资源服务器或提供者。它的工作是提供资源。但是，只有当它能够验证 JWT 时，它才会这样做。

在序列图中:

[![Sequence diagram](img/e77eeed8c7f85e3f02b48da4a22414e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C1THJETN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/882vk6pv0fayuvr7hbx0.png)

授权服务器(NodeJS)和资源提供者(C# WebAPI)可以运行在两个完全不同的服务器或云上。我们也可以使用授权服务器(NodeJS)和资源提供者(C# WebAPI)都知道的共享秘密，而不是像他的文章中那样使用公钥私钥来签名和验证 JWT。然而，`shared secret`方法不如`public private key`方法有效，原因如下。

1.  有 2 个潜在故障点，而不是 1 个。授权服务器或资源提供者都可能泄露共享秘密。另一方面，私钥仍然可能被泄露，但是只有一个实体知道私钥。

2.  如果有多个资源提供者，共享一个秘密只会增加潜在故障点的数量。

3.  为每个资源提供者提供不同的秘密是一种选择，但是在某些情况下，我们无法控制资源提供者，那么我们必须处理共享秘密的分发问题。

无论如何，让我们生成公钥和私钥。

## 生成公钥私钥

在 Windows 电脑上，

```
$ ssh-keygen -t rsa -b 4096 -f jwtRS256.key
$ openssl rsa -in jwtRS256.key -pubout -outform PEM -out jwtRS256.key.pub 
```

Enter fullscreen mode Exit fullscreen mode

信用:[https://gist.github.com/ygotthilf/baa58da5c3dd1f69fae9](https://gist.github.com/ygotthilf/baa58da5c3dd1f69fae9)

在苹果电脑上，

```
$ openssl genrsa -out jwtRS256.key 4096
$ openssl rsa -in jwtRS256.key -pubout -out jwtRS256.key.pub 
```

Enter fullscreen mode Exit fullscreen mode

信用:[https://gist . github . com/h-sakano/84d C4 BD 8371 be 4 f 0 e 8 dddc 9388974348 # file-file 0-sh](https://gist.github.com/h-sakano/84dc4bd8371be4f0e8dddc9388974348#file-file0-sh)

文件`jwtRS256.key.pub`是公钥，将作为静态文件。这将在后面展示。文件`jwtRS256.key`是私钥，我们将用它来签署 JWT。

## 在 NodeJS 中签下 JWT

我们将编写一个 NodeJS 服务器代码，它有一个名为`/login`的端点，并接受 POST 请求。POST 请求的主体包含 JSON 格式的用户 id 和密码。

运行`npm init`并安装必要的软件包:

```
$ npm init -y
$ npm i --save express path body-parser 
```

Enter fullscreen mode Exit fullscreen mode

创建一个`public`和一个`private`文件夹，将公钥`jwtRS256.key.pub`和私钥`jwtRS256.key`文件分别移动到这两个文件夹中。

创建一个名为`server.js`的文件，内容如下图所示。

此时，文件结构和`server.js`文件应该如下所示:

[![Figure 1](img/c7f2279bbcfddd4c7824445e13bab819.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WeLHvkrz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/it5mzc834w8ke7i6hoaf.png)

(不能复制粘贴，不用担心，这段代码到时会完成并可用。请继续阅读)

我们还没有真正做任何事情。但是你可以看到占位符。如果输入了正确的`userid`和`password`，我们将生成一个签名的 JWT，并返回一个状态代码 200。否则，我们返回状态 401。检查`userid`和`password`的逻辑由您决定。

如果此时在本地运行 NodeJS 服务器，可以用 Postman 或者你的浏览器去地址[http://localhost:8080/jwtrs 256 . key . pub](http://localhost:8080/jwtRS256.key.pub)，公钥是现成的。

现在我们安装`jsonwebtoken`包，这是签署 JWT 的本质，也是`fs`。

```
npm i --save jsonwebtoken
npm i --save fs 
```

Enter fullscreen mode Exit fullscreen mode

现在完整的代码:

```
const express = require('express');
const path = require('path');
const bodyParser = require('body-parser');
const fs = require('fs');
const jwt = require('jsonwebtoken');

const app = express();
const router = express.Router();

const port = process.env.PORT || 8080;

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.static(path.join(__dirname, 'public')));

app.post('/login', (req, res) => {
    const { userid, password } = req.body;

    if (userid === 'kevin' && password === '123456') { //replace with your logic
        const privateKey = fs.readFileSync(__dirname + '/private/jwtRS256.key', 'utf8');
        const issuer = 'Name-of-Issuer-that-you-want';
        const subject = 'Subject-name';
        const audience = 'Your-audience';
        const expiresIn = '24h';
        const algorithm = 'RS256'; //important
        const payload = { userid };

        const signOptions = {
            issuer,
            subject,
            audience,
            expiresIn,
            algorithm
        }

        const token = jwt.sign(payload, privateKey, signOptions);

        console.log(token);

        res.status(200).json( {token} );
    } else {
        res.status(401).json('Incorrect userid and/or password');
    }
});

app.listen(port);
module.exports = app; 
```

Enter fullscreen mode Exit fullscreen mode

只有 3 行比其余的更重要。第一行是读取私钥(`const privateKey = ...`)。第二行是将`'RS256'`分配给`algorithm`。第三行是令牌被签名的那一行(`const token = jwt.sign(...)`)

现在启动 Postman，发出一个 POST 请求，如下图所示，您将在响应中得到一个 JWT。

[![Login to NodeJS](img/4728b5108bc3c155ea3469b0112ae321.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--13--McNp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z1wlq9l3pniffb2j4bcm.png)

## 验证 C# WebAPI 中的 JWT

如您所见，响应中返回了一个 JWT。在哪里存储这个 JWT 取决于你开发的是哪种客户端 app，移动、web 应用还是电子桌面等。

接下来我将展示如何保护 C# WebAPI 资源。

所以在 Visual Studio 2017 或 2015 中，只需使用 WebAPI 项目模板创建一个新的解决方案。

您将看到一个名为`ValuesController.js`的文件，其中包含为您生成的以下代码。

```
public class ValuesController : ApiController
{
    // GET api/values
    public async Task<IEnumerable<string>> Get()
    {
        await Task.Delay(10);
        return new string[] { "value1", "value2" };
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个端点`GET api/values`不受保护。让我们继续保护这个端点。

通过添加一行
来修改该文件

```
public class ValuesController : ApiController
{
    // GET api/values
    [JwtAuthorization]
    public async Task<IEnumerable<string>> Get()
    {
        await Task.Delay(10);
        return new string[] { "value1", "value2" };
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

是我们将要编写的一个类。它是`AuthorizationFilterAttribute`的子类。在我展示它之前，我们必须安装一个名为`BouncyCastle`的 Nuget 包。

[![BouncyCastle](img/7aac59d94622a1e8b8bdfd8a1c147bce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ap9OiLSH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4aek9fmytmznhlnpnoi6.png)

然后让我们写一个读取公钥的类。请记住，公钥是一个静态文件，位于地址[http://localhost:8080/jwtrs 256 . key . pub](http://localhost:8080/jwtRS256.key.pub)

因为 public 只需要被读取一次，所以我只为它创建了 singleton。

```
public class PublicKeyStore
{
    private readonly string URL = "http://localhost:8080/jwtRS256.key.pub";
    private static PublicKeyStore _instance;
    private string _publicKey;

    public static async Task<PublicKeyStore> GetInstance()
    {
        if (_instance == null)
        {
            _instance = new PublicKeyStore();
            await _instance.FetchPublicKey();
        }

        return _instance;
    }

    public string PublicKey
    {
        get { return _publicKey; }
    }

    private async Task FetchPublicKey()
    {
        using (HttpClient client = new HttpClient())
        {
            using (HttpResponseMessage response = await client.GetAsync(URL))
            using (Stream receiveStream = await response.Content.ReadAsStreamAsync())
            {
                using (StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8))
                {
                    _publicKey = readStream.ReadToEnd();
                }
            }
        }
    }

    private PublicKeyStore()
    {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们进入最重要的部分，验证 JWT。正如我提到的，这将在基类`AuthorizationFilterAttribute`
的`overrides``OnAuthorization(HttpActionContext actionContext)`的`JwtAuthorization`类中完成

```
public class JwtAuthorizationAttribute : AuthorizationFilterAttribute
{
    public override async void OnAuthorization(HttpActionContext actionContext)
    {
        try
        {
            if (actionContext.Request.Headers.Authorization == null)
            {
                actionContext.Response = actionContext.Request.CreateResponse(HttpStatusCode.Unauthorized);
            }
            else
            {
                var bearer = actionContext.Request.Headers.Authorization.Scheme;
                var jwt = actionContext.Request.Headers.Authorization.Parameter;                    
                if (bearer.ToLower() != "bearer" || jwt == null)
                {
                    actionContext.Response = actionContext.Request.CreateResponse(HttpStatusCode.Unauthorized);
                }
                else
                {
                    var publicKeyStore = await PublicKeyStore.GetInstance();
                    var publicKey = publicKeyStore.PublicKey;

                    var pr = new PemReader(new StringReader(publicKey));
                    var asymmetricKeyParameter = (AsymmetricKeyParameter)pr.ReadObject();
                    var rsaKeyParameters = (RsaKeyParameters)asymmetricKeyParameter;
                    var rsaParams = DotNetUtilities.ToRSAParameters((RsaKeyParameters)asymmetricKeyParameter);                        
                    var rsaCsp = new RSACryptoServiceProvider();
                    rsaCsp.ImportParameters(rsaParams);

                    string[] jwtParts = jwt.Split('.');
                    if (jwtParts.Length < 3)
                    {
                        actionContext.Response = actionContext.Request.CreateResponse(HttpStatusCode.Unauthorized);
                    }
                    else
                    {
                        var sha256 = SHA256.Create();
                        var hash = sha256.ComputeHash(Encoding.UTF8.GetBytes(jwtParts[0] + '.' + jwtParts[1]));

                        var rsaDeformatter = new RSAPKCS1SignatureDeformatter(rsaCsp);
                        rsaDeformatter.SetHashAlgorithm("SHA256");

                        if (!rsaDeformatter.VerifySignature(hash, FromBase64Url(jwtParts[2])))
                        {
                            actionContext.Response = actionContext.Request.CreateResponse(HttpStatusCode.Unauthorized);
                        }
                        else
                        {
                            byte[] data = Convert.FromBase64String(jwtParts[1]);
                            var payload = Encoding.UTF8.GetString(data);
                            //Check for time expired claim or other claims
                        }
                    }

                    base.OnAuthorization(actionContext);
                }
            }
        }
        catch (Exception)
        {
            actionContext.Response = actionContext.Request.CreateResponse(HttpStatusCode.Unauthorized, "JWT is rejected");
        }
    }
    private static byte[] FromBase64Url(string base64Url)
    {
        string padded = base64Url.Length % 4 == 0
                ? base64Url : base64Url + "====".Substring(base64Url.Length % 4);
        string base64 = padded.Replace("_", "/")
                                  .Replace("-", "+");
        return Convert.FromBase64String(base64);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在转到 Postman，向运行 WebAPI 的地方发出 Post 请求，在`Authorization`属性中传递上面得到的 JWT(使用承载模式),您将得到状态为 200 的响应。

[![Call WebAPI endpoint](img/0828e95916b93f93da74efe83ecf945d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aWz4L1w7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lvacwu21efynloioo299.png)

没有 JWT 或使用不同的方案将导致 401 未授权。

#### 利益之分

1-代替下面的代码

```
...
var pr = new PemReader(new StringReader(publicKey));
var asymmetricKeyParameter = (AsymmetricKeyParameter)pr.ReadObject();
... 
```

Enter fullscreen mode Exit fullscreen mode

我看过

```
...
var keyBytes = Convert.FromBase64String(publicKey);
var asymmetricKeyParameter = PublicKeyFactory.CreateKey(keyBytes);
... 
```

Enter fullscreen mode Exit fullscreen mode

后者的问题是，抛出了下面的 FormatException】

```
The format of s is invalid. s contains a non-base-64 character, more than two padding characters, or a non-white space-character among the padding characters. 
```

Enter fullscreen mode Exit fullscreen mode

2-`JwtAuthorizationAttribute`过滤器异步运行，因为单例读取公钥也是异步的。为了确保过滤器总是在控制器方法之前运行，我人为地引入了 10 ms 的延迟。然而，正如我所说的，公钥只需读取一次，之后，它就可以在内存中使用了。因此，如果每个请求都被罚 10 毫秒，这似乎不公平。所以我在寻找更好的解决方案。

最后，如果你要源代码，我还在整理。同时，你可以通过给这篇文章一个赞并分享它来激励我。