# 实用的 Python - Duck-typing 来拯救！

> 原文：<https://dev.to/alvarocavalcanti/practical-python---duck-typing-to-the-rescue-46e6>

# 鸭子打字？

如果它像鸭子一样走路，像鸭子一样嘎嘎叫，像鸭子一样游泳:它是一只鸭子。

以上是对[鸭式类型化](https://en.wikipedia.org/wiki/Duck_typing)的简单定义，这是非静态类型化语言的一个很大的副作用，比如 Python。

# 好吧，我怎么用它？

让我们考虑下面的对象，一个 [Django 模型](https://docs.djangoproject.com/en/2.1/topics/db/models/)(在这个例子中被简化了):

```
class User(models.Model):
    username = models.CharField()
    full_name = models.CharField()
    address_street = models.CharField()
    address_zip = models.CharField()
    subscription = models.ForeignKey()
    pets = ArrayField(Pet())  # Consider a one-to-many relationship 
```

现在，您的任务是为用户创建一个变更跟踪器，但是那个变更应该只关心`username`和`pets`集合(因为原因)。然后，您创建一个新模型，如下所示:

```
class UserChange(models.Model):
    before_username = models.CharField()
    before_pets = ArrayField(Pet())
    after_username = models.CharField()
    after_pets = ArrayField(Pet()) 
```

一切都很好，您已经实现了几乎每一个场景，直到您从一个`User`中移除一个`Pet`。问题是，由于集合是从数据库中加载的，并且您应该只在完成后跟踪更改，所以加载以前的状态变得很棘手。好吧，你可以在初始集合被修改之前获取它，这很好，但是在哪里存储它呢？你也可以做一个 [`copy.deepcopy`](https://docs.python.org/2/library/copy.html) ，但是这听起来像是用大炮来杀死一只蚂蚁。话又说回来，你只需要存储`username`和`pets`集合。

# 鸭子打字拯救世界！

您可以使用轻量级的优雅解决方案，如下所示:

```
class Duck:
    def __init__(self, **kwds):
        self.__dict__.update(kwds) 
```

上面的类接受您传入的任何关键字参数，并将它们作为属性添加到类中，使用起来非常简单:

```
user_duck_before = Duck(username="foo", pets=["dog", "cat"])
user_duck_after = Duck(username="bar", pets=["cat"]) 
```

就是这样。现在，您可以在删除流中使用这些“鸭子”,而不会有太多的内存开销。

这种方法类似于使用 [`unites.mock`](https://docs.python.org/3/library/unittest.mock.html) ，但是用生产代码代替测试代码。

公平地说，尽管我脑子里已经有了这个想法，但我并没有想出代码，我是在[这篇 SO 帖子](https://stackoverflow.com/questions/2827623/how-can-i-create-an-object-and-add-attributes-to-it)上找到的，这篇帖子把我指向了由 [Alex Martelli](https://code.activestate.com/recipes/users/97991/) 编写的[代码配方](https://code.activestate.com/recipes/52308-the-simple-but-handy-collector-of-a-bunch-of-named/?in=user-97991)。谢谢，亚历克斯！

## 编辑

我意识到有些地方不清楚，但我不想修改它，因此这一节。真正激励我寻找鸭子式方法的是一对多收集。在我的真实世界场景中，这个集合是一个 [`RelatedManager`](https://docs.djangoproject.com/pt-br/2.1/ref/models/relations/) ，它阻止我在项目被移除后加载之前的状态，因此我需要以一种既不会破坏我的契约也不会为这个新的数据结构实现几乎相同的方法的方式来存储该信息。