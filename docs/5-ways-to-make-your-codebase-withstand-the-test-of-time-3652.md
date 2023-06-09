# 让你的代码库经受住时间考验的 5 种方法

> 原文：<https://dev.to/larribas/5-ways-to-make-your-codebase-withstand-the-test-of-time-3652>

*这是一系列文章中的第一篇，我和@hecrj 分享了我们在过去 3 年里致力于一个快速变化的大型代码库后所学到的东西，并且对结果非常满意！*

* * *

如果你是一名 web 开发人员，你可能习惯于每隔一周就有新的框架、库和技术问世。

> 我们正在永无止境地寻找更好的工具和模式，但这是否意味着我们的代码注定会变得陈旧和有皱纹？

[![Your decisions will always impact many other people](img/853b96cb21b8f9b512f44a06c202e644.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jRVY468z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AVd1Y16P-mNBDiCtduma8pg.jpeg)

你如何锚定你的项目，使其抵御趋势的*风？这里有 5 个对我们很有效的建议。*

## 1。根据领域概念，而不是技术概念来拆分代码

当你开始一个新项目时，你可能会遇到的第一个问题是你应该如何组织它。这里有两个流行的思想流派:或者我们按照技术概念，或者按照领域概念来划分我们的文件。

```
 # Split by tech concepts        # Split by domain concepts

    |- src                          |- auth
    |  |- controllers               |  |- controllers
    |  |  |- auth                   |  |- models
    |  |  |- profile                |  |- views
    |  |  |- article                |  |- tests
    |  |- models                    |- profile
    |  |- views                     |- article
    |- test                         (...)
    |  |- controllers
    |  |  |- auth
    (...) 
```

如果你已经阅读了标题，你可能知道我们会推荐什么，但是让我们用一些想法来支持它。

假设你带着一个特定的目标(找出一个 bug，添加一个特性，删除它，等等)到达了一个项目的根。).您需要找到合适的代码，浏览相关文件，查看测试，当您足够自信时，对代码库进行这些更改。

作为开发人员，这个过程是我们的面包和黄油，所以我们最好使它高效。

> 10 个文件的代码库和 100 个文件的代码库哪个更容易维护？

按照领域概念分割代码可以让你专注于代码库的一小部分，而按照技术概念分割代码则迫使你四处跳跃。

## 2。为你所有的领域概念提供一个公共契约(API)

想象你的项目有一个`payments`目录💰-相关代码。我们有一系列组件将我们的支付存储在数据库中，或者连接到第三方服务，如 *Stripe* 。

所有这些组件都是为了履行合同，也就是说，确保`payments`按照它们应该的方式运行。

需要明确的是，我们不是在讨论你的移动应用将调用的向用户收费的 HTTP API。我们说的是一个内部 API，把你的支付目录变成自己的“微服务”(自由使用这个术语)。

你会问为什么？

因为拥有显式 API 可以提供:

*   预期行为的清晰描述。

*   每个人都能同意并承诺的最小测试覆盖率。

*   改变底层实现的自由。

此外，**对于这个 API 来说，知道尽可能少的外部概念是很重要的，比如用户、权限或环境**。这些都不是领域的一部分。它们是我们解决通信层(公共 HTTP 端点本质上是不安全的)或开发工作流问题的方式。

例如，我们可以想象有:

*   一个面向公众的 API，公开一些域行为并控制认证和授权。

*   一个私有的管理 API +面板，提供简单的客户支持，并在不接触任何数据库或控制台的情况下检查错误。

*   这是一种非常简单的编写设备、示例和迁移的方法。

## 3。依赖小界面

这个很受欢迎。作为开发人员，我们不断被提醒要依赖抽象而不是具体的实现，[隔离我们的接口](https://en.wikipedia.org/wiki/Interface_segregation_principle)和[反转我们的依赖关系](https://en.wikipedia.org/wiki/Dependency_inversion_principle)。

你可以很容易地找到大量的理论材料，所以让我们把重点放在一些实际的例子。我们的`Payments`应用可能需要与这些接口对话:

*   事件发布者

*   事件订阅者

*   信用卡充电器

*   电子邮件发送者

所有这些接口都有一个小而明确的角色。稍后，我们将注入特定的实现:

```
production = Payments.new(
  event_publisher: rabbitmq,
  event_subscriber: rabbitmq_replicas,
  credit_card_charger: stripe,
  email_sender: mailgun,
)

development = Payments.new(
  event_publisher: in_memory_bus,
  event_subscriber: in_memory_bus,
  credit_card_charger: stripe_test_mode,
  email_sender: muted_mailer,
) 
```

正如您所看到的，小接口允许我们创建定义良好的测试，并根据环境为每个操作选择最佳策略。另一方面，我们通常基于特定的技术编写实现，以集中所有的知识和助手功能。

## 4。将您的数据从存储策略中分离出来

让我们澄清一下:我们认为 ORM 是错误的(或者可能是人们做错了)。看看这个 *Ruby on Rails* 代码:

```
class Article < ActiveRecord::Base
  belongs_to :user
  has_many :comments, dependent: :destroy

  scope :authored_by, ->(username) { where(user: User.where(username: username)) }

  validates :title, presence: true, allow_blank: false
  validates :body, presence: true, allow_blank: false

  before_validation do
    self.slug ||= “#{title.to_s.parameterize}-#{rand(36**6).to_s(36)}”
  end
end 
```

这里有很多东西要打开。

首先，我们注意到这个对象描述了关系、级联删除和可空属性。这正是您对对象关系映射器的期望。挺透明的！

接下来，让我们考虑一下。代表一篇文章时，对我们来说什么是重要的？：

*   我们应该能够利用我们正在使用的语言的全部力量。当我们使用 Java 时，我们希望能够自由地使用 OO 模式和继承。当我们使用 Haskell 时，我们希望使用联合类型和记录。

*   我们应该能够以不同的格式和数据库存储我们的数据。这允许我们使用 ElasticSearch 进行性能搜索，使用 PostgreSQL 进行一致的状态搜索，使用 Redis 保持我们的 autosave 特性足够快。

ORM 模型两者都不提供，因为它们只是一种与 SQL 数据库交互的方式。我们仍然需要在其他地方表示和操作我们的数据。问题是，一旦你接受了这种说法，使用 ORM 就显得笨拙或过分了。这就是我们的意思:

```
# Let's say we have a series of entities in our domain that we use to represent an article.
class Article; end # The big picture
class Tag; end
class RichText; end # Headings, bold, cross-references, …

# Now we need an interface to store the article's content in our SQL database.
class ArticleStore
  def store(title:, body:, tags:, author:)
    # Ruby doesn't have explicit interfaces, but you get the point
    raise NotImplementedError
  end
end

# Using an ORM creates an additional level of indirection that looks pointless
class ArticleORMStore < ArticleStore
  def store(title:, body:, tags:, author:)
    ArticleModel.create(title: title, body: body, tags: tags, author: UserModel.get(author.id))
  end
end

# A low-level SQL library feels simpler in comparison.
class ArticleSimpleStore < ArticleStore
  def store(title:, body:, tags:, author:)
    article_table.insert(title: title, body: body, tags: tags, author: author.id)
  end
end 
```

这里的底线是:**你可以使用 ORM，但是不要把它们作为表示和操作你的数据的唯一方式**。那与他们的目的相去甚远。

## 5。使用事件来保持应用程序的连接和代码的解耦

如果一个应用程序的两个部分连接在一起，代码必须以某种方式连接起来，对吗？

事件驱动的编程在保持应用程序的互联性方面做得很好，但是你的代码易于编写和维护。事实上，它做得如此之好，以至于类似的想法在移动和前端开发中以[反应式编程](https://medium.com/exploring-code/what-is-reactive-programming-da37c1611382)的名义变得无处不在，并且在运营世界中，[云提供商和公司在它上面下了很大赌注](https://thenewstack.io/event-driven-architecture-wave-future/)。

基本思想是，对您的领域的每一个更改都被表示为一个原子事件。

```
 article_published(…) 1 minute ago
    article_draft_created(…) 5 minutes ago
    user_signed_in(…) 25 minutes ago 
```

所有事件都是通过某种事件总线发布的，随机观察者可以订阅有趣的事件并对其做出反应，而不会过多地打扰其他组件。

起初这需要额外的努力，因为您需要为事件总线打下基础，并考虑每个事件的属性和原子性，但从长远来看，这绝对是值得的。

下面是一些使用事件驱动架构很容易实现的特性的例子，否则很难考虑和维护:

*   听一篇文章的评论，增加一个计数器(目的:更快的评论计数)。

*   向新用户发送欢迎电子邮件。

*   通知文章作者有新的评论。

试着想象一下，你会如何以命令的方式和被动的方式来完成这些任务。

> 事件驱动编程避免了具有许多不同副作用的长函数，并使您的测试更好、更孤立。

* * *

在下一篇文章中，我们将解释如何把所有这些部分放在一起创建我们自己的架构。

我们很乐意在评论区看到你对这些想法的看法！