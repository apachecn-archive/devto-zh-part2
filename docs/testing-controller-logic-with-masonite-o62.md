# 用 Masonite 测试控制器逻辑

> 原文：<https://dev.to/masonite/testing-controller-logic-with-masonite-o62>

# 简介

控制器如此构造的原因之一是它们变得可测试。事实上，Masonite 本身是极其可测试的。主包有过多的特性，因为它包含了整个框架，并且仍然有大约 91%的代码覆盖率。

让我们举一个典型控制器的例子，并展示我们如何修改它，使其更具可测试性。我觉得通读这篇文章的最好方式是给出一个真实世界的例子，然后我们如何修改它以通过测试。

Masonite 是非常可测试的，但是它允许一些不可测试的事情。例如，Masonite 带有内置助手，这使得代码的各个部分不容易在不模仿内置助手的情况下进行测试(这有点奇怪)。

我将撰写一些关于如何重构各种控制器方法的文章，所以请务必关注。

# 我们的例子

我们将保持它非常简单，这样我们可以在不损失任何人的情况下完成所有的基础工作。下面是我们的 Masonite 控制器示例:

```
 class PlanController:
    """ Manage User Subscriptions """

    def subscribe(self):
        plan = request().input('plan_id')
        request().user().subscribe(plan, request().input('stripeToken'))

        return request().redirect('/plans') 
```

Enter fullscreen mode Exit fullscreen mode

# 问题 1

如果我们去测试它，它可能看起来像这样:

```
from app.http.controllers.PlanController import PlanController

class TestPlanController:

    def setup_method(self):
        self.controller = PlanController()

    def test_controller_subscribes_user(self):
        assert self.controller.show() 
```

Enter fullscreen mode Exit fullscreen mode

这将开始抛出一大堆错误，因为:

1.  Python 测试不知道`request()`到底是什么，因为它是一个内置函数，当应用程序第一次启动时由服务提供商激活。
2.  它不能正确地获取输入，因为我们从未指定输入。

# 重构

让我们重构控制器，这样我们至少可以传入一个请求:

```
from masonite.request import Request

class PlanController:
    """ Manage User Subscriptions """

    def subscribe(self, request: Request):
        plan = request.input('plan_id')
        request.user().subscribe(plan, request.input('stripeToken'))

        return request.redirect('/plans') 
```

Enter fullscreen mode Exit fullscreen mode

好极了。现在我们至少可以通过请求类，所以让我们回到控制器测试:

```
from app.http.controllers.PlanController import PlanController
from masonite.request import Request
from masonite.testsuite.TestSuite import generate_wsgi

class TestPlanController:

    def setup_method(self):
        self.request = Request(generate_wsgi())
        self.controller = PlanController()

    def test_controller_subscribes_user(self):
        assert self.controller.show(self.request) 
```

Enter fullscreen mode Exit fullscreen mode

完美！现在我们至少可以模拟请求类。我们真的不需要模拟请求类，因为我们可以使用真正的类来模拟 WSGI 请求。

请注意，我们从 Masonite 测试套件中导入了一个`generate_wsgi`函数到代码中。Masonite 有几个用于测试的助手类和函数，我们可以在另一篇文章中讨论。

# 问题 2

好了，这应该还是不行，因为我们并没有真正将用户加载到`request.user()`方法中。它应该总是返回`None`。

让我们有一个请求类，其中已经加载了一个用户。这个用户将需要一个`subscribe`方法，我们还将检查用户是否订阅，所以让我们只是模拟一下。

我们还将模拟一个新的请求类，这样我们就可以在将来向它添加任何我们需要的方法:

```
from app.http.controllers.PlanController import PlanController
from masonite.request import Request
from masonite.testsuite.TestSuite import generate_wsgi

class MockUser:
    def subscribe(self, plan, token):
        self.plan = plan

    def is_subscribed(self, plan):
        if self.plan == plan:
            return True

        return False

class MockRequest(Request):

    def user(self):
        return MockUser

class TestPlanController:

    def setup_method(self):
        self.request = MockRequest(generate_wsgi())
        self.controller = PlanController()

    def test_controller_subscribes_user(self):
        assert self.controller.show(self.request) 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。现在我们可以运行`request.user()`方法，它将返回这个`MockUser`类。

# 问题 3

我们现在没有正确的输入。我们也可以很容易地模仿它们:

```
from app.http.controllers.PlanController import PlanController
from masonite.request import Request
from masonite.testsuite.TestSuite import generate_wsgi

...

class TestPlanController:

    def setup_method(self):
        self.request = MockRequest(generate_wsgi())
        self.controller = PlanController()

    def test_controller_subscribes_user(self):
        # Sets input data
        self.request.request_variables = {'plan_id': 'premium', 'stripeToken': 'tok_amex'}

        assert self.controller.show(self.request) 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们已经拥有了正确通过测试所需的一切。现在让我们再做一个断言:

```
 ...
    ...
    def test_controller_subscribes_user(self):
        # Sets input data
        self.request.request_variables = {'plan_id': 'premium', 'stripeToken': 'tok_amex'}

        assert self.controller.show(self.request)
        assert self.request.user().is_subscribed() 
```

Enter fullscreen mode Exit fullscreen mode

我们已经成功验证了该控制器方法成功订阅了用户！