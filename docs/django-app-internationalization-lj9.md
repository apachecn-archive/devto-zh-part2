# Django 应用程序国际化

> 原文：<https://dev.to/levivm/django-app-internationalization-lj9>

## 国际化

很多时候，我们面临的问题是如何构建我们的应用程序，使它们本身可以在多种语言中使用。那就是我们所说的，**国际化**。

让我们看看如何将它归档。

## 姜戈翻译

在开始之前，让我们克隆我们的基本代码(cat-quotes)，设置 virtualenv 并初始化项目。

```
python -m venv cat-quotes-project # Creating our virtualenv
cd cat-quotes-project
source bin/activate
git clone git@github.com:levivm/cat-quotes.git cat-quotes
cd cat-quotes
pip install -r requirements.txt
./manage.py migrate 
```

Enter fullscreen mode Exit fullscreen mode

这应该是我们的目录结构

```
cat
├── cat
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── db.sqlite3
├── manage.py
└── quotes
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── migrations
    │   └── __init__.py
    ├── models.py
    ├── templates
    │   └── quotes.html
    ├── tests.py
    ├── urls.py
    └── views.py 
```

Enter fullscreen mode Exit fullscreen mode

运行您的服务器`./manage.py runserver 8000`并访问`http://localhost:8000/cats/quotes/`。你应该这样看

[![Alt text of image](img/d1a47e4d71fd5866139d7b0227fa6425.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AgHGwWoz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://levipy.com/assets/posts/screen_2-6aa782bc305b6afd1a858bf0bef5da141152ba7c0970f3969b5cff5e471945b6.png)

翻译 django 应用程序有两个主要步骤。第一个是标记应该翻译的字符串，第二个是生成语言文件，您可以在其中为这些标记的字符串定义已翻译的字符串。

### 标记字符串。

Django 提供了两个函数来定义翻译字符串。他们是`ugettext()`和`ugettext_lazy()`。

我们要用`ugettext_lazy()`。所以，我们需要开始标记。

让我们找到我们的猫报价，打开`quotes/views.py`

```
from django.views.generic import TemplateView

class CatQuotesView(TemplateView):
    template_name = "quotes.html"

    def get_context_data(self, *args, **kwargs):
        context = super(CatQuotesView, self).get_context_data(**kwargs)

        cat_quote_one = "I'm bilingual cat and know how to meow in several languages: Meow."
        cat_quote_two = "I'm not angry, go to kill yourself."
        context.update({
            'cat_quote_one': cat_quote_one,
            'cat_quote_two': cat_quote_two
        })
        return context 
```

Enter fullscreen mode Exit fullscreen mode

导入`ugettext_lazy`

```
from django.utils.translation import ugettext_lazy as _ 
```

Enter fullscreen mode Exit fullscreen mode

使用`_()`结束我们的卡特彼勒报价。别担心，`_`只是一个别名，如果你愿意，你可以用`ugettext_lazy()`。

```
cat_quote_one = _("I'm bilingual cat and know how to meow in several languages: Meow.")
cat_quote_two = _("I'm not angry, go to kill yourself.") 
```

Enter fullscreen mode Exit fullscreen mode

所以，我们的观点就这样结束了

```
from django.views.generic import TemplateView
from django.utils.translation import ugettext_lazy as _

class CatQuotesView(TemplateView):
    template_name = "quotes.html"

    def get_context_data(self, *args, **kwargs):
        context = super(CatQuotesView, self).get_context_data(**kwargs)

        cat_quote_one = _("I'm bilingual cat and know how to meow in several languages: Meow.")
        cat_quote_two = _("I'm not angry, go to kill yourself.")
        context.update({
            'cat_quote_one': cat_quote_one,
            'cat_quote_two': cat_quote_two
        })
        return context 
```

Enter fullscreen mode Exit fullscreen mode

### 翻译标记字符串或翻译字符串。

在开始允许字符串翻译之前，我们需要在我们的`quotes`应用程序中创建一个名为`locale`的文件夹。同样，如果你有更多的应用程序，你只需要为每个你想翻译的程序创建一个。在`locale`中的 dir 是我们所有翻译文件的存放位置。

让我们为翻译字符串创建一个消息文件。该文件是代表单一语言的纯文本。它包含所有可用的翻译字符串(我们标记的字符串)以及应该如何翻译它们。那些文件有一个**。po** 文件扩展名。

为了创建或更新我们的消息文件，我们将使用 Django 提供的命令`makemessages`。

注意:我们需要[安装 *GNU gettext 工具集*T3】](https://stackoverflow.com/questions/27220052/django-i18n-make-sure-you-have-gnu-gettext-tools)

要创建或更新消息文件，我们需要在包含标记字符串的应用程序的根目录下运行命令。在我们的例子中`quotes/`。

```
django-admin makemessages -l es_ES 
```

Enter fullscreen mode Exit fullscreen mode

其中 **es_ES** 代表消息文件的本地名称。例如， **en-US** 代表美国英语， **de** 代表德语等等。

它将重新收集我们所有标记的字符串并更新`.po`文件。然后，我们需要打开。po 文件，并为每个字符串设置正确的翻译。

运行该命令后，您的`quotes`应用程序目录应该如下所示:

```
├── __init__.py
├── admin.py
├── apps.py
├── locale
│   └── es-ES
│       └── LC_MESSAGES
│           └── django.po
├── migrations
│   └── __init__.py
├── models.py
├── templates
│   └── quotes.html
├── tests.py
├── urls.py
└── views.py 
```

Enter fullscreen mode Exit fullscreen mode

我们得到了一个名为`es-ES`(西班牙语翻译文件的文件夹)的新文件夹，其中有一个文件夹 LC_MESSAGES，包含一个`.po`文件。该文件应该有这样的条目:

```
#: views.py:11 msgid "I'm bilingual cat and know how to meow in several languages: Meow."
msgstr ""

#: views.py:12 msgid "I'm not angry, go to kill yourself."
msgstr "" 
```

Enter fullscreen mode Exit fullscreen mode

`msgid`是翻译字符串，出现在源代码中。不要改变它。

是你放置字符串翻译的地方。开始它会是空的，你应该改变它。

```
#: views.py:11 msgid "I'm bilingual cat and know how to meow in several languages: Meow."
msgstr "Soy un gato bilingüe y sé como hacer meow en varios idiomas: Meow."

#: views.py:12 msgid "I'm not angry, go to kill yourself."
msgstr "No estoy molesto, mátate." 
```

Enter fullscreen mode Exit fullscreen mode

创建消息文件后，我们需要将其转换成更有效的形式，即**。mo** 文件扩展名。这是一个优化的二进制文件。

为了创建这些文件，我们需要使用项目根目录中的命令
`django-admin compilemessages`。

它将从一个`.po`扩展文件创建或更新我们的`.mo`文件。

## Django 设置

我们需要向 Django 应用程序表明我们想要使用一种额外的语言。默认情况下，Django 用这个值设置`LANGUAGE`配置变量:

```
LANGUAGES = [
    ('en-us', 'English'),
] 
```

Enter fullscreen mode Exit fullscreen mode

我们将在 django app 可用语言中添加西班牙语`('es-ES', _('Spanish'))`(注意，是`es-ES`不是`es_ES`)。

```
LANGUAGES = [
    ('en-us','English'),
    ('es-ES', 'Spanish')
] 
```

Enter fullscreen mode Exit fullscreen mode

有几种方法可以激活/使用 Django 翻译。手动或通过 URL 前缀。

## 手动激活翻译

让我们在视图中激活西班牙语，为了做到这一点，我们需要导入`translation` utils 并使用`translation.activate()`激活它。它需要一个语言代码作为参数。你可以做任何你想做的事，`views.py`，`models.py`或者任何 Django 文件。

```
from django.views.generic import TemplateView
from django.utils.translation import ugettext_lazy as _

from django.utils import translation

class CatQuotesView(TemplateView):
    template_name = "quotes.html"

    def get_context_data(self, *args, **kwargs):
        context = super(CatQuotesView, self).get_context_data(**kwargs)

        SPANISH_LANGUAGE_CODE = 'es-ES'
        translation.activate(SPANISH_LANGUAGE_CODE)

        cat_quote_one = _("I'm bilingual cat and know how to meow in several languages: Meow.")
        cat_quote_two = _("I'm not angry, go to kill yourself.")
        context.update({
            'cat_quote_one': cat_quote_one,
            'cat_quote_two': cat_quote_two
        })
        return context 
```

Enter fullscreen mode Exit fullscreen mode

让我们再次访问我们的猫报价`http://localhost:8000/cats/quotes/`。

[![Alt text of image](img/d1a47e4d71fd5866139d7b0227fa6425.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AgHGwWoz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://levipy.com/assets/posts/screen_2-6aa782bc305b6afd1a858bf0bef5da141152ba7c0970f3969b5cff5e471945b6.png)

[![Alt text of image](img/f4ea975d5fc0cf3d14bf0d51a2879b5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YvJ9JXHt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://levipy.com/assets/posts/screen_3-9c88ed4f9b03ea58d585dd4c1c5dc4b374221a9feaef35a2999aab6db151f119.png)

所以，我们把引语翻译成了西班牙语。

### 在模板中使用字符串翻译

除了在我们的视图中标记要翻译的字符串，我们还可以在 Django 模板中这样做。在这之前，我们需要给我们模板一些类型的标签，把{% raw %} `{% load i18n %}`放在模板的顶部。因此，之后我们将使用`{% trans %}`标签。让我们将我们的页面标题*中的“给 dummys 的猫的报价”*添加到`quotes/templates/quotes.html`中，并将其翻译成西班牙语。

```
{% load i18n %}
...............................
...............................
<div class="col-md-8 col-md-offset-2">
    <div>
        <h3 class="text-center">{% trans "Cat quotes for dummys." %}</h3>
    </div>
    <br/>
    <div class="quote"><i class="fa fa-quote-left fa-4x"></i></div>
    <div class="carousel slide" id="fade-quote-carousel" data-ride="carousel" data-interval="3000">
      <!-- Carousel indicators -->
      <ol class="carousel-indicators">
        <li data-target="#fade-quote-carousel" data-slide-to="0" class="active"></li>
        <li data-target="#fade-quote-carousel" data-slide-to="1"></li>
      </ol>
      <!-- Carousel items -->
      <div class="carousel-inner">
        <div class="item active">
            <img class="profile-circle img-responsive" src="https://s-media-cache-ak0.pinimg.com/564x/12/f6/d1/12f6d18125126757df29e733051697b8.jpg" alt="">
            <blockquote>
                <p>{{cat_quote_one}}</p>
            </blockquote>
        </div>
        <div class="item">
            <img class="profile-circle img-responsive" src="https://66.media.tumblr.com/avatar_ad6c1cb12b85_128.png" alt="">
            <blockquote>
                <p>{{cat_quote_two}}</p>
            </blockquote>
        </div>
      </div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

看起来应该是这样的
[![Alt text of image](img/a81cdb5cc047974e73282c53aae0fb86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Z5MTYac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://levipy.com/assets/posts/screen_4-ae580ef3dd11bd88abbe1db2e8548cb2958fa65d48c1fe7491d47ab9e91fb2c7.png)

我们的标题已经标记为翻译。要更新我们的翻译文件，在`quotes`应用根目录中重新运行`django-admin makemessages -l es_ES`命令，并在我们的`.po`文件`quotes/locale/es_ES/LC_MESSAGES/django.po`
中添加新条目的翻译

```
#: templates/quotes.html:81 msgid "Cat quotes for dummys."
msgstr "Citas de gatos para principiantes." 
```

Enter fullscreen mode Exit fullscreen mode

使用`django-admin compilemessages`再次编译，我们应该得到这个。

[![cat](img/50d62a135ba8335c4a2a9600ae105897.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LZGFJQ5i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://levipy.com/assets/posts/screen_5-34d5d974a41d2c9dcaefaca5a7a2146a4d0cf1c231a2c678a55d34b52948fd48.png)

**注意:**记住，我们已经在视图中将西班牙语激活为默认语言。

## 通过 URL 前缀激活翻译

正如你所注意到的，手动激活和关闭我们的语言是如此的乏味。更重要的是，我们希望能够在我们的 URL 中使用给定前缀的特定语言。

向 URL 模式的根添加语言前缀，使`LocaleMiddleware`能够从请求的 URL 中检测要激活的语言。

为此，首先，我们需要在我们的`MIDDLEWARE`设置中添加`django.middleware.locale.LocaleMiddleware`。

```
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'django.middleware.locale.LocaleMiddleware'
] 
```

Enter fullscreen mode Exit fullscreen mode

该中间件负责从我们的 url lang 前缀激活一种语言，即`en-us/any_url`。

此外，我们应该允许在我们的网址语言代码。使用`i18n_patterns()`我们可以将其存档。

这个函数可以在一个根 URLconf 中使用，Django 会自动将当前的活动语言代码添加到所有定义的 URL 模式中。

我们的新`/cats/urls.py`应该是这样的

```
from django.conf.urls import url, include
from django.contrib import admin
from django.conf.urls.i18n import i18n_patterns

urlpatterns = [
    url(r'^admin/', admin.site.urls),
]

urlpatterns += i18n_patterns(
    url(r'cats/', include('quotes.urls', namespace='cat_quotes')),
) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要从视图中移除手动语言激活。我们不再需要它了。

```
 from django.views.generic import TemplateView
from django.utils.translation import ugettext_lazy as _

class CatQuotesView(TemplateView):
    template_name = "quotes.html"

    def get_context_data(self, *args, **kwargs):
        context = super(CatQuotesView, self).get_context_data(**kwargs)

        cat_quote_one = _("I'm bilingual cat and know how to meow in several languages: Meow.")
        cat_quote_two = _("I'm not angry, go to kill yourself.")
        context.update({
            'cat_quote_one': cat_quote_one,
            'cat_quote_two': cat_quote_two
        })
        return context 
```

Enter fullscreen mode Exit fullscreen mode

让我们一起去参观`http://localhost:8000/en-us/cats/quotes/`

[![cat](img/bd289af2be2beb5513aa76fc799e0c66.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lD-0u_VO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://levipy.com/assets/posts/screen_6-f2e070ce95d7618656453d2a8265c608bd67a1152164e8bbd8778db25c357842.png)

现在，我们的西班牙语版本`http://localhost:8000/es-es/cats/quotes/`

[![cat](img/e67c1b22bc6982b9c974a2b82c082a80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rlSiKeqz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://levipy.com/assets/posts/screen_7-7a843beeff8e1f3c3c476a6f3f60cda34203804480de80057bacef5a82ec2dda.png)

**注意:**我们 url 中的语言前缀必须与我们在`settings.py`文件中定义的语言代码完全匹配。

也就是说，我们将卡特彼勒报价应用程序翻译成了两种语言。

如果你觉得这篇文章有用，请分享并传播。