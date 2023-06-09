# 防止 Django 应用程序中的一个用户有多个会话

> 原文：<https://dev.to/fleepgeek/prevent-multiple-sessions-for-a-user-in-your-django-application-13oo>

欢迎来到我在这个平台上的第一个教程。在这个例子中，我将向你展示如何防止一个用户帐户同时拥有多个会话。
每当用户登录时，之前的会话都会被删除。
这种方法在基于订阅的应用程序中非常有用，在这种应用程序中，您希望订阅用户可以进行单一访问。

# 我们的方法

对于本教程，我们将使用 django 2.0.4 及其中间件框架。中间件只是 django 的请求/响应处理之间的中间人。Django 有一些我们已经在项目中默认使用的中间件。
每当用户发出请求时，我们将使用中间件框架来检查用户在我们的应用程序上是否有现有会话。
如果会话存在，它将删除该会话，并将请求中的新会话指定为当前会话。

**注意**:“Django 将会话数据存储在“django_session”表中，因此从数据库中删除用户的会话与注销用户具有相同的效果。”

# 代码时间

话太多了，对吧？好了，我们来编码吧。
Ermm...还有一件事。下面的所有代码都应该存储在一个应用程序中。我相信你知道这一点，但有时说出一些显而易见的事情也无妨。
在您的 django 项目中创建一个名为“accounts”的应用程序，并遵循本教程。

## [T1】models . py](#modelspy)

我们需要创建一个模型来存储当前登录到我们的应用程序
的用户列表

```
from django.conf import settings

User = settings.AUTH_USER_MODEL

# Model to store the list of logged in users class LoggedInUser(models.Model):
    user = models.OneToOneField(User, related_name='logged_in_user')
    # Session keys are 32 characters long
    session_key = models.CharField(max_length=32, null=True, blank=True)

    def __str__(self):
        return self.user.username 
```

Enter fullscreen mode Exit fullscreen mode

我们创建了一个带有`OneToOneField`的`user`字段，这样一个`user`在表中只能有一个条目。简单地说，这唯一地标识了表中的一个用户。更简单地说，表中没有用户的重复条目。用最简单的话来说就是['在此插入您的']。`related_name`属性用于给`User`模型的反向关系起一个更友好的名字(你很快就会看到)。
`session_key`将用于存储`session_key`。很抱歉如果你期待一个更简单的解释。那些时代已经过去了。

## [T1】signals . py](#signalspy)

在您的应用程序目录中创建一个名为`signals.py`的新文件。每当我们的应用程序中发生一个动作时，信号就被用来广播事件。
对于我们的应用程序，我们需要知道用户何时登录和注销。在信号的帮助下，我们可以知道这一点并执行一些操作(这里，将用户插入 LoggedInUser 表)。

```
# Signals that fires when a user logs in and logs out 
from django.contrib.auth import user_logged_in, user_logged_out
from django.dispatch import receiver
from accounts.models import LoggedInUser

@receiver(user_logged_in)
def on_user_logged_in(sender, request, **kwargs):
    LoggedInUser.objects.get_or_create(user=kwargs.get('user')) 

@receiver(user_logged_out)
def on_user_logged_out(sender, **kwargs):
    LoggedInUser.objects.filter(user=kwargs.get('user')).delete() 
```

Enter fullscreen mode Exit fullscreen mode

我们使用内置的`user_logged_in`和`user_logged_out`信号，每当用户分别成功登录和注销时都会触发这两个信号。
如果你想知道我们是如何通过`kwargs`获得用户的，那就别想了，我的朋友。`user`参数总是与上面提到的两个信号一起发送。

## apps.py

将以下代码添加到您的`apps.py`文件中，以导入您的信号。

```
from django.apps import AppConfig
class AccountsConfig(AppConfig):
    name = 'accounts'
    # This function is the only new thing in this file
    # it just imports the signal file when the app is ready
    def ready(self):
        import accounts.signals 
```

Enter fullscreen mode Exit fullscreen mode

在您的`settings.py`文件中，更新您的应用程序的名称以指向`apps.py`
中的确切应用程序

```
INSTALLED_APPS = [
    .....

    'accounts.apps.AccountsConfig',
] 
```

Enter fullscreen mode Exit fullscreen mode

## 中间件. py

在你的 app 目录下创建一个名为`middleware.py`的文件。

```
#Session model stores the session data from django.contrib.sessions.models import Session

class OneSessionPerUserMiddleware:
    # Called only once when the web server starts
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Code to be executed for each request before
        # the view (and later middleware) are called.
        if request.user.is_authenticated:
            stored_session_key = request.user.logged_in_user.session_key

            # if there is a stored_session_key  in our database and it is
            # different from the current session, delete the stored_session_key
            # session_key with from the Session table
            if stored_session_key and stored_session_key != request.session.session_key:
                Session.objects.get(session_key=stored_session_key).delete()

            request.user.logged_in_user.session_key = request.session.session_key
            request.user.logged_in_user.save()

        response = self.get_response(request)

        # This is where you add any extra code to be executed for each request/response after
        # the view is called.
        # For this tutorial, we're not adding any code so we just return the response 
        return response 
```

Enter fullscreen mode Exit fullscreen mode

注意，`request.user.logged_in_user.session_key`我们可以在这里使用`logged_in_user`,因为我们将它定义为您的`LoggedInUser`模型中`user`字段的相关名称。如果我们不这样做，反向关系的名字将是`loggedinuser`，这不符合 python 的命名约定。

最后，将我们刚刚创建的中间件添加到您的`settings.py`文件
的`MIDDLEWARE`列表中

```
MIDDLEWARE = [
    ....

    'accounts.middleware.OneSessionPerUserMiddleware'

] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在这个例子中，我们学习了如何防止一个用户在你的应用中拥有多个会话。更像是防止坏人从你的 web 应用程序上的一个订阅帐户在 50 个设备(准确地说是 50 个朋友和家人的设备)上播放视频。

本教程将向您展示如何在 django 应用程序中创建定制中间件。肯定有其他方法可以更好地实现这一点。这可能不是满足您需求的完美解决方案。这个想法只是向你展示中间件是如何工作的。
现在开始探索吧！！
感谢阅读。

## 视频版

看完这个视频你知道该怎么做了吧？好吧，让我再说一遍显而易见的事情。点击“订阅”按钮，然后点击铃声图标。再次感谢您的阅读。

[https://www.youtube.com/embed/q7yWy97JISo](https://www.youtube.com/embed/q7yWy97JISo)