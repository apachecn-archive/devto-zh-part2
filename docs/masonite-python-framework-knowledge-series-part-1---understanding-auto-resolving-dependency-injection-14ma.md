# Masonite Python 框架知识系列:第 1 部分——理解自动解析依赖注入

> 原文：<https://dev.to/masonite/masonite-python-framework-knowledge-series-part-1---understanding-auto-resolving-dependency-injection-14ma>

[![](img/2327baeec801531a88719bd1ad6d08d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZRKPnaDu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/78uauinv9ddawwfxevos.png)

# 简介

Masonite Python 框架是一种比目前市场上任何东西都更现代的 Python 框架方法。这是一个大胆的说法，但是一旦你开始使用 Masonite，你会惊讶地发现它是多么强大，只需使用几个简单的设计决策，其他 Python 框架没有的，如依赖注入 IOC 容器、中介器(或管理器)模式，该模式解耦了许多框架以保持其流动性和可扩展性，以及许多其他设计决策。

在学习 Masonite 时，您还会学到许多在软件开发领域占主导地位的模式。

我将写几篇这样的文章来真正给出 Masonite 如何工作的背景知识。你越了解你的工具，你就能越好地最大限度地利用它们。

在本系列的第一部分中，我将深入探讨 Masonite 如何处理它所谓的“自动解析依赖注入”

如果你喜欢这篇文章，喜欢用 Masonite 编写应用程序，可以考虑加入 Slack 频道:[http://slack.masoniteproject.com](http://slack.masoniteproject.com)

## 什么事？

所以依赖注入是一个 1 美元定义的 10 美元单词。简单来说就是我们把一个东西传入一个它需要的对象。

举个简单的例子:

```
from app import Model

def get_user_id(self, model):
    return model.find(1).id

user = get_user_id(Model) 
```

Enter fullscreen mode Exit fullscreen mode

在这个最基本的例子中，模型是这个函数的一个依赖项，我们刚刚执行了依赖注入。我们将类“注入”(或传递)到这个函数对象中。

## 更进一步

让我们更进一步，为了演示的目的，我们在函数上面添加了一个字典(我们称之为容器)。这本字典将包含我们的应用程序所需的所有类。

注意:这将是一个实现它的坏方法，但现在只是跟着做:)这只是理解它如何工作的基础。

```
from static_assets import AmazonUpload

container = {'upload': AmazonUpload}

def upload_image(self, upload):
    return upload.store('some-file.png') 
```

Enter fullscreen mode Exit fullscreen mode

所以请注意，这里我们有一个名为`upload`的参数和一个名为`upload`的字典键。这是“自动解析”依赖注入的最基本的基础。

这里的想法是，我们可以使用这个容器来获取我们需要的所有依赖项，并且我们不必担心需求的变化。容器在我们的具体代码实现和容器的流畅性之间扮演着某种中介的角色。

如果我们需要改变功能，我们只需交换容器中的类。

## 更进一步

所以你可能会问自己..好吗？那么，为什么我的对象和这些类之间需要这个中介呢？为什么我不导入我需要的驱动程序类并使用它呢？”

Masonite 容器背后的思想是将您的应用程序(通常是具体的实现)与您的功能“松散耦合”。

使用上面的`upload_image`功能，如果老板走到你面前说“嘿！相反，我们需要开始将所有内容上传到文件系统，亚马逊对我们公司来说太贵了”..那你会怎么做？您可能会浏览您的代码，看到类似这样的内容:

```
from static_assets import AmazonUpload

def upload_image(self):
    return AmazonUpload.store('some-file.png')

def rename_image(self):
    return AmazonUpload.rename('some-file.png', 'another-file.png')

def delete_image(self):
    return AmazonUpload.delete('some-file.png')

def move_image(self):
    return AmazonUpload.move('some-file.png', 'another-location.png')

def copy_image(self):
    return AmazonUpload.copy('some-file.png', 'another-file.png') 
```

Enter fullscreen mode Exit fullscreen mode

现在我在 5 个地方编写了 AmazonUpload 依赖，现在有了 5 个具体的实现，现在需要进行重大的重构。

让我们再看一看，但是让我们偷偷地放回我们的容器，这样参数名再次匹配字典键:

```
from static_assets import AmazonUpload

container = {'upload': AmazonUpload}

def upload_image(self, upload):
    return upload.store('some-file.png')

def rename_image(self, upload):
    return upload.rename('some-file.png', 'another-file.png')

def delete_image(self, upload):
    return upload.delete('some-file.png')

def move_image(self, upload):
    return upload.move('some-file.png', 'another-location.png')

def copy_image(self, upload):
    return upload.copy('some-file.png', 'another-file.png') 
```

Enter fullscreen mode Exit fullscreen mode

## 自动部分

好了，现在你理解了“这个参数和字典键同名”的概念，我们可以再进一步。

替换 Masonite 处理依赖注入的实际功能并使用伪代码，自动部分可能看起来像这样:

注意:这是伪代码，而不是工作的 Python 代码。

```
class Container

    providers = {'upload', AmazonUpload}

    def resolve(self, object_to_resolve):
        build_parameter_list = []

        # Get all of the objects parameters
        paramaters = inspect(object_to_resolve).parameters
        # returns ['upload'] 
        for paramater in paramaters:
            if paramater in self.providers:
                build_parameter_list.append(parameter)

        return object_to_resolve(*build_parameter_list) 
```

Enter fullscreen mode Exit fullscreen mode

真的是这样。用通俗的语言来解释，我们只是使用 Python inspection 来获得所有参数的列表。例如，上面的`upload_image`函数会给出`'upload'`，因为这是参数名。

## 自动解析依赖注入

好吧！所以我们做了好事。让我们开始自动解析我们的依赖性吧！就拿上面的`upload_image`例子来说:

```
from container import Container

def upload_image(self, upload):
    return upload.store('some-file.png')

uploaded_image = Container().resolve(upload_image)
# Looks into the container for the 'upload' key and injects it into the class. 
```

Enter fullscreen mode Exit fullscreen mode

我们已经完全删除了 upload_image 的具体实现。它现在完全是“服务不可知的”不管它是一个`AmazonUpload`类还是一个`AzureUpload`类，依赖关系只在一个地方。我们现在可以随意交换整个应用程序的功能。

## 梅森奈特

容器如此强大的原因是在我们的特性和应用程序之间有一个完整的中介。除此之外，所有的特性都是不可知的，所以这就创建了一个非常可插拔的系统。

Masonite 使用更高级的方法来查找要解析的对象。

在 Masonite 中，我们这样使用这个功能:

```
class WelcomeController:

    def show(self, Request):
        Request.user().id 
```

Enter fullscreen mode Exit fullscreen mode

“请求”是容器中的一个键。这有点太基础了，让我们再深入一步，展示 Masonite 如何处理函数注释。

## Python 函数注释

Python 注释总是有点古怪。它基本上是一个参数的内联注释。似乎很奇怪。当他们把这个添加到 Python 中时，我不确定它是否应该为 Python 的核心服务。事实上，PEP 中是这样写的:

> 注释具有意义的唯一方式是由第三方库解释它们。这些注释消费者可以对函数的注释做任何他们想做的事情。

所以 Masonite 所做的是，除了遍历参数列表和解析依赖关系之外，我们还遍历注释并解析它们。而不是像这样正常标注:

```
def upload_image(upload: "pass in the upload class"):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

我们可以更进一步，通过整个课程:

```
from static_files import AmazonUpload

def upload_image(upload: AmazonUpload):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

我们本质上是用一个类来注释参数，而不是用一个字符串。我们现在可以使用注释类型(一个特定的类)在容器中查找。这更像是“通过字典值获取”，而不是前面的例子中的“通过字典键获取”

在 Masonite 中，应用程序有几个部分是自动解析的，如控制器、驱动程序和中间件。Masonite 中的自动解析将如下所示:

```
from masonite.view import View
from masonite.request import Request

class WelcomeController:
    ''' Controller For Welcoming The User '''

    def show(self, view: View, request: Request):
        ''' Show Welcome Template '''
        return view.render('welcome', {'request': request}) 
```

Enter fullscreen mode Exit fullscreen mode

## 子类(高级)

如果你已经理解了上面的一切，那么我们可以更进一步

除了获取该类，我们还可以获取该类的一个更强大的子类。容器是保持应用程序松散耦合的一种方式，但是如果我们使用注释，我们就不能随意从容器中换出类。我们现在与“具体的”实现“紧密地”联系在一起。换句话说，我们只能使用 1 个类。

以此为例:

```
from masonite.drivers import UploadS3Driver

class UploadController:
    ''' Controller For Uploading Images '''

    def show(self, upload: UploadS3Driver):
        ''' Show Welcome Template '''
        return upload.store('some-file.png') 
```

Enter fullscreen mode Exit fullscreen mode

我们可以进一步添加一个父类。`UploadS3Driver`在后端看起来是这样的:

```
...
from masonite.contracts import UploadContract
...

class UploadS3Driver(BaseDriver, UploadContract):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以通过使用`UploadContract`来获得相同的类:

*最好知道`UploadContract`不在容器*中

```
from masonite.contracts import UploadContract

class UploadController:
    ''' Controller For Uploading Images '''

    def show(self, upload: UploadContract):
        ''' Show Welcome Template '''

        return upload.store('some-file.png')
        # returns an upload driver 
```

Enter fullscreen mode Exit fullscreen mode

## 怎么回事

这使我得到了`UploadS3Driver`类，因为它是我们正在寻找的子类。Masonite 在后端处理这个逻辑。

在 Masonite 应该解决的问题上有一点等级之分。

它的基本原理是寻找类本身，但是如果它找不到它(因为契约不在容器中),那么它将解析你正在寻找的子类。如果容器中没有匹配，那么它将抛出一个异常。

## 测试

这种结构最棒的地方是它非常容易测试。现在我们可以导入控制器并模拟参数。

这里有一个关于我们如何模仿这个控制器的例子:

```
from app.http.controllers.WelcomeController import WelcomeController
from masonite.request import Request
from masonite.testsuite.TestSuite import generate_wsgi

class MockView:

    def render(self, template, dictionary):
        pass

def test_welcome_controller():
    assert WelcomeController().show(MockView, Request(generate_wsgi())) 
```

Enter fullscreen mode Exit fullscreen mode

如果你保持你的控制器简短，并在容器中保持你的许多依赖，那么你可以很容易地创建快速测试和模仿对象。

* * *

我希望你喜欢这篇文章！如果你感兴趣的话，我会推出更多这样的作品。你可以在 [Github](https://github.com/MasoniteFramework/masonite) 上开始，或者加入 [Slack](http://slack.masoniteproject.com) 频道！