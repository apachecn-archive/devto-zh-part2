# YouTube API 集成:用 Django 上传视频

> 原文：<https://dev.to/ivancrneto/youtube-api-integration-uploading-videos-with-django-53c5>

不久前，我为一个客户工作，在他们的网站上整合视频评论。像任何有动力的开发人员解决一个新问题一样，我做的第一件事是谷歌它，我发现了过多无用或误导的答案，关于如何实现完全不同的东西，或者过时和无人维护的 Python 包。最终，我咬紧牙关，团队和我从头开始构建一切:我们创建了视图，了解了 Google 的 API，创建了 API 客户端，并最终成功地从 Django 以编程方式上传了视频。

在这篇文章中，我将试着一步一步地指导你如何从 Django 应用程序上传 YouTube 视频。这将需要对 Google API 凭证进行一些调整——首先是 web 界面，然后是代码。YouTube 部分本身非常简单。我们需要了解谷歌的工作原理，因为有时这很棘手，信息会分散到很多地方。

## 先决条件

我建议您在开始工作之前熟悉以下内容:

*   [YouTube 数据 API: Python 快速入门](https://developers.google.com/youtube/v3/quickstart/python)
*   [YouTube 数据 API: API 参考](https://developers.google.com/youtube/v3/docs/)
*   [YouTube 数据 API:代码示例](https://developers.google.com/youtube/v3/code_samples/)
*   [谷歌 Python API 客户端库](https://developers.google.com/api-client-library/python/)
*   [Google Python API 客户端库:参考文档](https://developers.google.com/api-client-library/python/reference/pydoc)
*   [Google Python API 客户端库:代码示例](https://github.com/google/google-api-python-client/blob/master/samples/README.md)
*   [YouTube API: Python 代码示例](https://github.com/youtube/api-samples/tree/master/python)

值得注意的一段有趣的代码是来自 [Google YouTube API 文档](https://developers.google.com/youtube/v3/docs/videos/insert)的以下 Python 片段:

```
 # Sample python code for videos.insert 
def videos_insert(client, properties, media_file, **kwargs):
  resource = build_resource(properties) # See full sample for function
  kwargs = remove_empty_kwargs(**kwargs) # See full sample for function
  request = client.videos().insert(
    body=resource,
    media_body=MediaFileUpload(media_file, chunksize=-1,
                               resumable=True),
    **kwargs
  )

  # See full sample for function
  return resumable_upload(request, 'video', 'insert')

media_file = 'sample_video.flv'
  if not os.path.exists(media_file):
    exit('Please specify a valid file location.')
videos_insert(client, 
    {'snippet.categoryId': '22',
     'snippet.defaultLanguage': '',
     'snippet.description': 'Description of uploaded video.',
     'snippet.tags[]': '',
     'snippet.title': 'Test video upload',
     'status.embeddable': '',
     'status.license': '',
     'status.privacyStatus': 'private',
     'status.publicStatsViewable': ''},
    media_file,
    part='snippet,status') 
```

Enter fullscreen mode Exit fullscreen mode

## 入门

阅读完前提条件后，就该开始了。让我们看看我们需要什么。

### 工具带

基本上，让我们创建一个虚拟环境。我个人比较喜欢 [pyenv](https://github.com/pyenv/pyenv) 。设置这两者超出了本文的范围，所以我将在下面发布一些 pyenv 命令，如果您的偏好是`virtualenv`，请随意相应地替换这些命令。

在这篇文章中，我将使用 Python 3.7 和 Django 2.1。

```
➜  ~/projects $ mkdir django-youtube
➜  ~/projects $ cd django-youtube
➜  ~/projects/django-youtube $ pyenv virtualenv 3.7.0 djangoyt
➜  ~/projects/django-youtube $ vim .python-version 
```

Enter fullscreen mode Exit fullscreen mode

```
 djangoyt 
```

Enter fullscreen mode Exit fullscreen mode

正在安装依赖项:

```
➜  ~/projects/django-youtube $ pip install google-api-python-client google-auth\
 google-auth-oauthlib google-auth-httplib2 oauth2client Django unipath jsonpickle 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候开始我们的 django 项目了:

```
➜  ~/projects/django-youtube $ django-admin startproject django_youtube . 
```

Enter fullscreen mode Exit fullscreen mode

## 暂停一些谷歌配置。

让我们现在配置我们的项目凭证，以便我们能够使用 Google APIs。

第一步。转到以下网址:[https://console . developers . Google . com/APIs/library/YouTube . Google APIs . com](https://console.developers.google.com/apis/library/youtube.googleapis.com)

第二步。创建新项目。

[![](img/ce3de6a1310450257704a33169502ed9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8rEdUh8Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qia5363b26v9tqgyz09e.png)

第三步。点按“启用 API 和服务”

[![](img/3c687424d006c3d8b55833c45dca5b48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EtDciwgf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2nw39v3h767r1t8ch76q.png)

第四步。找到 Youtube 数据 API v3，点击“启用”

[![](img/b39d4087bcd6db907db651e2e1c3a5d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wVEVk9fj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uol2oo9s5gqpuufn8uf3.png)

第五步。您应该会收到一条关于凭证的消息。

[![](img/78da97c1d28c4c5dd449956d026e85df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fsguMkGE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c3ghsorvh53ej95kfgv0.png)

第六步。单击右侧的“Create credentials”蓝色按钮，您应该会看到以下屏幕:

[![](img/55684c73adb630cba04e27324f203ba7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QDwIb3HQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ketgq0ubfrijjx0tgq2v.png)

第七步。选择 Web 服务器，用户数据

[![](img/53bad40866ae31a31dc5ad07b4d87920.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oim1FPS6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xrjljcb8caj2gsa2u4ka.png)

第八步。添加授权的 JS 起源和重定向 URIs。继续到底。

[![](img/2a2008e45df5c2ef22eefec4a8418f79.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cse_s05K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e6hpsl8jd6jqznbvo66m.png)

好了，我们完成了凭证设置。您可以下载 JSON 格式的凭证，或者复制*客户端 ID* 和*客户端秘密*。

## 回到姜戈

让我们开始我们的第一个 Django 应用程序。我通常把它命名为“核心”:

```
(djangoyt) ➜  ~/projects/django-youtube $ python manage.py startapp core 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将以下内容添加到我们的根 urls.py 文件中，以将主页请求路由到我们的核心应用:

```
# urls.py 
from django.urls import path, include

    path('', include(('core.urls', 'core'), namespace='core')), 
```

Enter fullscreen mode Exit fullscreen mode

在核心应用程序中，我们有另一个 urls.py 文件，也有一些配置:

```
# core/urls.py 
from django.conf import settings
from django.conf.urls.static import static
from django.urls import path

from .views import HomePageView

urlpatterns = [
    path('', HomePageView.as_view(), name='home')
]

if settings.DEBUG:
    urlpatterns += static(
        settings.STATIC_URL, document_root=settings.STATIC_ROOT)
    urlpatterns += static(
        settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 
```

Enter fullscreen mode Exit fullscreen mode

看到有一条空路径指向`HomePageView`。是时候添加一些代码了。

现在让我们做一个简单的`TemplateView`来看看它是如何运行的。

```
# core/views.py from django.shortcuts import render
from django.views.generic import TemplateView

class HomePageView(TemplateView):
    template_name = 'core/home.html' 
```

Enter fullscreen mode Exit fullscreen mode

当然我们需要一个基本的模板:

```
# core/templates/core/home.html
<!DOCTYPE html>
<html>
<body>

<h1>My First Heading</h1>
<p>My first paragraph.</p>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做一些设置调整:

```
# settings.py 

from unipath import Path

# Build paths inside the project like this: os.path.join(BASE_DIR, ...) BASE_DIR = Path(__file__).parent

INSTALLED_APPS = [
    # [...]
    'core',
]

STATIC_ROOT = BASE_DIR.parent.child('staticfiles')

STATIC_URL = '/static/'

MEDIA_ROOT = BASE_DIR.parent.child('uploads')

MEDIA_URL = '/media/' 
```

Enter fullscreen mode Exit fullscreen mode

让我们现在创建一个`YoutubeForm`并作为`form_class`添加到视图:

```
from django import forms
from django.views.generic.edit import FormView

class YouTubeForm(forms.Form):
    pass

class HomePageView(FormView):
    template_name = 'core/home.html'
    form_class = YouTubeForm 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试运行您的应用程序，页面将如下所示:

[![](img/04f82169571dd4318b32095f6133a8ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QUrhTg0p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/djpzc0k4joac4vb9lrcs.png)

### 暂停做授权

首先，您必须创建一个模型来存储您的凭证。您可以通过文件、缓存系统或任何其他存储解决方案，但数据库似乎是合理的和可伸缩的，如果您愿意，您还可以存储每个用户的凭据。

在继续之前，需要进行调整——我们必须使用支持 Django 2.1 的 oauth2client 的 fork。很快，我们将获得官方支持，但在此期间，您可以检查分叉更改。它们非常简单。

```
pip install -e git://github.com/Schweigi/oauth2client.git@v4.1.3#egg=oauth2client
# Because of compatibility with Django 2.0 
```

Enter fullscreen mode Exit fullscreen mode

转到您的`settings.py`，放置您在之前步骤中从谷歌获得的*客户 ID* 和*客户秘密*。

```
# settings.py 
GOOGLE_OAUTH2_CLIENT_ID = '<your client id>'
GOOGLE_OAUTH2_CLIENT_SECRET = '<your client secret>' 
```

Enter fullscreen mode Exit fullscreen mode

*注意:*不建议在代码中存储秘密。我这样做只是为了演示。我建议在您的生产应用程序中使用环境变量，而不要在应用程序文件中硬编码秘密。或者，如果你从 Google 下载了 JSON，你也可以指定它的路径而不是上面的设置:

```
GOOGLE_OAUTH2_CLIENT_SECRETS_JSON = '/path/to/client_id.json' 
```

Enter fullscreen mode Exit fullscreen mode

oauth2client 包已经提供了大量的功能，我们可以使用已经完成的 CredentialsField。可以添加更多的字段，如外键和创建/修改日期，这样我们会更健壮，但让我们保持简单。

存储凭证的简单模型:

```
# core/models.py from django.db import models
from oauth2client.contrib.django_util.models import CredentialsField

class CredentialsModel(models.Model):
    credential = CredentialsField() 
```

Enter fullscreen mode Exit fullscreen mode

创建迁移和迁移的时间:

```
(djangoyt) ➜  ~/projects/django-youtube $ ./manage.py makemigrations core
(djangoyt) ➜  ~/projects/django-youtube $ ./manage.py migrate 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们更改 API 视图，以便能够授权我们的应用程序:

在我们的`core/urls.py`文件中，让我们为第一个授权视图添加另一个条目:

```
# core/urls.py from .views import AuthorizeView, HomePageView

urlpatterns = [
    # [...]
    path('authorize/', AuthorizeView.as_view(), name='authorize'),
] 
```

Enter fullscreen mode Exit fullscreen mode

因此，AuthorizeView 的第一部分将是:

```
# core/views.py 
from django.conf import settings
from django.shortcuts import render, redirect
from django.views.generic.base import View

from oauth2client.client import flow_from_clientsecrets, OAuth2WebServerFlow
from oauth2client.contrib import xsrfutil
from oauth2client.contrib.django_util.storage import DjangoORMStorage
from .models import CredentialsModel

# [...] 
class AuthorizeView(View):

    def get(self, request, *args, **kwargs):
        storage = DjangoORMStorage(
            CredentialsModel, 'id', request.user.id, 'credential')
        credential = storage.get()
        flow = OAuth2WebServerFlow(
            client_id=settings.GOOGLE_OAUTH2_CLIENT_ID,
            client_secret=settings.GOOGLE_OAUTH2_CLIENT_SECRET,
            scope='https://www.googleapis.com/auth/youtube',
            redirect_uri='http://localhost:8888/oauth2callback/')

        # or if you downloaded the client_secrets file
        '''flow = flow_from_clientsecrets(
            settings.GOOGLE_OAUTH2_CLIENT_SECRETS_JSON,
            scope='https://www.googleapis.com/auth/youtube',
            redirect_uri='http://localhost:8888/oauth2callback/')''' 
```

Enter fullscreen mode Exit fullscreen mode

然后第二部分:

```
 if credential is None or credential.invalid == True:
            flow.params['state'] = xsrfutil.generate_token(
                settings.SECRET_KEY, request.user)
            authorize_url = flow.step1_get_authorize_url()
            return redirect(authorize_url)
        return redirect('/') 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果没有凭证或者凭证无效，就生成一个凭证，然后将其重定向到 authorize URL。否则，只需转到主页，这样我们就可以上传视频了！

让我们现在访问视图，看看会发生什么:

[![](img/7726d19cc663d91a228eee83360ac103.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7jcJZoo1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1pfxeq50chhxh3a0hebh.png)

在转到该页面之前，让我们创建一个用户。

```
(djangoyt) ➜  ~/projects/django-youtube $ python manage.py createsuperuser

Username (leave blank to use 'ivan'): ivan
Email address: ivan***@mail.com
Password:
Password (again):
This password is too short. It must contain at least 8 characters.
Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully. 
```

Enter fullscreen mode Exit fullscreen mode

我们也通过`/admin`用它登录吧。之后，让我们再次访问我们的`/authorize/`视图。

[![](img/dd76ab40f20fdf9b4380b26cc4321e90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7R97TICt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/38by08tj2pngg47d00tu.png)

[![](img/2147fc3ed5c08a6c7105b0fe506b7c51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x9Uc1pGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2hftmrnqwcyr8ryjpi1u.png)

然后，

[![](img/7d7957963e03512d964f7024f0c36dcc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ENOMPVst--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0fguhm74sffrebd81nmf.png)

好的，它试图重定向到我们很久以前用 Google 配置的回调 URL。现在我们需要实现回调视图。

让我们给我们的`core/urls.py` :
增加一个条目

```
# core/urls.py 
from .views import AuthorizeView, HomePageView, Oauth2CallbackView

urlpatterns = [
    # [...]
    path('oauth2callback/', Oauth2CallbackView.as_view(),
         name='oauth2callback')
] 
```

Enter fullscreen mode Exit fullscreen mode

还有另一种观点:

```
# core/views.py 

# the following variable stays as global for now flow = OAuth2WebServerFlow(
    client_id=settings.GOOGLE_OAUTH2_CLIENT_ID,
    client_secret=settings.GOOGLE_OAUTH2_CLIENT_SECRET,
    scope='https://www.googleapis.com/auth/youtube',
    redirect_uri='http://localhost:8888/oauth2callback/')
# or if you downloaded the client_secrets file '''flow = flow_from_clientsecrets(
    settings.GOOGLE_OAUTH2_CLIENT_SECRETS_JSON,
    scope='https://www.googleapis.com/auth/youtube',
    redirect_uri='http://localhost:8888/oauth2callback/')'''

# [...] 

class Oauth2CallbackView(View):

    def get(self, request, *args, **kwargs):
        if not xsrfutil.validate_token(
            settings.SECRET_KEY, request.GET.get('state').encode(),
            request.user):
                return HttpResponseBadRequest()
        credential = flow.step2_exchange(request.GET)
        storage = DjangoORMStorage(
            CredentialsModel, 'id', request.user.id, 'credential')
        storage.put(credential)
        return redirect('/') 
```

Enter fullscreen mode Exit fullscreen mode

*注:流程被移至`AuthorizeView`之外，成为全球化。理想情况下，您应该在`AuthorizeView`下创建它并保存在缓存中，然后在回调中检索它。但是这超出了本文的范围。*

AuthorizeView 的 get 方法现在是:

```
 def get(self, request, *args, **kwargs):
        storage = DjangoORMStorage(
            CredentialsModel, 'id', request.user.id, 'credential')
        credential = storage.get()

        if credential is None or credential.invalid == True:
            flow.params['state'] = xsrfutil.generate_token(
                settings.SECRET_KEY, request.user)
            authorize_url = flow.step1_get_authorize_url()
            return redirect(authorize_url)
        return redirect('/') 
```

Enter fullscreen mode Exit fullscreen mode

您可以在这里查看类似的实现。`oauth2clien`包本身提供了视图，但是我特别喜欢实现我的自定义 Oauth 视图。

*   [https://github . com/Google/Google-API-python-client/blob/master/samples/django _ sample/plus/views . py](https://github.com/google/google-api-python-client/blob/master/samples/django_sample/plus/views.py)
*   [https://github . com/Google/oauth 2 client/blob/master/oauth 2 client/contrib/django _ util/views . py](https://github.com/google/oauth2client/blob/master/oauth2client/contrib/django_util/views.py)

现在，如果您再次尝试使用`/authorize/` URL，OAuth 流应该可以工作了。是时候看看这项工作是否值得并上传我们的视频了！主页视图将首先检查凭证，如果一切正常，我们就可以上传视频了。

让我们检查一下`HomePageView`的新代码将会是什么样子:

```
import tempfile
from django.http import HttpResponse, HttpResponseBadRequest
from googleapiclient.discovery import build
from googleapiclient.http import MediaFileUpload

class HomePageView(FormView):
    template_name = 'core/home.html'
    form_class = YouTubeForm

    def form_valid(self, form):
        fname = form.cleaned_data['video'].temporary_file_path()

        storage = DjangoORMStorage(
            CredentialsModel, 'id', self.request.user.id, 'credential')
        credentials = storage.get()

        client = build('youtube', 'v3', credentials=credentials)

        body = {
            'snippet': {
                'title': 'My Django Youtube Video',
                'description': 'My Django Youtube Video Description',
                'tags': 'django,howto,video,api',
                'categoryId': '27'
            },
            'status': {
                'privacyStatus': 'unlisted'
            }
        }

        with tempfile.NamedTemporaryFile('wb', suffix='yt-django') as tmpfile:
            with open(fname, 'rb') as fileobj:
                tmpfile.write(fileobj.read())
                insert_request = client.videos().insert(
                    part=','.join(body.keys()),
                    body=body,
                    media_body=MediaFileUpload(
                        tmpfile.name, chunksize=-1, resumable=True)
                )
                insert_request.execute()

        return HttpResponse('It worked!') 
```

Enter fullscreen mode Exit fullscreen mode

和模板:

```
# core/templates/core/home.html

        <!DOCTYPE html>
        <html>
        <body>

        <h1>Upload your video</h1>
        <p>Here is the form:</p>
        <form action="." method="post" enctype="multipart/form-data">
        { csrf_token }
        \{\{ form.as_p \}\}
        <input type="submit" value="Submit">
        </form>

        </body>
        </html> 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记将视频字段添加到 YouTube eform:

```
class YouTubeForm(forms.Form):
    video = forms.FileField() 
```

Enter fullscreen mode Exit fullscreen mode

开始了。

[![](img/faa9d9585bf7ca5290ee7996fa15c7b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Goi8CuIL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9w7nlkyfmawiy64oe7a8.png)

[![](img/75bbbecc62b8c767fe82c3a9f4131e53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_nE18516--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hjm9q0z1gmnybcc0kyyg.png)

瞧啊。

### 期末备注

代码需要一些改进，但这是一个很好的起点。我希望它对谷歌的 YouTube API 集成问题有所帮助。这里有一些更重要的事情需要注意:

*   对于授权，重要的是需要用户的登录和额外权限，以授权您的应用程序上传视频。
*   流变量需要从全局变量中移出。在生产环境中是不安全的。例如，最好基于访问第一个视图的用户 ID 或会话进行缓存。
*   Google 只在你第一次授权的时候提供一个刷新令牌。因此，一段时间后，通常是一个小时后，您的令牌将过期，如果您没有与他们的 API 进行交互，您将开始收到 invalid_grant 响应。重新授权已经授权客户端的同一用户将无法保证您的刷新令牌。*您必须在您的 Google 帐户页面中撤销该应用程序，然后再次进行授权过程。在某些情况下，您可能需要运行任务来持续刷新令牌。
*   我们需要在视图中要求登录，因为我们使用的是与请求直接相关的用户凭证。
*   基于这段经历和这篇文章，有一项工作正在进行中。当然还有很多要补充的，但这是一个很好的起点:[https://github.com/ivancrneto/youtube-django](https://github.com/ivancrneto/youtube-django)

[![](img/f4ef1ef7418ef053c3ff6ebbcc26ccec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Aj0tT9TF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s1r6istovdehyhzn2hd2.png)

上传需要很多时间，在主应用程序进程中上传会导致整个应用程序在上传过程中阻塞。正确的方法是将它移到自己的进程中，并异步处理上传。

代码需要一些改进，但这是一个很好的起点。我希望它有助于谷歌的 API 处理，当然还有上传你的视频到 YouTube！

干杯！