# Rails 和单一责任原则

> 原文：<https://dev.to/drews256/rails-and-the-single-responsibility-principle-9kd>

# 单一责任原则

单一责任原则指出:

> “每个模块或类都应该对软件提供的功能的一个部分负责”

这是相当令人困惑的。每个模块或类负责软件的*功能*的单个*部分*。让我们稍微分解一下。软件的功能可以是显示

罗伯特·C·马丁这样说，

> "一个类应该只有一个改变的理由."

很简洁。但是很复杂。一个类应该只有一个*原因*来改变。到底什么是原因，什么是对一个类的改变。有许多方法可以改变一个类。我们现在不会详细讨论这些问题。

Rails 如何处理单一责任原则？作为开发人员，我们如何维护在熟悉的 Rails API 上编写的代码的单一责任原则？

Rails 模型很难扩展。编写好的 RoR 的原则之一是保持控制器“苗条”，人们经常将多余的代码移回到模型中。模型往往会变得复杂，而且变得很快。当您扩展它们来做不同的事情时，我们必须测试每一个方法，因为副作用会越积越多，我们最终会得到不可管理的模型。这可能比不可控制的控制器更糟糕。

如果你已经在 Rails 社区呆了几乎一段时间，我相信你对经典的“15 分钟 Rails 应用博客”很熟悉。它允许你发表一篇新的博文，并对这篇博文发表评论。这是简单而容易的，传统上，我们在这个应用程序中有三个模型。一个`Post`，一个`Comment`，通常还有一个`User`或者一个`Author`。

这种分离似乎是合理的，因为这里有两件主要的事情需要我们去做，而有些事情需要 T2 去做。

`Post`是博客帖子，它包括:

1.  一个作家
2.  关于创建时间的一些信息
3.  关于博客文章的文本

`Comment`非常简单，它包括:

1.  对您正在评论的帖子的引用
2.  关于发表评论的人的一些信息
3.  注释的文本

`Author/User`包括:

1.  辨识信息
2.  神奇的 rails 协会让您可以撰写评论和帖子

这里的问题在于你期望这些模型的“责任”是什么。

Rails 模型有责任知道它存储在哪里吗？什么数据与*本身*相关联。你有什么遗漏的参考？当一条评论或一篇文章的每一个部分都丢失时会发生什么？这些模型如何与其他模型互动。

或者你可以从更大的角度来看，这里的“单一”责任是模型处理与数据相关的所有事情，数据是模型的*。*

 *我们清楚地知道，这是*而不是*的责任来处理观看本身，或者处理在正确的终点上服务自己。

马丁先生会说，一个帖子或一条评论的改变应该只有一个原因。这种变化意味着什么？底层数据的变化？

Rails 在这方面做得很好。一个简单的例子是，我们更新、创建、读取或删除了这个模型的一个实例:

```
new_post = Post.new(user_id: 1, body: some_giant_wall_text)
# new_post is now a brand *new* instance of the Post class. 
new_post.save
# new_post is now persisted to the database
new_post.update(body: some_other_giant_wall_of_text)
# new_post is now persisted to the database again! 
```

Enter fullscreen mode Exit fullscreen mode

刚刚发生了什么？类的同一个实例被持久化了两次。我们是不是在三行 Rails 代码中破坏了 SRP？！

事实上，我们改变了类的实例。快速的健全性检查显示:

`new_post.body == some_other_giant_wall_of_text`

该类的实例已更改。这一变化背后的*原因*是我们*将数据持久化*到数据库。

rails 为什么要这么做？感觉好像模特在做两件事。保存和更新。但让我重新表述一下它在做什么。Rails 模型处理数据库的 CRUD 操作。仅此而已。我们创建了模型，但是传入了一些属性的散列，它被简洁地保存到数据库中，然后我们更新了这些属性，它也简洁地将这些属性保存到数据库中。

Rails 模型的职责是处理数据库中的对象。该模型允许开发人员快速处理针对数据库的 CRUD 操作。

我认为可以肯定地说，改变的原因是 Rails 模型将某些东西持久化到数据库中。模型的一个属性发生了变化，因此类的实例可以发生变化，但仍然符合 SRP。

关键是，在许多情况下，Rails 模型与 SRP 是一致的，你不必担心你的 Rails 应用程序不稳定。

我们的 Rails 模型继承自 ActiveRecord::Base 是有原因的。ActiveRecord 处理数据库中*对象*的读取、创建、更新和删除。没别的了。

酪

Rails 模型很难。它们变得复杂了。我们都知道这一点。我们都曾在一个 Rail 应用程序中破坏了一个模型，并带来了意想不到的后果。

通常情况下，业务规则希望我们的模型做其他事情，与其他对象进行交互。当我们想让我们的`Post`突然推断出`Post`的作者是谁时。或者我们想只允许某些`User`看到某些`Comment`并与之互动，或者某个`Post`突然需要成为公告板的一部分。或者你需要给一个`Post`加`Tag` s。

Rails 确实允许您安全地做到这一点。向数据库中添加一个新列并在数据库中安全地创建关联是非常简单的。

这在我们的`Comment` s 和`Post` s 的关系上可以看出来。

```
post = Post.find_by(author: 'me') # an instance of a post from our Database
post.comments # responds with a collection of Comments that match the Post ID
post.user # responds with an instance of the author
post.tags # responds with and error since we have no `Tag` table 
```

Enter fullscreen mode Exit fullscreen mode

同样，这大部分是让我们将信息保存到数据库中的扩展。我们仍然没有打破 SRP。此外，我们能够以多种方式扩展我们的模型，并且不会破坏 SRP。

我们已经做了很多了。非常远。

现在，当你带着这样的要求回来时会发生什么，每个作者都应该有一个他们所发表的帖子的摘要。你说很容易。

```
author = User.find_by(name: my_name) # find an author
author.posts # find all the posts 
```

Enter fullscreen mode Exit fullscreen mode

🎉我为那个作者收集了一些帖子。很简单。

现在我不想要整篇文章！这是一份内容丰富的总结。我只想要每个帖子的前 200 个字符。这是总结！

我们最终在`Post`上找到了一个方法，因为这是处理数据操作的最方便的方法，而数据操作必须碰巧显示某些帖子。

我们以 Post 模型上的一个方法结束，比如:

```
class Post < ActiveRecord::Base
...

  def body_summary
    body[0..200]
  end

...
end 
```

Enter fullscreen mode Exit fullscreen mode

所以为了得到一个`Post`的摘要，我们可以调用:

```
author = User.find_by(name: 'my_name') # find an author
author.posts(&:body_summary) # grab all the post summaries 
```

Enter fullscreen mode Exit fullscreen mode

但是等等！ActiveRecord 和我们的 Rails 模型的目标是什么！？从数据库中读取、更新、创建和删除信息！而不是转换我们从数据库中检索的数据。

是的，现在我们已经打破了 Rails 模型中的 SRP。这就像添加一个方法一样简单。我们已经*现在*修改了对象的实例，除了从数据库中读取、更新、创建和删除值。数据库永远不会看到也不会关心`body_summary`。

我们正在改变一个只为一个原因而存在的对象，即数据库中的 CRUD，这样我们就可以以一种非常不同的方式使用它，以一种特定的方式在视图中显示该信息。这是 Rails 中非常常见的模式。这很糟糕。:远离编写蹩脚的方法:

ActiveRecord 模型用于创建、读取、更新和删除数据库中的信息。

这个方法本身并不是世界上最糟糕的方法。我最喜欢的 Sandi Metz 的引用似乎支持这个代码的存在。事实上。在某些情况下，我会鼓励这种努力。

> “代码并不完美，但在某些方面它达到了更高的标准:足够好了。”

在这个非常简单的例子中，我会停在这里，并不真正担心它。它不会伤害任何东西。当然，你可以进入 N+1 查询，变得非常繁重，并且花费*长的*时间来生成`body_summary`，但是这很可能是不现实的。

除了重点。该代码打破了我们到目前为止定义的单一责任规则。有哪些类似 Rails 的方法可以清理这些代码？

让我们看看其他解决方案，这样我们的`Post`就不会违反单一责任规则。从简单的实现到更复杂的解决方案。

这里一个简单且稍微好一点的选择是在`Post`表中添加一个`body_summary`列。这将允许我们在每次更新`Post`时使用回调来添加一个`body_summary`。

```
class Post < ActiveRecord::Base
  ...
  after_save :build_body_summary

  def build_body_summary
    update({body_summary: body[0..100]})
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

这很简单。就我个人而言，我喜欢这里的简单性，它将所有信息保存在一个表中，并在`Post`上使用，现在当我们需要主体摘要时，我们可以像访问`Post`对象上的任何其他属性一样访问它们。但是我们也假设*很多关于身体的事情。当`build_body_summary`失败时会发生什么？我们需要两个验证周期来保存`Post`一次。*

考虑到与此解决方案相关的单一责任原则，`Post`确实会保存自身及其所有数据。但是一个`Post`需要负责自救吗！？`Post`类每次都有足够的信息来自救吗？它必须回忆起*本身*，然后通过把自己转变成一个接近但不完全是自己的东西来改变自己。它是否随着创建、更新、删除或阅读的明确目的而改变？不，它改为生成一个`body_summary`，然后*将*保存到数据库中。

另一种选择是定义一个由数据库表支持的 BodySummary 类。

```
class BodySummary < ActiveRecord::Base
  belongs_to :post
  belongs_to :author
end 
```

Enter fullscreen mode Exit fullscreen mode

并用回调来扩展`Post`类。这是控制 SRP 的唯一途径。我提倡 after_save 回调，因为它消除了覆盖`initialize`方法或覆盖 Post 类上其他方法的需要。

```
class Post < ActiveRecord::Base
  ...
  has_one :body_summary

  after_save :persist_body_summary

  def perist_body_summary
    BodySummary.new(post_id: id, author_id: author.id, summary: body[0..100])
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们使用标准的 rails 表单为每个帖子构建一个摘要集合。但是现在在`Post`类中间有一个相当大的依赖`BodySummary` smack dab。

这几乎比在`Post`类上有一个`body_summary`方法更糟糕。回到单一责任，人们实际上可能会争辩说，在数据库中保存除了*本身*之外的任何东西都超出了`Post`类的责任，因此我们在这里也违反了 SRP。如果`Post`类的目的是为帖子创建、读取、更新和删除数据，我不完全同意`BodySummary`本身构成了 SRP 中的一个显式中断。只是*碰巧*与`Post`在不同的表中。你对此有什么想法？

最近很多人都在谈论服务对象。事实上，最近一些狂热的 Rails 爱好者对服务对象的使用产生了一些混乱。

关于服务对象及其在 Rails 中的整体必要性的讨论是另一篇文章的主题，各方都有许多人支持这两种选择。

我确实喜欢一个好的服务对象，我们可以轻松地编写一个 PORO，它接受一组`Post`并返回主体摘要。

```
class PostBodySummary
  attr_reader :posts

  def initialize(posts:)
    @posts = posts
  end

  def perform
    summaries = []
    posts.each do |post|
      summaries << post.body[0..200]
    end
    summaries
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

调用`PostBodySummary.new(posts: user.posts).perform`非常简单，而且很有效。这里的一个退步是，我们对这个模型到底是怎么回事的理解非常有限。如果我们不打开`PostBodySummary`,我们在做什么就很不明显了。减轻这种痛苦的一种方法是将`perform`方法重命名为类似于`generate_summaries`的名称。但是 ymmv 在这个简单的例子中。

这很容易重用和扩展。我们可以定义一个不同的方法，它将返回更短的摘要。很容易，我们可以写:

```
class PostBodySummary
  ...
  def shorter_summaries
    posts.each do |post|
      post.body[0..100]
    end
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

这更干净，并遵循我们上面的模式，显式返回您调用的。

我们应该在这里检查一下单一责任原则。`PostBodySummary`类是做什么的？它接受一个 ActiveRecord 集合并返回`Post`的主体摘要。只有当我们需要不同的 ActiveRecord 帖子集合时，它才会改变。这似乎是合理的。一个`Author`的`Post`集合不同于另一个`Author`的`Posts`集合，服务对象的每个实例都会反映这种变化。这个解决方案可能是我认为最遵循单一责任原则的解决方案。`PostBodySummary`在`Posts`的集合变化时变化。改变的一个原因是帖子的集合改变了，这个对象反映了这一点。

对单一责任原则的整体思考是艰难的。尤其是当我们准备扩展 Rails 为我们公开的 API 时。有些人会说，我过多地谈论 Rails 模型而忽略了这一点，单一责任原则与其说是关于对象的实例和我们使用它们的方式，不如说是关于类中的*实际*代码。我跑题了。

当 Rails 模型变得复杂时，很难扩展，部分原因是即使在简单的情况下，它们也不断违反单一责任规则。通常，Rails 模型的目标是处理数据和数据库。我认为这已经足够复杂了，这就是它需要做的全部事情。

有许多选项可以让我们将 Rails 模型的复杂性封装在实际模型之外。介绍了一些我最喜欢的方法，包括利用回调来设置自身的属性，使用服务对象来完全删除代码，添加新的 ActiveRecord 对象本身，以及介于两者之间的一些选项。

让我知道你对单一责任原则的看法！

-该男子本人对 SRP 的更多评论:

鲍勃叔叔澄清了他的真实意思，他说:

> "一个类应该只有一个改变的理由."

[https://8 thlight . com/blog/uncle-bob/2014/05/08/singlerepointibilityprinciple . html](https://8thlight.com/blog/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html)

-关于服务对象和我前面提到的文章的更多信息:

Aaron Lasseigne 关于使用服务对象的文章回应 Avdi 的帖子:

[https://aaronlasseigne . com/2017/11/08/why-arent-we-using-more-service-objects-already/](https://aaronlasseigne.com/2017/11/08/why-arent-we-using-more-service-objects-already/)

Avdi Grimm 关于服务对象和程序的文章:

[https://avdi . codes/service-objects/](https://avdi.codes/service-objects/)

需要注意的一点是，Aaron 是一个非常流行的服务对象 gem 的作者，这个服务对象 gem 叫做 ActiveInteraction。所以这可能会影响他的总体看法。*