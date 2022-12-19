# 如何为你的 Symfony 应用程序使用两种不同的认证系统？

> 原文：<https://dev.to/minompi/how-to-use-two-different-authentication-system-for-your-symfony-application-203j>

[![Directory Structure Image](../Images/177ac9d3408b329d020197f9149cc55f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CyqtbvI---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://alessandrominoccheri.github.io/img/symfony-authentication.jpg)

在你的应用程序中，很多时候你有一个基于 **JWT 令牌**的登录系统。
在某个时刻，您需要您的应用程序能够响应另一个应用程序发出的 rest api 请求。所以你可以给它使用 JWT 令牌登录的可能性，但这是不安全的。
所以你需要为你的应用实现一个 api 令牌认证，但这意味着你的应用必须**管理两个认证器** : JWT 和 Api 令牌。

嗯，怎么能同时使用**认证系统**？

第一步是让第一个认证器工作，就好像它是唯一的一样。

**security.yml**

```
rest_api:
    pattern:   ^/api/
    stateless: true
    guard:
        authenticators:
           - lexik_jwt_authentication.jwt_token_authenticator 
```

Enter fullscreen mode Exit fullscreen mode

然后开始在 security.yml 文件中实现另一个验证器，如下:

```
rest_api:
    pattern:   ^/api/
    stateless: true
    guard:
        authenticators:
           - lexik_jwt_authentication.jwt_token_authenticator
           - AppBundle\Security\ApiKeyAuthenticator
        entry_point: lexik_jwt_authentication.jwt_token_authenticator 
```

Enter fullscreen mode Exit fullscreen mode

在此配置中，您指定了两个不同的授权码系统和一个入口点。
注意，入口点是强制的！切入点突出了原则认证系统。

现在您需要将字段 apiKey 添加到您的用户实体中，如下所示:

```
/**
 * @var string
 *
 * @ORM\Column(name="api_key", type="string", length=255, nullable=false, unique=true)
 */
private $apiKey; 
```

Enter fullscreen mode Exit fullscreen mode

之后，您需要创建您的自定义 ApiKeyAuthenticator，例如:

```
namespace AppBundle\Security;

use AppBundle\Entity\PvpUser;
use Doctrine\ORM\EntityManagerInterface;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Security\Core\Authentication\Token\TokenInterface;
use Symfony\Component\Security\Core\Exception\AuthenticationException;
use Symfony\Component\Security\Core\User\UserInterface;
use Symfony\Component\Security\Core\User\UserProviderInterface;
use Symfony\Component\Security\Guard\AuthenticatorInterface;
use Symfony\Component\Security\Guard\Token\GuardTokenInterface;
use Symfony\Component\Security\Guard\Token\PostAuthenticationGuardToken;

class ApiKeyAuthenticator implements AuthenticatorInterface
{
    private $em;

    public function __construct(EntityManagerInterface $em)
    {
        $this->em = $em;
    }

    public function start(Request $request, AuthenticationException $authException = null)
    {
        return new Response('Auth header required', 401);
    }

    public function supports(Request $request)
    {
        return $request->headers->has('apikey');
    }

    public function getCredentials(Request $request)
    {
        return array(
            'token' => $request->headers->get('apikey'),
        );
    }

    public function getUser($credentials, UserProviderInterface $userProvider)
    {
        $apiKey = $credentials['token'];

        if (null === $apiKey) {
            return;
        }

        $user = $this->em->getRepository(User::class)->findOneByApiKey($apiKey);

        if (null == $user) {
            return null;
        }

        return $userProvider->loadUserByUsername($user->getUsername());
    }

    public function checkCredentials($credentials, UserInterface $user)
    {
        //here you can check password if necessary
        return true;
    }

    public function createAuthenticatedToken(UserInterface $user, $providerKey)
    {
        return new PostAuthenticationGuardToken(
            $user,
            $providerKey,
            $user->getRoles()
        );
    }

    public function onAuthenticationFailure(Request $request, AuthenticationException $exception)
    {
        return new Response('Authentication Failure', 401);
    }

    public function onAuthenticationSuccess(Request $request, TokenInterface $token, $providerKey)
    {
        return null;
    }

    public function supportsRememberMe()
    {
        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

功能解释

**开始**

```
This is called when an anonymous request accesses a resource that requires authentication. The job of this method is to return some response that "helps" the user start the authentication process.
 It must return a Response object 
```

Enter fullscreen mode Exit fullscreen mode

**支持**
该函数表示如果认证者支持给定的请求
，它必须返回一个布尔值

**getCredentials**
这个函数从请求中获取认证证书，并将它们返回到一个数组或变量中，这取决于你的系统

**getUser**
根据凭证返回一个 UserInterface 对象。它必须返回一个用户界面对象或空值。

**checkCredentials**
如果凭证有效，则返回 true。
它必须返回一个布尔值

**createAuthenticatedToken**
为给定用户创建一个认证令牌
它必须返回一个 GuardTokenInterface 对象

**onAuthenticationFailure**
该函数在认证已经执行但失败(如 apikey 错误)时调用。
它必须返回一个响应对象或 null

**onAuthenticationSuccess**
它必须返回一个响应对象或 null

**supportsRememberMe**
该函数表示如果认证支持记住我函数
，则必须返回一个布尔值

在这段代码中，我们检查每个请求的头中是否有键 **apikey** 。
如果该报头存在，认证器尝试通过存储在数据库中的 apikey 加载用户。然后，如果找到该用户，则检查是否允许其访问所请求的资源。

这样，您可以对所有路由使用这两种授权码。但是如果你只想关闭一些路线，你可以在你的 **security.yml** 中指定许多规则