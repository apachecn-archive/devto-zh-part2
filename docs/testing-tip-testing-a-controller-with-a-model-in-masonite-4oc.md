# 测试技巧:在 Masonite 中用模型测试控制器

> 原文：<https://dev.to/masonite/testing-tip-testing-a-controller-with-a-model-in-masonite-4oc>

# 简介

我们会让这个很短。我会做一些代码片段和解释。

几乎 80%以上的时间，我们会在我们的控制器中有一些模型，但通常很难模仿这些模型。例如，我们可能有一个带有某种逻辑的控制器，比如:

```
from app.User import User

class MikeController:

    def show(self):
        return User.find(1).name # returns Mike 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们进入我们的测试，我们有类似于:

```
from app.http.controllers.MikeController import MikeController

class TestController:

    def setup_method(self):
        self.controller = MikeController()

    def test_controller_show_returns_mike(self):
        assert self.controller.show() == 'Mike' 
```

Enter fullscreen mode Exit fullscreen mode

我们不能模仿用户类，因为它是硬编码到我们的控制器中的。它将直接从数据库中提取。如果我们不想在不接触数据库的情况下运行测试，那该怎么办？

# 解

我们可以简单地将该用户添加为类级别的属性:

```
from app.User import User

class MikeController:

    __user__ = User

    def show(self):
        return self.__user__.find(1).name # returns Mike 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以模拟这个模型:

```
from app.http.controllers.MikeController import MikeController

class MockUser:
    name = 'Mike'

    def find(self, index):
        return self

class TestController:

    def setup_method(self):
        self.controller = MikeController()
        self.__user__ = MockUser()

    def test_controller_show_returns_mike(self):
        assert self.controller.show() == 'Mike' 
```

Enter fullscreen mode Exit fullscreen mode

TADA！我们现在可以在`self.controller.__user__`属性中加入任何模型，当我们测试代码时，它会被嘲笑。这是测试控制器的一个很好的小技巧。我们的控制器可能看起来像:

```
from app.User import User
from app.Phone import Phone

class MikeController:

    __user__ = User
    __phone__ = Phone

    def show(self):
        return self.__user__.find(1).name # returns Mike 
    def call(self):
        return self.__phone__.where('user_id', self.__user__.find(1).id).first() 
```

Enter fullscreen mode Exit fullscreen mode

这真的是可测试的，也是一个很酷的小技巧。