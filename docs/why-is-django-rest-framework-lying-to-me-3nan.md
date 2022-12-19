# Django REST 框架为什么要骗我？

> 原文：<https://dev.to/rhymes/why-is-django-rest-framework-lying-to-me-3nan>

关于框架的一件事是，当你遵循教程和惯例时，它们工作得很好，当你试图稍微偏离轨道时，它们通常不工作。

我花了太多时间尝试从 VueJS SPA 登录 Django 应用，有以下要求/限制:

*   没有 jwt 或令牌
*   登录应该只发送凭证和 csrf cookie/header，并取回将在下面的请求中自动发送的`HttpOnly`会话 cookie
*   就这样

这应该有多难？

我已经安装了`django-rest-framework`和`django-rest-auth`，但它们似乎只能与`token based authentication`或其他东西一起工作，而不能与普通的基于会话的认证一起工作。过了一会儿，我开始在`django-rest-framework`和`django-rest-auth`代码库中放置断点，以弄清楚发生了什么。

这就是现在正在发生的事情:

前端在第一次请求时发送由 Django 设置的证书和 CSRF 令牌:

```
const CSRF_COOKIE_NAME = 'csrftoken'
const CSRF_HEADER_NAME = 'X-CSRFToken'

const client = axios.create({
  xsrfCookieName: CSRF_COOKIE_NAME,
  xsrfHeaderName: CSRF_HEADER_NAME,
})

export default {
  login(username, password) {
    return client.post('/auth/login/', { username, password })
  },
  logout() {
    return client.post('/auth/logout/')
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

服务器上的`/auth/`映射到 django rest auth 如下:

```
path('auth/', include('rest_auth.urls')) 
```

Enter fullscreen mode Exit fullscreen mode

服务器(django-rest-auth)看到登录尝试并调用认证器(在 django-rest-framework 中)进行[会话认证](https://github.com/encode/django-rest-framework/blob/master/rest_framework/authentication.py#L117) :

```
 def authenticate(self, request):
        """
        Returns a `User` if the request session currently has a logged in user.
        Otherwise returns `None`.
        """

        # Get the session-based user from the underlying HttpRequest object
        user = getattr(request._request, 'user', None)

        # Unauthenticated, CSRF validation not required
        if not user or not user.is_active:
            return None

        self.enforce_csrf(request)

        # CSRF passed with authenticated user
        return (user, None) 
```

Enter fullscreen mode Exit fullscreen mode

这是它变得令人毛骨悚然的地方，文档并没有完全澄清这一点。

这个方法唯一做的事情是检查服务器端是否已经有一个经过身份验证的用户(对此不能 100%确定)，从逻辑上来说，这意味着要通过验证，您已经需要经过身份验证(！？！？)通过 AJAX 来使用它。

因此，它不是对您进行身份验证，而是检查您已有的身份验证是否正确有效。

我需要的是认证凭证，这东西并没有真正做到这一点。

如果它实际上没有认证你，为什么叫`SessionAuthentication`？

这并不是说它没有用，它只是对传统登录和 AJAX 调用的服务器端渲染页面有用，而不是 SPA。

命名:D 浪费了太多时间，他们应该叫它`CheckAlreadyExistingSessionAuthentication`。

有人有什么建议吗？

我在考虑自己实现它(可能作为 django rest 框架的自定义类？).似乎所有这些花哨的认证机制(令牌、jwt、oauth 等等)都忘记了涵盖基础知识...

**6 月 25 日更新**

现在我(我想)通过稍微弯曲框架来解决这个问题。这很难看，一旦我有多一点时间，我会想出如何正确地做到这一点，遗憾的是，所有这些框架都忽略了基于会话的认证。

这就是我所做的。在服务器端，我必须创建一些自定义的东西。

首先，我必须为 Django REST 框架创建一个定制认证类,它简单地获取凭证并使用 Django 自己的认证层来认证用户:

```
# adapted from https://bit.ly/2K80boT and https://bit.ly/2JV1iMK 
from django.contrib import auth
from django.contrib.auth.models import User
from django.middleware.csrf import CsrfViewMiddleware
from django.utils.translation import ugettext_lazy as _
from rest_framework import authentication, exceptions

class CSRFCheck(CsrfViewMiddleware):
    def _reject(self, request, reason):
        # Return the failure reason instead of an HttpResponse
        return reason

class DRFSessionAuthentication(authentication.BaseAuthentication):
    'Session authentication against username/password for DRF'

    def authenticate(self, request):
        '''
        Returns a User if a correct username and password have been supplied
        using Django Session Authentication. Otherwise returns None.
        '''

        username = request.data.get('username')
        password = request.data.get('password')

        return self.authenticate_credentials(username, password, request)

    def authenticate_credentials(self, userid, password, request=None):
        '''
        Authenticate the userid and password against username and password
        with optional request for context.
        '''

        credentials = {
            auth.get_user_model().USERNAME_FIELD: userid,
            'password': password
        }
        user = auth.authenticate(request=request, **credentials)

        if user is None:
            raise exceptions.AuthenticationFailed(
                _('Invalid username/password.')
            )

        if not user.is_active:
            raise exceptions.AuthenticationFailed(
                _('User inactive or deleted.')
            )

        self.enforce_csrf(request)

        return (user, None)

    def authenticate_header(self, request):
        return 'Session'

    def enforce_csrf(self, request):
        'Enforce CSRF validation for session based authentication.'

        reason = CSRFCheck().process_view(request, None, (), {})
        if reason:
            # CSRF failed, bail with explicit error message
            raise exceptions.PermissionDenied('CSRF Failed: %s' % reason) 
```

Enter fullscreen mode Exit fullscreen mode

Django 自动创建会话 cookies，将其设置为 http only，并(如果您这么说的话)将其设置为生产安全。

一旦你有了 http-only 会话 cookie，用户实际上就被认证了，在每次 HTTP 调用时，浏览器将把会话令牌发送给服务器。

(仅供参考:会话 cookie 只是 HTTP 头中的一个令牌，基本上与那些花哨的 auth 令牌是一样的)

下一步是让客户端检查用户是否通过了身份验证，这可以通过调用服务器来完成，服务器的响应如下:

```
def user(request):
    'Returns a user object if authenticated, 401 otherwise'

    if request.user.is_authenticated:
        user = request.user
        return JsonResponse({
            'id': user.id,
            'username': user.username,
            'email': user.email,
            'first_name': user.first_name,
            'last_name': user.last_name
        })
    else:
        return JsonResponse({}, status=status.HTTP_401_UNAUTHORIZED) 
```

Enter fullscreen mode Exit fullscreen mode

为了让它正常工作，我必须像这样连接路由(同样，把`django-rest-auth`留在一边):

```
from django.contrib.auth import views as auth_views
from app import views

path('auth/login/', views.SessionLoginView.as_view(), name='login'),
path('auth/logout/', auth_views.logout, {'next_page': '/'}, name='logout'),
path('auth/user/', views.user, name='user'), 
```

Enter fullscreen mode Exit fullscreen mode

`SessionLoginView`是一个定制类，我必须编写它来绕过所有的*令牌认证*机器 django-rest-auth 放入的:

```
class SessionLoginView(LoginView):
    def login(self):
        self.user = self.serializer.validated_data['user']
        self.process_login()

    def get_response(self):
        return Response('', status=status.HTTP_204_NO_CONTENT) 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都是丑陋和未经测试的，我浪费了一些时间来解决一些应该非常简单的事情...