# 从头开始:用户认证

> 原文：<https://dev.to/absolux/from-scratch-user-authentication-37a7>

## 定义

用户身份验证被宽泛地定义为根据用户的凭证来识别用户。换句话说，它通过检查用户的凭证是否与保存在数据库、数据认证服务器或任何其他地方的凭证相匹配，为系统提供访问控制。

另一方面，凭证可以是任何东西:标识符、密码、个人识别码、证书、指纹、视网膜、声音。我指的是将用户与其他用户区分开来的任何东西。

你可以在这篇精彩的[文章](https://searchsecurity.techtarget.com/definition/authentication)中获得更多关于认证、认证类型和认证因素的信息。

## 方案

开发人员和框架提供了不同的解决方案和愿景，简单的和复杂的，来管理用户标识。

让我们看一些例子:

### Zend 框架 2

Zend framework 提供了一个专用的模块 [zend-authentication](https://docs.zendframework.com/zend-authentication/) ，其中包括针对不同认证方法的`adapters`，以及一个`AuthenticationService`类。

```
use Zend\Authentication\AuthenticationService;

// Instantiate the authentication service
$auth = new AuthenticationService();

// Instantiate a dummy authentication adapter
$authAdapter = new Adapter($email, $password);

// Attempt authentication, saving the result
$result = $auth->authenticate($authAdapter); 
```

在成功的认证尝试之后，后续的请求可以查询认证服务以确定身份是否存在，如果存在，则检索它:

```
if ($auth->hasIdentity()) {
  // Identity exists; get it
  $identity = $auth->getIdentity();
} 
```

### 拉勒韦尔 5

事实上，Laravel 是一个庞大的框架，提供不同的解决方案和工具来处理认证。

Laravel 的认证服务可以通过`Auth` [门面](https://laravel.com/docs/5.6/facades)访问。

```
use Illuminate\Support\Facades\Auth;

if (Auth::attempt($credentials)) {
  // Authentication passed
}
else {
  // Authentication failed
} 
```

`attempt`方法接受一个键/值对的数组作为它的第一个参数，如果在数据库中发现用户使用数组中的值，它将返回一个`boolean`。

可以通过`Auth` facade 再次检索经过身份验证的用户。

```
use Illuminate\Support\Facades\Auth;

// Get the currently authenticated user...
$user = Auth::user();

// Get the currently authenticated user's ID...
$id = Auth::id(); 
```

### symphony 4

Symfony 的解决方案相当复杂。`security`模块提供了多个类和接口来处理认证。

```
use Symfony\Component\Security\Core\Authentication\AuthenticationProviderManager;
use Symfony\Component\Security\Core\Authentication\Token\UsernamePasswordToken;
use Symfony\Component\Security\Core\Exception\AuthenticationException;

// instances of Symfony\Component\Security\Core\Authentication\Provider\AuthenticationProviderInterface
$providers = array(...)

// Instantiate the authentication manager
$authenticationManager = new AuthenticationProviderManager($providers);

// create a token, containing the user's credentials
$unauthenticatedToken = new UsernamePasswordToken($username, $password, $providerKey);

// validate the given token, and return an authenticated token
try {
  $authenticatedToken = $authenticationManager->authenticate($unauthenticatedToken);
} catch (AuthenticationException $exception) {
  // authentication failed
} 
```

认证后，当前用户的用户对象可以通过`getUser()`从控制器内部访问，如下所示:

```
public function index()
{
  $user = $this->getUser();
} 
```

或者通过版本 3.4 中可用的`Security`类:

```
use Symfony\Component\Security\Core\Security;

public function indexAction(Security $security)
{
  $user = $security->getUser();
} 
```

### 姜戈 2

默认情况下，Django 使用会话和中间件将身份验证系统与请求对象挂钩。

```
from django.contrib.auth import authenticate

def my_view(request):
  user = authenticate(username='john', password='secret')

  if user is not None:
    # A backend authenticated the credentials
  else:
    # No backend authenticated the credentials 
```

无论用户是否经过身份验证，都可以在`request`对象中访问:

```
if request.user.is_authenticated:
  # Do something for authenticated users.
  ...
else:
  # Do something for anonymous users.
  ... 
```

### Passport.js

认证请求就像调用`passport.authenticate()`并指定采用哪种策略一样简单。`authenticate()`的函数签名是一个标准的[连接](http://www.senchalabs.org/connect/)中间件，方便在[快递](http://expressjs.com/)应用中作为路由中间件使用。

```
app.post('/login', passport.authenticate('strategy-name'), function (req, res) {
  // If this function gets called, authentication was successful.
  // `req.user` contains the authenticated user.
  res.redirect('/users/' + req.user.username);
}); 
```

不幸的是，`passport`只能在类似 express 的应用程序中使用，因为`authenticate()`返回一个中间件函数。它没有提供任何可以独立使用的 API。

## 结论

在我看来，Django [解决方案](https://docs.djangoproject.com/en/2.1/topics/auth/default/#authenticating-users)是最好的一个，因为与其他方案不同，它将`authentication`与`login`分开。

*   [authenticate](https://docs.djangoproject.com/en/2.1/topics/auth/default/#django.contrib.auth.authenticate) 函数根据每个认证后端检查用户凭证，如果凭证对后端有效，则返回一个`User`对象。
*   但是 [login](https://docs.djangoproject.com/en/2.1/topics/auth/default/#django.contrib.auth.login) 函数将用户的 ID 保存在会话中，使得它在多次请求中都是持久的。

登录用户是特定于应用程序的。但是用户身份在应用和系统之间是共享的，只有`backend`不同。

从上面以及这里没有引用的其他例子中，我得到了一个简单、愚蠢的身份验证管理器，它将用户凭证分派给一系列处理程序。

```
interface AuthManager {
  /**
   * Check the credentials and return the user
   */
  attempt (credentials: object): Promise<User | undefined>

  /**
   * Register an authenticator
   */
  use (name: string, handler: Authenticator): this
} 
```

管理器是`chain of responsibility`设计模式的一个实现，在内部使用具有以下契约的授权码:

```
interface Authenticator {
  /**
   * Try to handle the credentials, or delegate to the next handler.
   */
  process (credentials: object, next: () => any): Promise<User | undefined>
} 
```

我仍在致力于这个实现，以使它变得清晰和与框架无关。你可以看看[开发](https://github.com/aldojs/authentication/tree/develop)分公司。

伙计们，你们觉得怎么样？