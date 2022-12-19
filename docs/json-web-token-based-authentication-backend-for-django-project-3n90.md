# Django 项目基于 JSON WEB 令牌的认证后端

> 原文：<https://dev.to/apcelent/json-web-token-based-authentication-backend-for-django-project-3n90>

在我们的[上一篇文章](https://blog.apcelent.com/json-web-token-tutorial-with-example-in-python.html)中，我们给出了关于 JSON Web 令牌的详细描述。在这篇文章中，我们将展示一个使用 Django Web 框架的示例 JSON Web 令牌认证机制。

Owasp 将认证定义为验证个人、实体或网站是其所声称的那个人的过程。web 应用程序上下文中的身份验证通常通过提交用户名或 ID 以及一项或多项只有给定用户才应该知道的私有信息来执行。

HTTP 本质上是无状态的，不管后端是什么。因此，大多数 web 框架都带有授权和会话管理特性。在典型的浏览器-服务器情况下；浏览器为每个域管理一个名为 cookies 的键/值对列表。服务器可以通过解析 Cookie HTTP 请求头来访问 Cookie(读取)。这些 Cookie 可以由服务器使用 Set-Cookie HTTPresponse 头进行管理(创建、修改、删除)。

面向 Web 的框架提供了在更高层次上处理 cookies 的功能，让我们来看看 django。

[![Alt text of image](img/796d8db69ac10baa69ca665150c01432.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4495ZZM8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/apcelent-json-authentication-django.png)

## Django 的内置认证

Django 内置了[会话](https://docs.djangoproject.com/en/dev/topics/http/sessions/)和[认证](https://docs.djangoproject.com/en/dev/topics/auth/)管理。它通过中间件负责会话和授权，中间件修改传入的请求和传出的响应。

Django 会话中间件接收一个[传入请求](https://github.com/django/django/blob/master/django/contrib/sessions/middleware.py#L14)，在请求的 cookies 中查找会话密钥，然后将 request.session 设置为 SessionStore 实例。如果请求会话被修改，或者如果配置是每次都保存会话，则它保存更改并设置会话 cookie，或者如果会话已被清空，则删除会话。

[认证中间件](https://github.com/django/django/blob/master/django/contrib/auth/middleware.py)将 request.user 设置为 LazyUser。这里的 LazyUser 用于延迟包装类的实例化。当 request.user 被访问时——比方说被@ log in _ required decorator——lazy user 调用 django.contrib.auth.get_user()，传入请求；get_user()从会话中提取信息，如果用户通过了身份验证，则返回适当的用户实例。但是，如果没有用户的会话详细信息，则会尝试对用户进行身份验证。

我们如何认证用户？

给定一个凭据字典(用户名和密码)，我们调用 django . contrib . auth . aut enticate(* *凭据)。如果成功，该函数返回用户对象，如果凭证不正确，则返回 None。

给定一个经过身份验证的用户对象，我们调用 django . contrib . auth . log in(request，User)。这将身份验证后端和用户 id 存储到会话中。由于会话中的一个变量被修改，request.session 应该被更新，这是由[会话中间件](https://github.com/django/django/blob/53ccffdb8c8e47a4d4304df453d8c79a9be295ab/django/contrib/sessions/middleware.py#L18)中的 process_response 完成的。

## Django 中的自定义认证后端

Django 有一个容易扩展的认证后端。它允许改变检查用户凭证的方法。这方面的一个例子是创建一个社会认证后端。像脸书这样的 OAuth 提供者提供当前通过身份验证的用户的详细信息。但是为了维护 login_required 装饰器或使用 request.user 变量，我们仍然需要让它们登录到 django。这就是自定义验证后端可以实现的功能。

让我们实现一个基本的自定义身份验证后端，其中所有用户的密码都是 alphaQ。

用户名:alpha 密码:alphapass

```
 # import the User object
    from django.contrib.auth.models import User

    # Define backend class
    class BasicCustomBackend(object):

        # Create an authentication method
        def authenticate(self, username=None, password=None):

        try:
            # Try to find a user matching the username provided
            user = User.objects.get(username=username)

            # if successful return user if not return None
            if password == 'alphapass':
                return user
            else:
                return None
        except User.DoesNotExist:
            # No user was found
            return None

        # Required for the backend to work properly
        def get_user(self, user_id):
        try:
            return User.objects.get(pk=user_id)
        except User.DoesNotExist:
            return None 
```

Enter fullscreen mode Exit fullscreen mode

在 settings.py 中我们添加了

```
AUTHENTICATION_BACKENDS = ( 'path.to.Backend', ) 
```

Enter fullscreen mode Exit fullscreen mode

这个后端不适合生产使用，但足以演示一个定制的后端。务必参考[正式文件](https://docs.djangoproject.com/en/1.9/topics/auth/customizing/)。

## JWT 和姜戈

现在我们已经理解了 django 中的基本认证过程，并编写了一个自定义的认证后端。让我们转到编写基于 JSON Web 令牌的定制后端。为了重温 JWT Auth 是如何工作的，阅读我们关于 JWT 的[文章会很有帮助。](https://blog.apcelent.com/json-web-token-tutorial-with-example-in-python.html)

从文章中可以明显看出，有四个步骤:

1.  浏览器向服务器发送包含用户名和密码的 POST 请求。

2.  服务器创建一个令牌，并返回给浏览器。

3.  浏览器在验证头中发送令牌。

4.  服务器验证令牌，并在响应中返回受保护的信息。

基于令牌的身份验证需要以下步骤:

1.  一个 python 库来生成和验证 jwt，我们将使用 [python-jose](https://github.com/mpdavis/python-jose/) 。

2.  一个 django 视图，接受用户名和密码并返回一个令牌。

```
 from jose import jws
    from django.http import HttpResponse
    import datetime
    from django.contrib.auth import authenticate

    def create_jwt(request):

        """
        the above token need to be saved in database, and a one-to-one
        relation should exist with the username/user_pk
        """

        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(username=username, password=password)
        expiry = datetime.date.today() + timedelta(days=50)
        token = jws.sign({'username': user.username, 'expiry':expiry}, 'seKre8',  algorithm='HS256')

    return HttpResponse(token) 
```

Enter fullscreen mode Exit fullscreen mode

1.  令牌的自定义身份验证后端

```
 class JWTAuthentication(object):

        """
        Simple token based authentication.
        Clients should authenticate by passing the token key in the "Authorization"
        HTTP header, prepended with the string "Token ".  For example:
        Authorization: Token 401f7ac837da42b97f613d789819ff93537bee6a
        """

        def authenticate(self, request):
        auth = get_authorization_header(request).split()

        if not auth or auth[0].lower() != b'token':
            return None

        try:
            token = auth[1].decode()
        except UnicodeError:
            msg = _('Invalid token header. Token string should not contain invalid  characters.')
            raise exceptions.AuthenticationFailed(msg)

        return self.authenticate_credentials(token)

        def authenticate_credentials(self, payload):

        decoded_dict = jws.verify(payload, 'seKre8', algorithms=['HS256'])

        username = decoded_dict.get('username', None)
        expiry = decoded_dict.get('expiry', None)

        try:
            usr = User.objects.get(username=username)
        except model.DoesNotExist:
            raise exceptions.AuthenticationFailed(_('Invalid token.'))

        if not usr.is_active:
            raise exceptions.AuthenticationFailed(_('User inactive or deleted.'))

        if expiry < datetime.date.today():
            raise exceptions.AuthenticationFailed(_('Token Expired.'))

        return (usr, payload)

        def authenticate_header(self, request):
        return 'Token' 
```

Enter fullscreen mode Exit fullscreen mode

希望上面的文章展示了如何用 django 实现 JWT。以上步骤仅为演示之用，不应用于生产。以下是基于令牌的身份验证的常用库帮助:

[django-rest-know](https://github.com/James1345/django-rest-knox/)

django-jwt-auth

希望这篇文章有所帮助。欢迎在评论中提交你的想法。

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/django-json-web-token-authentication-backend.html)上。