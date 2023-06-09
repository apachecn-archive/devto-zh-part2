# 像布置你的房子一样布置你的代码

> 原文：<https://dev.to/_bigblind/lay-out-your-code-like-youd-lay-out-your-house-hco>

想象你醒来，起床刷牙。你去“扫帚和刷子”房间拿你的牙刷。然后，你去“清洁用品”室拿牙膏。你继续去“容器”房间拿杯子，最后，你去“输入和输出”房间，那里有一个让水进入你房子的水龙头，和一个带排水管的水槽，液体可以通过它离开你的房子。你现在可以去刷牙了。

我不会详细描述你在这个非常奇怪的房子里度过的一天，但你可以想象从“刀具”室得到一把小刀，在你的面包上涂一些花生酱，你也可以在那里放你的剃刀和园艺剪。你的房子那样布置显然会非常不方便。然而，我们经常这样构建我们的代码库。

## 这个为什么这么不方便？

在上面的例子中，你不得不去 4 个不同的房间刷牙。这太荒谬了！你会期望刷牙所需的所有东西都能方便地放在一起。

因此，让我们来看看一个假设的博客应用程序的示例代码库。因为这是一个假设的代码库，所以我没有使用任何特定的 web/数据库/...框架在这里。
代码会用 Python 写，但是你不需要懂 Python 就能明白这篇帖子的重点。

应用程序的结构可能如下所示:

```
BogoBlog/
├── main.py
├── controllers/
│   ├── posts.py
│   ├── comments.py
│   └── users.py
├── errors/
│   ├── posts.py
│   ├── comments.py
│   └── users.py
├── storage/
│   ├── posts.py
│   ├── comments.py
│   └── users.py
├── templates/
│   ├── posts/
│   │   ├── post.html
│   │   └── ... more files ...
│   ├── comments/ ...more files inside ...
│   └── users/ ...more files inside ...
└── validators/
    ├── posts.py
    ├── comments.py
    └── users.py 
```

这看起来像一个 **MVC** 应用程序。我们来看看`controllers/posts.py`文件:

```
from cool_web_framework import BaseController, HttpForbiddenError, HttpNotFoundError,
from storage.posts import PostsStore
from storage.users import UsersStore
from errors.posts import PostNotFoundError
from errors.users import UserNotFoundError
from validators.posts import validate_post

class PostController(BaseController):
    def get(self, postId):
        """Handle GET requests to /posts/{postId}"""
            post = self.fetch_post(postId)

        if post.is_draft:
            self.ensure_current_user_can_edit(post)

        return self.render_template("templates/post.html", post)

    def post(self, postId):
        """Handle POST requests to /posts/{postId}"""
        post = self.fetch_post(postId)

        self.ensure_current_user_can_edit(post)

        data = self.request.body.json()
        err = validate_post(data)
        if err:
            raise HttpBadRequestError(err)

        post.update(data)
        PostsStore.save(post)
        return self.redirect("/")

    def fetch_post(self, postId):
        try: 
            return PostsStore.get_post_by_id(postId)
        except PostNotFoundError:
            raise HttpNotFoundError("No post with id " + postId)

    def ensure_current_user_can_edit(self):
        err = HttpForbiddenError("You don't have permission to view this post.")
        try:
            return UsersStore.get_current_user(self.request)
        except UserNotFoundError:
            raise err

        if not (user.is_admin or post.author == user):
            raise err 
```

这段代码可能看起来很合理，但是当事情发生变化时，就会出现问题。例如，上面的文件从 manny 其他模块导入函数。如果这些模块中的任何一个改变了它们的公共接口，这个文件就需要更新。但是由于文件不在变更附近，所以您需要搜索整个代码库，以查看在哪里调用了变更的函数，从而更新调用。举一个更具体的例子，假设您被要求向帖子添加一个“banner_image”字段，以使帖子在视觉上更有趣。这需要在以下方面做出改变:

*   `storage/posts.py`将字段添加到数据库中。
*   `controllers/posts.py`处理上传的图像。
*   `validators/posts.py`要验证图像，请确保它的文件类型、大小等受支持。
*   `templates/posts/post.html`显示图像。

虽然这对于这种规模的代码库来说还不算太糟糕，但是随着代码库的增长，跟踪哪里需要更改会变得更加困难。不得不如此频繁地跳转也使得代码库更难理解。我们到处编辑文件以添加一个特性的原因是，我们已经根据 te **MVC** 模式根据它们持有的代码类型对文件进行了分组，而不是根据它们提供的功能根据**对它们进行分组。**

## 我们来搞定这个吧！

[![](img/92998624e8802038ce1697908f6b3ec3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xoQTPnvn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://frederikcreemers.be/posts/code-layout/refactor.png)

让我们将所有与帖子相关的功能组合在一起，将所有与用户相关的功能组合在一起，等等...

`

```
BogoBlog/
├── main.py
├── posts/
│   ├── controllers.py
│   ├── storage.py
│   ├── validators.py
│   └── templates/
│       ├── post.html
│       └── ... more files ...
├── comments/
│   ├── controllers.py
│   ├── storage.py
│   └── validators.py
└── users/
    ├── controllers.py
    ├── storage.py
    └── validators.py 
```

所以现在，当我们需要改变与文章相关的东西时，你可能只需要编辑`/posts`中的文件，其他功能也是如此。当然，你可能仍然需要编辑多个目录中的代码，但至少现在你在考虑一个变化会影响应用程序的哪些**特性**。

## 衔接

当谈到什么属于一个软件项目时，通常会用到**内聚**这个词。众所周知，维基百科是互联网上所有真相的来源，所以让我们看看[维基百科对凝聚力](https://en.wikipedia.org/wiki/Cohesion_(computer_science))有什么说法。

> 在[计算机编程](https://en.wikipedia.org/wiki/Computer_programming)中，**内聚**是指一个[模块](https://en.wikipedia.org/wiki/Module_(programming))内部的元素属于一起的程度。从某种意义上来说，它是一个类的方法和数据与该类所服务的某种统一目的或概念之间关系强度的度量。在另一种意义上，它是类的方法和数据本身之间关系强度的度量。

好的，所以**内聚**仅仅意味着“程序的多少部分属于一起”。这个术语的定义并没有说应该如何衡量，但它经常被使用，而没有进一步的定义。人家只是说你要有“高凝聚力”。

在同一篇文章的后面，有一个不同类型凝聚力的列表，根据他们如何被测量来定义。

> *   **功能内聚**是模块的部分被分组，因为它们都有助于模块的一个明确定义的任务(例如，XML 字符串的词法分析)。
> 
> [...]
> 
> 包括拉里·康斯坦丁、[爱德华·尤顿](https://en.wikipedia.org/wiki/Edward_Yourdon)和[史蒂夫·麦康奈尔](https://en.wikipedia.org/wiki/Steve_McConnell)、[【3】](https://en.wikipedia.org/wiki/Cohesion_(computer_science)#cite_note-3)在内的许多人的研究表明...]且功能衔接优越。

功能内聚听起来很像我们刚刚做的，对吗？根据功能对事物进行分组。所以当谈到凝聚力时，请具体说明你指的是哪种类型的凝聚力。

我从这个引用中删除了很多内容，因为我不喜欢引用维基百科的一整节，然后重新解释它。我可能会花更多的时间来研究这个问题，并写另一篇关于内聚性的文章，因为我认为这篇维基百科文章过于强调面向对象编程，我很想看看内聚性在不同粒度级别和不同编程范例中的含义。

## 其他注意事项。

*   **不仅仅是 MVC** 。我在这里使用了一个 MVC 应用程序，因为这是一个很多人都熟悉的模式，但在其他情况下也会出现同样的问题，比如当你将一个 React+Redux 应用程序分成组件、动作、reducers，...等等。
*   **习俗很重要**。如果每个人都理解代码库的结构，你会更有效率。如果有一个社区、你的公司或你的团队坚持的代码基础结构，不要突然改变一切。如果你想改变惯例，就围绕它展开对话。改变约定可能还需要工具的改变，比如支撑项目结构的 CLI 工具。
*   **构建支持这个**的框架。如果你是一个框架的作者，试着去设计它，这样用户就可以用这种方式构建他们的代码库。Django 很好地做到了这一点，它将项目分割成“应用程序”,为项目保存不同的特性。(是的，app 这个名字有点令人困惑，因为你会认为整个项目就是一个 app，但你会习惯的。)
*   **不要窝的太深**。目录使查找东西变得更容易，但是它的缺点是你需要记住文件在哪里。大多数显示文件列表的地方都可以根据文件名按字母顺序显示。在一个排序列表中找到一个文件是很容易的，然而要记住文件和文件夹的深层嵌套结构却需要更多的心理循环。

## 感谢阅读！

请在 Twitter 上告诉我你的想法