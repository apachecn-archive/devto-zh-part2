# 移植到 pytest:一个实际的例子

> 原文：<https://dev.to/dmerejkowsky/porting-to-pytest-a-practical-example-12hm>

*最初发表于[我的博客](https://dmerej.info/blog/post/porting-to-pytest-a-practical-example/)。*

# 简介

前几天，我正在学习 django 教程。

如果你从来没有读过这个教程，或者不想读，下面是你需要知道的:

我们有一个 django 项目，其中包含一个名为`polls`的应用程序。

我们有两个表示问题和选择的模型对象。

每个问题都有发表日期、正文和选项列表。

每个选项都有一个对现有问题的引用(通过一个外键)、一个文本和一些投票。

有一个视图将问题列表显示为链接。每个链接在被点击时都会显示选项，并有一个让用户投票的表单。

代码非常简单:

```
# polls/models.py class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

    def was_published_recently(self):
        now = timezone.now()
        two_days_ago = now - datetime.timedelta(days=2)
        return two_days_ago < self.pub_date <= now

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200) 
```

Enter fullscreen mode Exit fullscreen mode

一切都进行得很顺利，直到我读到了关于自动化测试的第 5 部分，在那里我读到了以下内容:

> 有时候，将自己从生产性的、创造性的编程工作中分离出来，去面对单调乏味的编写测试的工作，似乎是一件苦差事，尤其是当你知道你的代码工作正常的时候。

好吧，允许我反驳！

# 起点:使用文档中的测试

下面是从 django 文档中提取出来的测试内容:

```
import datetime

from django.utils import timezone
from django.test import TestCase

from .models import Question

class QuestionModelTests(TestCase):
    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() returns False for questions whose pub_date
        is in the future.
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)

    def test_was_published_recently_with_old_question(self);
        """
        was_published_recently() returns False for questions whose pub_date
        is older than 1 day.
        """
        time = timezone.now() - datetime.timedelta(days=1, seconds=1)
        old_question = Question(pub_date=time)
        self.assertIs(old_question.was_published_recently(), False)

    def test_was_published_recently_with_recent_question(self):
        """
        was_published_recently() returns True for questions whose pub_date
        is within the last day.
        """
        time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
        recent_question = Question(pub_date=time)
        self.assertIs(recent_question.was_published_recently(), True)

def create_question(question_text, days):
    """
    Create a question with the given `question_text` and published the
    given number of `days` offset to now (negative for questions published
    in the past, positive for questions that have yet to be published).
    """
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)

class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        """
        If no questions exist, an appropriate message is displayed.
        """
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            []
        )

    def test_past_question(self):
        """
        Questions with a pub_date in the past are displayed on the
        index page.
        """
        create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_future_question(self):
        """
        Questions with a pub_date in the future aren't displayed on
        the index page.
        """
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            []
        )

    def test_future_question_and_past_question(self):
        """
        Even if both past and future questions exist, only past questions
        are displayed.
        """
        create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_two_past_questions(self):
        """
        The questions index page may display multiple questions.
        """
        create_question(question_text="Past question 1.", days=-30)
        create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            [
                '<Question: Past question 2.>',
                '<Question: Past question 1.>'
            ]
        ) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用`manage.py`脚本运行它们，并检查它们是否都通过:

```
$  python manage.py test polls
Creating test database for alias 'default'...
System check identified no issues (0 silenced). ........ ---------------------------------------------------------------------------
Ran 8 tests in 0.017s

OK
Destroying test database for alias 'default'... 
```

Enter fullscreen mode Exit fullscreen mode

好吧，测试通过了。让我们试着改进它们。

我已经建立了一个 GitHub 库,如果你愿意，你可以按照下面的步骤逐个提交。

# 第一步:设置 pytest

我已经告诉过你[我有多爱 pytest](https://dmerej.info/blog/post/pytest-rocks/) ，所以让我们试着皈依`pytest`。

第一步是安装`pytest-django`并配置:

```
$  pip install pytest pytest-django 
```

Enter fullscreen mode Exit fullscreen mode

```
# in pytest.ini
[pytest]
DJANGO_SETTINGS_MODULE=mysite.settings
python_files = tests.py test_*.py 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以直接使用`pytest`运行测试:

```
$  pytest
========== test session starts ========
platform linux -- Python 3.5.3, pytest-3.3.1, py-1.5.2, pluggy-0.6.0
Django settings: mysite.settings (from ini file)
rootdir: /home/dmerej/src/dmerej/django-polls, inifile: pytest.ini
plugins: django-3.1.2
collected 8 items

polls/tests.py ........   [100%]

======== 8 passed in 0.18 seconds ======= 
```

Enter fullscreen mode Exit fullscreen mode

# 第二步:重写断言

我们现在可以使用`pytest`魔法来重写所有“简单”的断言，比如`assertFalse`或`assertEquals` :

```
- self.assertFalse(future_question.was_published_recently()) + assert not future_question.was_published_recently() 
```

Enter fullscreen mode Exit fullscreen mode

我们已经可以看到一些改进:

*   代码可读性更强，遵循 PEP8
*   错误消息更加详细:

```
#  Before, with unittest
$  python manage.py test
 def test_was_published_recently_with_future_question(self): ... >  self.assertFalse(question.was_published_recently())
E       AssertionError: True is not false #  After, with pytest
$  pytest
>  assert not question.was_published_recently()
E       AssertionError: assert not True E        +  where True = <bound method was_published_recently() of Question> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们必须处理看起来有点 django 特有的`assertContains`和`assertQuerysetEqual`。

对于`assertContains`，我很快发现我可以用`response.rendered_content`来代替:

```
- self.assertContains(response, "No polls are available.") + assert "No polls are available." in response.rendered_content 
```

Enter fullscreen mode Exit fullscreen mode

这有点难。

```
def test_past_question(self):
    create_question(question_text="Past question.", days=-30)
    response = self.client.get(reverse('polls:index'))
    self.assertQuerysetEqual(
        response.context['latest_question_list'],
        ['<Question: Past question.>']
    ) 
```

Enter fullscreen mode Exit fullscreen mode

该测试检查用于生成响应的上下文是否传递了正确的`latest_question_list`值。

但是它是通过检查`Question`对象的*字符串表示*来实现的。

这样一来，`Question.__str__`一变就破，不太理想。

因此，我们可以这样写，直接检查`question_text`属性的内容:

```
def test_past_question(self):
    create_question(question_text="Past question.", days=-30)
    response = self.client.get(reverse('polls:index'))
    actual_questions = response.context['latest_question_list']
    assert len(actual_questions) == 1
    actual_question = actual_questions[0]
    assert actual_question.question_text == "Past question" 
```

Enter fullscreen mode Exit fullscreen mode

当我们这样做的时候，我们可以引入一些小的帮助函数来使测试更容易阅读:

例如，字符串`No polls are available`在测试中被硬编码了两次。先介绍一个`assert_no_polls`帮手:

```
def assert_no_polls(text):
    assert "No polls are available" in text 
```

Enter fullscreen mode Exit fullscreen mode

```
- assert "No polls are available." in response.rendered_content + assert_no_polls(response.rendered_content) 
```

Enter fullscreen mode Exit fullscreen mode

另一个硬编码的字符串是`polls:index`，所以我们来介绍一下`get_latest_list` :

```
def get_latest_list(client):
    response = client.get(reverse('polls:index'))
    assert response.status_code == 200
    return response.context['latest_question_list'] 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们是如何将状态代码检查直接嵌入到我们的助手中的，这样我们就不必在每个测试中重复检查。

另外，请注意，如果路线的名称(`polls:index`)或者模板中使用的上下文关键字的名称(`latest_question_list`)发生了变化，我们只需要在一个地方更新测试代码。

然后，我们可以进一步简化我们的断言:

```
def assert_question_list_equals(actual_questions, expected_texts):
    assert len(actual_questions) == len(expected_texts)
    for actual_question, expected_text in zip(actual_questions, expected_texts):
        assert actual_question.question_text == expected_text

def test_past_question(self):
    ...
    create_question(question_text="Past question.", days=-30)
    latest_list = get_latest_list(self.client)
    assert_question_list_equals(latest_list, ["Past question."]) 
```

Enter fullscreen mode Exit fullscreen mode

# 第三步:将代码移出类

关于`pytest`的好处是你不需要把你的测试作为一个类的方法，你可以直接写
测试函数。

所以我们只需移除`self`参数，缩进所有代码，我们就(几乎)准备好了。

我们已经去掉了所有的`self.assert*`方法，所以最后要做的是将 Django 测试客户端作为参数通过，而不是使用`self.client`。(这就是[pytest fights](https://docs.pytest.org/en/latest/fixture.html)的工作方式):

```
-    def test_two_past_questions(self):
-        ...
-        latest_list = get_latest_list(self.client) 
+ def test_no_questions(client):
+    latest_list = get_latest_list(client) 
```

Enter fullscreen mode Exit fullscreen mode

但是接下来我们遇到了一个意想不到的失败:

```
Polls/tests.py:34: in create_question
    return Question.objects.create(question_text=question_text, pub_date=time)

    ...

>       self.ensure_connection()
E       Failed: Database access not allowed, use the "django_db" mark, or the "db" or "transactional_db" fixtures to enable it. 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用`python manage.py test`时，django 的`manage.py`脚本隐式地为我们创建了一个测试数据库。

当我们使用`pytest`时，我们必须明确它，并添加一个特殊的标记:

```
import pytest

# No change here, no need for a DB def test_was_published_recently_with_old_question():
    ...

# We use create_question, which in turn calls Question.objects.create(),
# so we need a database here: @pytest.mark.django_db
def test_no_questions(client):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

确实，这有点烦人，但是请注意，如果我们只想测试模型本身(像`was_published_recently()`方法)，我们可以只使用:

```
$  pytest -k was_published_recently 
```

Enter fullscreen mode Exit fullscreen mode

并且根本不会创建数据库*。*

# 第四步:去掉文档串

我不喜欢文档字符串，*除了*当我实现一个非常公共的 API 时。好了，我已经说了。

我非常喜欢代码是“自文档化的”，尤其是测试代码。

正如鲍勃叔叔所说，“测试应该像写得很好的规范一样”。所以让我们尝试一些重构。

我们可以从更有意义的变量名开始，并从例子中获得更多乐趣:

```
def test_was_published_recently_with_old_question(): -   time = timezone.now() - datetime.timedelta(days=1, seconds=1)
-   old_question = Question(pub_date=time) +   last_year = timezone.now() - datetime.timedelta(days=365)
+   old_question = Question('Why is there something instead of nothing?',
+                            pub_date=last_year)
    assert not old_question.was_published_recently()

def test_was_published_recently_with_recent_question():
-   time = timezone.now() - datetime.timedelta(days=1, seconds=1)
-   recent_question = Question(pub_date=time) +   last_night = timezone.now() - datetime.timedelta(hours=10)
+   recent_question = Question('Dude, where is my car?', pub_date=last_night) 
```

Enter fullscreen mode Exit fullscreen mode

时间和日期代码总是很复杂，负的天数实际上没有意义，所以让我们更容易推理:

```
def n_days_ago(n):
    return timezone.now() - datetime.timedelta(days=n)

def n_days_later(n):
    return timezone.now() + datetime.timedelta(days=n) 
```

Enter fullscreen mode Exit fullscreen mode

另外,`create_question`与`Question`模型相耦合，所以让我们使用相同的参数名和模型属性名。

因为我们可能想创建问题而不关心发布日期，所以让它成为一个可选参数:

```
def create_question(question_text, *, pub_date=None):
    if not pub_date:
        pub_date = timezone.now()
    ... 
```

Enter fullscreen mode Exit fullscreen mode

代码变成:

```
-    create_question(question_text="Past question.", days=-30) +    create_question(question_text="Past question.", pub_date=n_days_ago(30)) 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们添加一个新的测试，看看我们的助手是否真的有效:

```
 @pytest.mark.django_db
def test_latest_five(client):
    for i in range(0, 10):
        pub_date = n_days_ago(i)
        create_question("Question #%s" % i, pub_date=pub_date)
    latest_list = get_latest_list(client)
    assert len(actual_list) == 5 
```

Enter fullscreen mode Exit fullscreen mode

你仍然认为这个测试需要 docstring 吗？

# 第五步:硒的乐趣

## 硒基础知识

[Selenium](https://www.seleniumhq.org/) 处理浏览器自动化。

这里我们将使用 Python 绑定，它允许我们启动`Firefox`或`Chrome`并用代码控制它们。

(在这两种情况下，您都需要分别安装一个单独的二进制文件:`geckodriver`或`chromdriver`)

以下是如何使用`selenium`访问一个网页并点击第一个链接:

```
import selenium.webdriver

driver = selenium.webdriver.Firefox()
# or driver = selenium.webdriver.Chrome()
driver.get("http://example.com")
link = driver.find_element_by_tag_name('a')
link.click() 
```

Enter fullscreen mode Exit fullscreen mode

## 现场服务器测试案例

Django 公开了一个`LiveServerTestCase`，但是没有`LiveServer`对象或类似的对象。

代码有点复杂，因为它需要在一个单独的线程中生成一个“真正的”服务器，确保它使用一个空闲端口，并告诉 selenium 驱动程序使用类似于`http://localhost:32456`的 URL

不要害怕，`pytest`在这种情况下也能正常工作。我们只需小心地在设置和拆除方法中使用`super()`，这样来自`LiveServerTestCase`的代码就能正确执行:

```
import urllib.parse

class TestPolls(LiveServerTestCase):
    serialized_rollback = True

    def setUp(self):
        super().setUp()
        self.driver = selenium.webdriver.Firefox()

    def tearDown(self):
        self.driver.close()
        super().tearDown()

    def test_home_no_polls(self):
        url = urllib.parse.urljoin(self.live_server_url, "/polls")
        self.driver.get(url)
        assert_no_polls(self.browser.page_source) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道为什么我们需要`serialized_rollback=True`，答案就在[文档](https://docs.djangoproject.com/en/2.0/topics/testing/tools/#transactiontestcase)中。没有它，我们可能会在测试中出现奇怪的数据库错误。

我们的第一个测试非常简单:我们要求浏览器访问`'polls/` URL 并检查没有显示任何投票，重用我们之前的`assert_no_polls`助手函数。

我们还要检查是否显示了问题的链接，并可以点击这些链接:

```
class TestPolls(LiveServerTestCase):
    ...
    def test_home_list_polls(self):
        create_question("One?")
        create_question("Two?")
        create_question("Three?")
        url = urllib.parse.urljoin(self.live_server_url, "polls/")
        self.driver.get(url)
        first_link = self.driver.find_element_by_tag_name("a")
        first_link.click()
        assert "Three?" in self.driver.page_source 
```

Enter fullscreen mode Exit fullscreen mode

## 让我们建一个门面

selenium API 的`find_element_by_*`方法使用起来有点繁琐:它们被称为`find_element_by_tag_name`、`find_element_by_class_name`、`find_element_by_id`等等

因此，让我们编写一个`Browser`类来隐藏那些隐藏在更“Pythonic 化”的 API 后面的东西:

```
# old link = driver.find_element_by_tag_name("link")
form = driver.find_element_by_id("form-id")

# new link = driver.find_element(tag_name="link")
form = driver.find_element(id="form-id") 
```

Enter fullscreen mode Exit fullscreen mode

(这被称为“立面”设计模式)

```
class Browser:
    """ A nice facade on top of selenium stuff """
    def __init__(self, driver):
        self.driver = driver

    def find_element(self, **kwargs):
        assert len(kwargs) == 1   # we want exactly one named parameter here
        name, value = list(kwargs.items())[0]
        func_name = "find_element_by_" + name
        func = getattr(self.driver, func_name)
        return func(value) 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们如何将`items()`转换成一个真实的列表来获取第一个元素...(在 Python2 中，`kwargs.items()[0]`本来可以工作得很好)。如果你找到更好的方法，请告诉我...

还要注意我们*不仅仅是从`selenium.webdriver.Firefox`继承*。目标是公开一个*不同的* API，所以在这里使用组合更好。

如果我们需要访问`self.driver`的属性，我们可以使用一个属性，就像这样:

```
class Browser

    ...

    @property
    def page_source(self):
        return self.driver.page_source 
```

Enter fullscreen mode Exit fullscreen mode

如果我们需要直接调用底层对象的方法，我们可以直接调用:

```
 def close(self):
        self.driver.close() 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以隐藏难看的`urllib.parse.urljoin(self.live_server_url)`实现细节:

```
 class Browser:
    def __init__(self, driver):
        self.driver = driver
        self.live_server_url = None  # will be set during test set up 
    def get(self, url):
        full_url = urllib.parse.urljoin(self.live_server_url, url)
        self.driver.get(full_url)

class TestPolls(LiveServerTestCase):

    def setUp(self):
        super().setUp()
        driver = selenium.webdriver.Firefox()
        self.browser = Browser(driver)
        self.browser.live_server_url = self.live_server_url 
```

Enter fullscreen mode Exit fullscreen mode

现在测试显示:

```
 def test_home_no_polls(self):
        self.browser.get("/polls")
        assert_no_polls(self.browser.page_source) 
```

Enter fullscreen mode Exit fullscreen mode

好看又短:)

## 只启动一次驱动程序

在每次测试之前都会调用`setUp()`方法，所以如果我们添加更多的测试，我们将会创建大量的 Firefox 驱动程序实例。

让我们通过使用`setUpClass`(别忘了`super()`调用)
来解决这个问题

```
class TestPolls(LiveServerTestCase):

    @classmethod
    def setUpClass(cls):
        super().setUpClass()
        driver = webdriver.Chrome()
        cls.browser = Browser(driver)

    def setUp(self):
        self.browser.base_url = self.live_server_url

    @classmethod
    def tearDownClass(cls):
        cls.browser.close()
        super().tearDownClass() 
```

Enter fullscreen mode Exit fullscreen mode

现在`browser`是一个*类属性*，而不是一个*实例属性*。所以整个测试套件只有一个`Browser`对象，这正是我们想要的。

尽管如此，代码的其余部分仍然可以使用`self.browser`。

## 调试测试

最后一件事。您可能认为调试这样的高级测试会很痛苦。

但这实际上是一个非常好的体验，因为只有一件事:内置的 Python 调试器！

只需添加如下内容:

```
 def test_login():
    self.browser.get("/login")
    import pdb; pdb.set_trace() 
```

Enter fullscreen mode Exit fullscreen mode

然后像这样运行测试:

```
$  pytest -k login -s 
```

Enter fullscreen mode Exit fullscreen mode

(需要`-s`来避免捕获输出，这是`pdp`不喜欢的)

然后，一旦测试到达带有`pdb.set_trace()`的那一行，您将拥有:

*   一个全新的 Firefox 实例正在运行，可以访问所有优秀的调试工具(这样您就可以快速找到 id 或 CSS 类名之类的东西)
*   ...还有一个不错的 REPL，在这里你可以使用`self.browser`来测试代码

顺便说一下，如果你使用 [ipdb](https://pypi.python.org/pypi/ipdb) 或 [pdbpp](https://pypi.python.org/pypi/pdbpp/) 并享受来自 REPL 的自动完成和语法着色的权利，REPL 会更好:)

# 结论

我希望我成功地展示了你实际上可以得到创造性写作测试，甚至可以从中得到乐趣。

下次见！

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。