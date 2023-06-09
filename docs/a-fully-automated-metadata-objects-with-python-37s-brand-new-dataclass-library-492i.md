# Python 3.7 全新数据类库的全自动元数据对象。

> 原文：<https://dev.to/furkan_kalkan1/a-fully-automated-metadata-objects-with-python-37s-brand-new-dataclass-library-492i>

[![metadata banner](img/fa34d2b0ea0f26a861bf66be6fc883ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3FeDjwf3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/05t7orypfsyow3vfm5lp.png)

Dataclass 是 Python 3.7 中一个全新的数据结构。最近，@btaskaya 写了一篇很棒的文章。如果你还没有读过，你可以在[这里](https://dev.to/btaskaya/dataclasses-in-python-4hli)阅读。

Dataclass 具有创建可重用、自我验证和自动化元数据对象的良好特性。在此之前，我习惯使用`dict`格式来创建元数据对象，但是总是复制和粘贴同一个对象是很无聊的，并且与反对干(不要重复自己)的规则相冲突。

事情是这样的:

```
Metadata = {}
Metadata["id"] = id
Metadata["url"] = url

if something:
    Metadata["some_field"] = some_data

Metadata["media"] = {}
Metadata["media"]["id"] = media_id 
... 
```

Enter fullscreen mode Exit fullscreen mode

我可以用`NamedTuple`或别的什么来代替`dict`，但是它们有一些限制，在项目的早期，我真的没有足够的时间来实现一些更好的东西。当我重构代码时，我意识到 dataclass 更符合我的需要。

在本文中，我将逐步向您展示如何用 dataclasses 实现完全自动化的元数据对象。

## 第 1 部分:实现不需要计算的元数据字段

这一步完全没有问题。这只是标准实现。

```
from dataclasses import *

@dataclass
class Metadata:
    title: str
    url: str
    created_at: str = None    # Fields may have default value 
```

Enter fullscreen mode Exit fullscreen mode

## 第二部分:添加一些需要计算的字段，让我们自动计算。

这些字段只有在计算后才会得到值。在我们的例子中，`post_id`应该等于随机数加上 url。

```
import random
from dataclasses import *
@dataclass
class Metadata:
    # Normal fields
    title: str
    url: str
    created_at: str = None
    # Calculated fields
    post_id: str = field(init=False)
    def __post_init__(self):
        random_number = random.randint(100000, 999999)
        self.post_id = f"{random_number}_{self.url}" 
```

Enter fullscreen mode Exit fullscreen mode

`__post_init__`函数将在初始化后计算我们的字段`post_id`。

姑且称之:

```
>>> Metadata(
...  title="Some Article",
...  url="https://example.com/article.html",
...  created_at="2018-09-23"
... )
Metadata(title='Some Article', url='https://example.com/article.html', created_at='2018-09-23', post_id='696953_https://example.com/article.html') 
```

Enter fullscreen mode Exit fullscreen mode

抓住你了。

## 第三部分:让我们的手更脏；仅添加`__post_init__`伪字段

我们可能想要建造自主的复杂结构。例如，如果一个字段被注释，dataclass 可以为我们构建整个子结构。在我们的例子中，我们使用额外的字段`author_names`和`author_ids`来构建`authors`字段作为`dict`的`list`。如果没有为文章提供作者信息，`authors`字段的值应该是`None`。

```
import random
from dataclasses import *

@dataclass
class Metadata:
    # Normal fields
    title: str
    url: str
    created_at: str = None
    authors: list = None
    # Calculated fields
    post_id: str = field(init=False)
    # Non-nullable Pseudo fields
    author_names: InitVar[list]
    author_ids: InitVar[list]

    def __post_init__(self, author_names, author_ids): # You have to pass pseudo fields as the parameter.
        random_number = random.randint(100000, 999999)
        self.post_id = f"{random_number}_{self.url}"
        self.authors = []
        for i in range(0, len(author_names)):
            self.authors.append({"id": author_ids[i], "name": author_names[i]}) 
```

Enter fullscreen mode Exit fullscreen mode

姑且称之:

```
>>> Metadata(
...  title="Some Article",
...  url="https://example.com/article.html",
...  created_at="2018-09-23"
... )

TypeError: non-default argument 'author' follows default argument. 
```

Enter fullscreen mode Exit fullscreen mode

没用:(

> **重要提示:**您必须对默认和非默认字段进行分组。

再试一次:

```
import random, json
from dataclasses import *

@dataclass
class Metadata:
    # Non-nullable Pseudo fields
    author_names: InitVar[list]
    author_ids: InitVar[list]
    # Normal fields
    title: str
    url: str
    created_at: str = None
    authors: list = None
    # Calculated fields
    post_id: str = field(init=False)

    def __post_init__(self, author_names, author_ids): # You have to pass pseudo fields as the parameter.
        random_number = random.randint(100000, 999999)
        self.post_id = f"{random_number}_{self.url}"
        self.authors = []
        for i in range(0, len(author_names)):
            self.authors.append({"id": author_ids[i], "name": author_names[i]})

    def to_json(self):
        json.dumps(asdict(self)) 
```

Enter fullscreen mode Exit fullscreen mode

我们再叫一遍:

```
>>> Metadata(
... title="Some Article",
... url="https://example.com/article.html",
... created_at="2018-09-23",
... author_names=["Furkan Kalkan", "John Doe"],
... author_ids=["1", "2"]
... )
Metadata(title='Some Article', url='https://example.com/article.html', created_at='2018-09-23', authors=[{'id': '1', 'name': 'Furkan Kalkan'}, {'id': '2', 'name': 'John Doe'}], post_id='692728_https://example.com/article.html') 
```

Enter fullscreen mode Exit fullscreen mode

耶！

但是等等...`author_names`和`author_ids`都去哪了？

> **注意:**表示`InitVar`实例的伪字段，仅在`__post_init__()`中用作参数，它们不是对象的一部分。
> 
> ```
> >> Metadata.author_names
> Traceback (most recent call last):
>   File "<stdin>", line 1, in <module>
> AttributeError: type object 'Metadata' has no attribute 'author_names' 
> ```

## 第四部分:我们不需要定义`author_names`。

我们也可以将伪字段设置为可选的。

```
import random, json
from dataclasses import *

@dataclass
class Metadata:
    # Non-nullable Pseudo fields
    author_ids: InitVar[list]
    # Normal fields
    title: str
    url: str
    created_at: str = None
    authors: list = None
    # Nullable Pseudo fields
    author_names: InitVar[list] = field(default=None)
    # Calculated fields
    post_id: str = field(init=False)

    def __post_init__(self, author_names, author_ids): # You have to pass pseudo fields as the parameter.
        random_number = random.randint(100000, 999999)
        self.post_id = f"{random_number}_{self.url}"
        if author_names:
            self.authors = []
            for i in range(0, len(author_names)):
                self.authors.append({"id": author_ids[i], "name": author_names[i]})

    def to_json(self):
        json.dumps(asdict(self)) 
```

Enter fullscreen mode Exit fullscreen mode

调用它:

```
>>> Metadata(
... title="Some Article",
... url="https://example.com/article.html",
... created_at="2018-09-23",
... author_ids=["1", "2"]
... )
Metadata(title='Some Article', url='https://example.com/article.html', created_at='2018-09-23',authors=None,post_id='692728_https://example.com/article.html') 
```

Enter fullscreen mode Exit fullscreen mode

## 第五部分:我们需要 JSON。

Python 对象是好的，但是我们需要将它作为 JSON 转储，以将其发布到 web 服务、MQs 等。数据类库有内置函数`asdict()`，可以把我们的对象转储到`dict`。

让我们为我们的对象编写包装器。

```
import random, json
from dataclasses import *

@dataclass
class Metadata:
    # Non-nullable Pseudo fields
    author_names: InitVar[list]
    author_ids: InitVar[list]
    # Normal fields
    title: str
    url: str
    created_at: str = None
    authors: list = None
    # Calculated fields
    post_id: str = field(init=False)

    def __post_init__(self, author_names, author_ids): # You have to pass pseudo fields as the parameter.
        random_number = random.randint(100000, 999999)
        self.post_id = f"{random_number}_{self.url}"
        if author_names:
            self.authors = []
            for i in range(0, len(author_names)):
                self.authors.append({"id": author_ids[i], "name": author_names[i]})

    def to_json(self):
        return json.dumps(asdict(self)) 
```

Enter fullscreen mode Exit fullscreen mode

检查一下:

```
>>> m = Metadata(
... title="Some Article",
... url="https://example.com/article.html",
... created_at="2018-09-23",
... author_names=["Furkan Kalkan", "John Doe"],
... author_ids=["1", "2"]
... )
>>> m.to_json()
{"title": "Some Article", "url": "https://example.com/article.html", "created_at": "2018-09-23", "authors": [{"id": "1", "name": "Furkan Kalkan"}, {"id": "2", "name": "John Doe"}], "post_id": "466969_https://example.com/article.html"} 
```

Enter fullscreen mode Exit fullscreen mode

## 第 6 部分:移除 json 中不必要的字段。

我们希望从 json 中移除除 url 字段之外的`None`值字段。稍微改变一下就有可能:

```
 def to_json(self):    
    metadata = asdict(self)
    for key in list(metadata):
        if key != "url" and metadata[key] == None:
                del metadata[key]
    return json.dumps(metadata) 
```

Enter fullscreen mode Exit fullscreen mode