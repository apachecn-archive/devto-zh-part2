# 通过一个 bug 来和我一起浏览这个网站的源代码

> 原文：<https://dev.to/rhymes/come-with-me-on-a-journey-through-this-websites-source-code-by-way-of-a-bug-odj>

这篇文章的开始是因为我认为研究一个特定的 bug 可能有助于更好地理解 devto 的源代码。所以我在做研究的时候写了这个，最后我也写了一些其他的东西。开始了。

我指的这个 bug 叫做在 GitHub 上删除帖子时[超时。顾名思义，删除帖子会导致服务器超时，从而给用户带来错误。](https://github.com/thepracticaldev/dev.to/issues/821) [@peter](https://dev.to/peter) 在他的 bug 报告中添加了几个我们需要为我们的“调查”保留的细节:这个 bug 并不总是发生(这在寻找解决方案的背景下会更好，确定性总是更好)，它更有可能出现在有许多反应和评论的文章中。

第一个线索:这种情况有时会发生，通常发生在附有大量数据的文章中。

让我们看看是否能在钻研代码之前挖掘出更多的信息*。*

注意:我写这篇文章是为了解释(并扩展)我研究这个问题的方式，而它是在同一时间发生的(嗯，在几天的过程中，但仍然是在同一时间:-D)，所以当我写下它们时，所有的发现对我来说都是新的，如果你读了这篇文章，它们对你来说也是新的。

另一个注意事项:我将在这里交替使用术语“异步”和“进程外”。在这个上下文中，异步意味着“用户不等待调用被执行”，而不是 JavaScript 中的“异步”。更好的说法应该是“进程外”，因为这些异步调用是由一个外部进程通过数据库上的一个队列执行的，该队列带有一个名为[延迟作业](https://github.com/collectiveidea/delayed_job/)的库/gem。

## 参照完整性

ActiveRecord (Rails 的 ORM)像许多其他对象关系映射器一样，是位于关系数据库系统之上的对象层。让我们绕一点弯路，谈一谈在数据库系统中保持数据有意义的一个基本特性:引用完整性。为什么不，窃听器可以等！

[引用完整性](https://en.wikipedia.org/wiki/Referential_integrity)，简单来说，是对那些对如何构建关系数据有奇怪想法的开发人员的一种防御。它禁止在关系的主表中插入没有对应关系的行。通俗地说，它保证在关系中有一个对应的行:如果您有一个包含 10 个城市的表，您不应该有一个地址属于未知城市的客户。有趣的是，MySQL 在默认情况下用了十多年才激活参照完整性，而 PostgreSQL 在那时已经用了 10 年。有时我认为 MySQL 在它的早期版本是一个巨大的 CSV 文件集合，上面是 SQL。我开玩笑的，也许。

有了适当的引用完整性，您可以(大部分)放心，数据库不会让您添加僵尸行，会保持关系更新，如果您告诉它，它会在您之后清理。

你如何指示数据库做所有这些事情？很简单。我将使用来自 PostgreSQL 10 文档 :
的一个例子

```
CREATE TABLE products (
    product_no integer PRIMARY KEY,
    name text,
    price numeric
);

CREATE TABLE orders (
    order_id integer PRIMARY KEY,
    shipping_address text,
);

CREATE TABLE order_items (
    product_no integer REFERENCES products ON DELETE RESTRICT,
    order_id integer REFERENCES orders ON DELETE CASCADE,
    quantity integer,
    PRIMARY KEY (product_no, order_id)
); 
```

Enter fullscreen mode Exit fullscreen mode

表`order_items`有两个外键，一个指向`orders`，另一个指向`products`(如果你想知道的话，这是多对多的经典例子)。

当您设计这样的表时，您应该问自己以下问题(除了像“我到底在用这些数据做什么？”):

*   如果删除了主表中的一行，会发生什么情况？

*   我要删除所有相关的行吗？

*   我是否要将引用列设置为`NULL`？在这种情况下，这对我的业务逻辑意味着什么？`NULL`对我的数据有意义吗？

*   我要将列设置为默认值吗？这对我的商业逻辑意味着什么？这个列有默认值吗？

如果你回头看看这个例子，我们告诉数据库的是以下两件事:

*   产品不能被移除，除非它们没有以任何顺序出现

*   订单可以在任何时候被删除，他们带着这些物品进入坟墓😀

请记住，在这个上下文中删除仍然是一个快速操作，即使在像 dev.to 这样的上下文中，如果一篇文章有与 *cascade* 指令链接的表，它仍然应该是一个快速操作。当单个`DELETE`触发数百万(或数千万)的其他删除时，DBs 往往会变慢。我假设情况不是这样(现在或将来),但是因为这一节的重点是扩展我们关于引用完整性的知识，而不是实际调查 bug，所以让我们继续挖掘。

接下来，我们打开控制台，使用`psql` :
检查表格是否相互链接

```
$ rails dbconsole
psql (10.5)
Type "help" for help.

PracticalDeveloper_development=# \d+ articles
...
Indexes:
    "articles_pkey" PRIMARY KEY, btree (id)
    "index_articles_on_boost_states" gin (boost_states)
    "index_articles_on_featured_number" btree (featured_number)
    "index_articles_on_hotness_score" btree (hotness_score)
    "index_articles_on_published_at" btree (published_at)
    "index_articles_on_slug" btree (slug)
    "index_articles_on_user_id" btree (user_id) 
```

Enter fullscreen mode Exit fullscreen mode

该表有一个主键和一些索引，但显然没有外键约束(参照完整性的指标)。将其与同时包含以下两项的表格进行比较:

```
PracticalDeveloper_development=# \d users
...
Indexes:
    "users_pkey" PRIMARY KEY, btree (id)
    "index_users_on_confirmation_token" UNIQUE, btree (confirmation_token)
    "index_users_on_reset_password_token" UNIQUE, btree (reset_password_token)
    "index_users_on_username" UNIQUE, btree (username)
    "index_users_on_language_settings" gin (language_settings)
    "index_users_on_organization_id" btree (organization_id)
Referenced by:
    TABLE "messages" CONSTRAINT "fk_rails_273a25a7a6" FOREIGN KEY (user_id) REFERENCES users(id)
    TABLE "badge_achievements" CONSTRAINT "fk_rails_4a2e48ca67" FOREIGN KEY (user_id) REFERENCES users(id)
    TABLE "chat_channel_memberships" CONSTRAINT "fk_rails_4ba367990a" FOREIGN KEY (user_id) REFERENCES users(id)
    TABLE "push_notification_subscriptions" CONSTRAINT "fk_rails_c0b1e39717" FOREIGN KEY (user_id) REFERENCES users(id) 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们到目前为止所学到的:当您从 DB 中删除行时，为什么引用完整性会发挥作用，以及在数据库级别上,`articles`表与任何其他表没有明显的关系。但是在 web app 中是这样吗？让我们向上移动一层，深入 Ruby 代码。

*ps。对于 Rails(不记得从哪个版本开始了)，你还可以通过查看 [schema.rb](https://github.com/thepracticaldev/dev.to/blob/master/db/schema.rb) 文件来查看你定义了哪些外键。*

## ActiveRecord，关联和回调

现在我们知道了什么是参照完整性，如何识别它，现在我们知道它在这个 bug 中不起作用，我们可以向上移动一层，检查如何定义[文章对象](https://github.com/thepracticaldev/dev.to/blob/master/app/models/article.rb)(我将跳过我认为与本文和 bug 本身无关的内容，尽管我可能是错的，因为我不太了解代码库):

```
class Article < ApplicationRecord
  # ...

  has_many :comments,       as: :commentable
  has_many :buffer_updates
  has_many :reactions,      as: :reactable, dependent: :destroy
  has_many  :notifications, as: :notifiable

  # ...

  before_destroy    :before_destroy_actions

  # ...

  def before_destroy_actions
    bust_cache
    remove_algolia_index
    reactions.destroy_all
    user.delay.resave_articles
    organization&.delay&.resave_articles
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这段代码中的一些新信息:

*   Rails(但不是 DB)知道一篇文章可以有许多评论、缓冲区更新、反应和通知(在 Rails 行话中这些被称为“关联”)

*   反应明显依赖于文章，如果文章被移除，它们将被破坏

*   在对象及其在数据库中的行被销毁之前，有一个回调函数会做一些事情(我们将在后面探讨)

*   四个关联中有三个是`able`类型，Rails 称这些[多态关联为](https://guides.rubyonrails.org/association_basics.html#polymorphic-associations)，因为它们允许程序员使用两个不同的列(一个包含对象所属模型类型名称的字符串和一个 id)将多种类型的对象关联到同一行。它们非常方便，尽管我总觉得它们使数据库非常依赖于域模型(由 Rails 设置)。他们还可以在关联表中要求一个[复合索引](https://www.postgresql.org/docs/10/static/indexes-multicolumn.html)来加速查询

与底层数据库系统所能做的类似，ActiveRecord 允许开发人员指定当主对象被销毁时，相关对象会发生什么。根据[文档](https://guides.rubyonrails.org/association_basics.html#dependent) Rails 支持:销毁所有相关对象、删除所有相关对象、设置外键为`NULL`或限制删除错误。*销毁*和*删除*的区别在于，在前一种情况下，所有相关的回调都在移除之前执行，在后一种情况下，回调被跳过，只有 DB 中的行被移除。

对于没有`dependent`的关系，[默认策略](https://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html#module-ActiveRecord::Associations::ClassMethods-label-Delete+or+destroy%3F)是什么都不做，这意味着将被引用的行留在原处。如果由我决定，默认将是*该应用程序不会启动，直到你决定如何处理链接的模型*，但我不是设计 ActiveRecord 的人。

请记住，数据库胜过代码，如果您在 Rails 级别没有定义任何东西，但是数据库被配置为自动销毁所有相关的行，那么这些行将被销毁。这是值得花时间学习 DB 如何工作的众多原因之一:-)

关于模型层，我们还没有谈到的最后一点是回调，这可能是 bug 出现的地方。

### 声名狼藉的回调

这个在销毁之前的*回调将在向数据库发出`DELETE`语句之前执行:* 

```
def before_destroy_actions
  bust_cache
  remove_algolia_index
  reactions.destroy_all
  user.delay.resave_articles
  organization&.delay&.resave_articles
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 缓存破坏

回调做的第一件事是调用方法`bust_cache`，方法[依次调用六次 Fastly API](https://github.com/thepracticaldev/dev.to/blob/master/app/labor/cache_buster.rb#L6) 来清除文章的缓存(每次调用 bust 都是两次 HTTP 调用)。它还对同一个 API 进行了大量的进程外调用(大约 20-50 次，取决于文章的状态和标签的数量)，但这些都无关紧要，因为用户不会等待它们。

有一点需要注释:六个 HTTP 调用是*在你按下按钮删除一篇文章后总是*出去。

#### 索引删除

dev.to 使用 Algolia 进行搜索，调用`remove_algolia_index`执行以下操作:

*   调用 [algolia_remove_from_index](https://github.com/algolia/algoliasearch-rails/blob/25436b69de166b60eb220890e2eb8a07ed65ac82/lib/algoliasearch-rails.rb#L587)进而调用 Algolia HTTP API 的“异步”版本，该版本实际上会对 Algolia 进行[(快速)同步调用，而无需等待索引在它们那端被清除。这仍然是一个同步呼叫主题，增加了用户的等待时间](https://github.com/algolia/algoliasearch-client-ruby/blob/2dcf070e2b9f4f446a85d1abf494269acc4dec42/lib/algolia/client.rb#L518)

*   为其他索引调用 Algolia 的 HTTP API 另外两次

因此，加上之前对 Fastly 的 6 个 HTTP 调用，我们在进程中调用了 9 个 API

#### 反应破坏

第三步是`reactions.destroy_all`,顾名思义，就是摧毁对文章的所有反应。在 Rails `destroy_all`中，简单地迭代所有对象，并对每个对象调用 destroy，然后激活所有的“destroy”回调函数进行适当的清理。[反应](https://github.com/thepracticaldev/dev.to/blob/master/app/models/reaction.rb)模式有两个`before_destroy`回调:

```
class Reaction < ApplicationRecord
  # ...

  before_destroy :update_reactable_without_delay
  before_destroy :clean_up_before_destroy

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

我不得不稍微挖掘一下，看看第一个是做什么的(我不喜欢 Rails 做事方式的一个原因是到处出现的神奇方法，它们使重构更加困难，并且它们鼓励模型和所有各种宝石之间的耦合)。`update_reactable_without_delay`绕过队列调用 [update_reactable](https://github.com/thepracticaldev/dev.to/blob/master/app/models/reaction.rb#L76) (默认情况下已经声明为异步函数)。结果是用户等待的标准内联呼叫。

*   如果文章已经发表，则重新计算(这一次在进程之外)文章的分数(这是应该避免的事情，因为文章将被删除)。然后(反向内联)它调用 Algolia 重新索引文章(两次)，从 Fastly 的缓存中删除反应(每个破坏缓存的调用是两个 Fastly 的调用)，破坏另一个缓存(两个以上的 HTTP 调用)，并可能更新文章上的一个列(可能不需要，因为它将被删除)。总共有 6 个 HTTP 调用:一个异步 HTTP 调用(第一个调用到 Algolia)，另一个调用到 Algolia，四个调用到 Fastly。让我们注释下用户必须等待的 5。

*   对 Algolia 的文章进行了重新索引(第三次)。

让我们总结一下:移除一个反应相当于 6 次 HTTP 调用。如果文章有 100 个反应...你可以算一下。

假设这篇文章有 1 个反馈，加上之前统计的 15 个 HTTP 调用:

*   6 .破解文章的缓存

*   3 从索引中删除文章

*   6 对于附在文章上的反应

还有一个额外的额外的 HTTP 调用，我偶然发现它使用了一个[要点来调试 net/http 调用](https://gist.github.com/ahoward/736721)，它调用了 [Stream.io API](https://github.com/GetStream/stream-rails#activerecord) 来删除用户提要中的反应。总共 16 次 HTTP 调用。

这就是当一个反应被破坏时发生的事情(我在我的本地安装中添加了令人敬畏的 gem[http log](https://github.com/trusche/httplog):

```
[httplog] Sending: PUT https://REDACTED.algolia.net/1/indexes/Article_development/25
[httplog] Data: {"title":" The Curious Incident of the Dog in the Night-Time"}
[httplog] Connecting: REDACTED.algolia.net:443
[httplog] Status: 200
[httplog] Benchmark: 0.357128 seconds
[httplog] Response:
{"updatedAt":"2018-10-09T17:23:44.151Z","taskID":945887592,"objectID":"25"}

[httplog] Sending: PUT https://REDACTED.algolia.net/1/indexes/searchables_development/articles-25
[httplog] Data: {"title":" The Curious Incident of the Dog in the Night-Time","tag_list":["discuss","security","python","beginners"],"main_image":"https://pigment.github.io/fake-logos/logos/medium/color/8.png","id":25,"featured":true,"published":true,"published_at":"2018-09-30T07:44:48.530Z","featured_number":1538293488,"comments_count":1,"reactions_count":0,"positive_reactions_count":0,"path":"/willricki/-the-curious-incident-of-the-dog-in-the-night-time-3e4b","class_name":"Article","user_name":"Ricki Will","user_username":"willricki","comments_blob":"Waistcoat craft beer pickled vice seitan kombucha drinking. 90's green juice hoodie.","body_text":"\n\nMeggings tattooed normcore kitsch chia. Fixie migas etsy hashtag jean shorts neutra pork belly. Vice salvia biodiesel portland actually slow-carb loko chia. Freegan biodiesel flexitarian tattooed.\n\n\nNeque. \n\n\nBefore they sold out diy xoxo aesthetic biodiesel pbr\u0026amp;b. Tumblr lo-fi craft beer listicle. Lo-fi church-key cold-pressed.\n\n\n","tag_keywords_for_search":"","search_score":153832,"readable_publish_date":"Sep 30","flare_tag":{"name":"discuss","bg_color_hex":null,"text_color_hex":null},"user":{"username":"willricki","name":"Ricki Will","profile_image_90":"/uploads/user/profile_image/6/22018b1a-7afa-47c1-bbae-b829977828e4.png"},"_tags":["discuss","security","python","beginners","user_6","username_willricki","lang_en"]}
[httplog] Status: 200
[httplog] Benchmark: 0.031995 seconds
[httplog] Response:
{"updatedAt":"2018-10-09T17:23:44.426Z","taskID":945887612,"objectID":"articles-25"}

[httplog] Sending: PUT https://REDACTED.algolia.net/1/indexes/ordered_articles_development/articles-25
[httplog] Data: {"title":" The Curious Incident of the Dog in the Night-Time","path":"/willricki/-the-curious-incident-of-the-dog-in-the-night-time-3e4b","class_name":"Article","comments_count":1,"tag_list":["discuss","security","python","beginners"],"positive_reactions_count":0,"id":25,"hotness_score":153829,"readable_publish_date":"Sep 30","flare_tag":{"name":"discuss","bg_color_hex":null,"text_color_hex":null},"published_at_int":1538293488,"user":{"username":"willricki","name":"Ricki Will","profile_image_90":"/uploads/user/profile_image/6/22018b1a-7afa-47c1-bbae-b829977828e4.png"},"_tags":["discuss","security","python","beginners","user_6","username_willricki","lang_en"]}
[httplog] Status: 200
[httplog] Benchmark: 0.047077 seconds
[httplog] Response:
{"updatedAt":"2018-10-09T17:23:44.494Z","taskID":945887622,"objectID":"articles-25"}

[httplog] Sending: PUT https://REDACTED.algolia.net/1/indexes/Article_development/25
[httplog] Data: {"title":" The Curious Incident of the Dog in the Night-Time"}
[httplog] Status: 200
[httplog] Benchmark: 0.029352 seconds
[httplog] Response:
{"updatedAt":"2018-10-09T17:23:44.541Z","taskID":945887632,"objectID":"25"}

[httplog] Sending: PUT https://REDACTED.algolia.net/1/indexes/searchables_development/articles-25
[httplog] Data: {"title":" The Curious Incident of the Dog in the Night-Time","tag_list":["discuss","security","python","beginners"],"main_image":"https://pigment.github.io/fake-logos/logos/medium/color/8.png","id":25,"featured":true,"published":true,"published_at":"2018-09-30T07:44:48.530Z","featured_number":1538293488,"comments_count":1,"reactions_count":0,"positive_reactions_count":1,"path":"/willricki/-the-curious-incident-of-the-dog-in-the-night-time-3e4b","class_name":"Article","user_name":"Ricki Will","user_username":"willricki","comments_blob":"Waistcoat craft beer pickled vice seitan kombucha drinking. 90's green juice hoodie.","body_text":"\n\nMeggings tattooed normcore kitsch chia. Fixie migas etsy hashtag jean shorts neutra pork belly. Vice salvia biodiesel portland actually slow-carb loko chia. Freegan biodiesel flexitarian tattooed.\n\n\nNeque. \n\n\nBefore they sold out diy xoxo aesthetic biodiesel pbr\u0026amp;b. Tumblr lo-fi craft beer listicle. Lo-fi church-key cold-pressed.\n\n\n","tag_keywords_for_search":"","search_score":154132,"readable_publish_date":"Sep 30","flare_tag":{"name":"discuss","bg_color_hex":null,"text_color_hex":null},"user":{"username":"willricki","name":"Ricki Will","profile_image_90":"/uploads/user/profile_image/6/22018b1a-7afa-47c1-bbae-b829977828e4.png"},"_tags":["discuss","security","python","beginners","user_6","username_willricki","lang_en"]}
[httplog] Status: 200
[httplog] Benchmark: 0.028819 seconds
[httplog] Response:
{"updatedAt":"2018-10-09T17:23:44.612Z","taskID":945887642,"objectID":"articles-25"}

[httplog] Sending: PUT https://REDACTED.algolia.net/1/indexes/ordered_articles_development/articles-25
[httplog] Data: {"title":" The Curious Incident of the Dog in the Night-Time","path":"/willricki/-the-curious-incident-of-the-dog-in-the-night-time-3e4b","class_name":"Article","comments_count":1,"tag_list":["discuss","security","python","beginners"],"positive_reactions_count":1,"id":25,"hotness_score":153829,"readable_publish_date":"Sep 30","flare_tag":{"name":"discuss","bg_color_hex":null,"text_color_hex":null},"published_at_int":1538293488,"user":{"username":"willricki","name":"Ricki Will","profile_image_90":"/uploads/user/profile_image/6/22018b1a-7afa-47c1-bbae-b829977828e4.png"},"_tags":["discuss","security","python","beginners","user_6","username_willricki","lang_en"]}
[httplog] Status: 200
[httplog] Benchmark: 0.02821 seconds
[httplog] Response:
{"updatedAt":"2018-10-09T17:23:44.652Z","taskID":945887652,"objectID":"articles-25"}

[httplog] Connecting: us-east-api.stream-io-api.com:443
[httplog] Sending: DELETE http://us-east-api.stream-io-api.com:443/api/v1.0/feed/user/10/Reaction:7/?api_key=REDACTED&foreign_id=1
[httplog] Data:
[httplog] Status: 200
[httplog] Benchmark: 0.336152 seconds
[httplog] Response:
{"removed":"Reaction:7","duration":"17.84ms"} 
```

Enter fullscreen mode Exit fullscreen mode

如果你数他们，他们是 7 岁，而不是 16 岁。这是因为对 Fastly 的调用只在生产中执行。

#### 保存文章

[User.resave_articles](https://github.com/thepracticaldev/dev.to/blob/master/app/models/user.rb#L338) 刷新用户的其他文章，并在进程外被调用，因此我们现在对它不感兴趣。如果文章是一个组织的一部分，同样的情况也会发生，所以我们不在乎。

让我们回顾一下到目前为止我们所知道的。每篇文章的删除都会触发一个回调，这个回调会做很多涉及第三方服务的事情，帮助这个网站尽可能快地运行，它还会更新各种计数器，我并没有真正研究过这些计数器:-D。

### 文章删除后会发生什么

在处理完回调，所有不同的缓存都是最新的，数据库中的反应都消失了之后，我们仍然需要检查我们要删除的文章的其他关联发生了什么。正如您所记得的，每篇文章都可能有评论、反应(现在已经没有了)、缓冲区更新(不确定它们是什么)和通知。

我们来看看销毁一篇文章会发生什么，看能不能得到其他线索。我用我的总结代替了一篇长日志:

```
> art = Article.last # article id 25
> art.destroy!
# its tags are destroyed, this is handled by "acts_as_taggable_on :tags"...
# a bunch of other tag related stuff happens, 17 select calls...
# the aforamentioned HTTP calls for each reaction are here too...
# there's a SQL DELETE for each reaction...
# the user object is updated...
# a couple of other UPDATEs I didn't investigate but which seem really plausible...
# the HTTP calls to remove the article itself from search...
# the article is finally deleted from the db...
# the article count for the user is updated 
```

Enter fullscreen mode Exit fullscreen mode

除了在我最初的概述中我完全忘记了标签的销毁(它们相当于数据库中的一个`DELETE`和一个`UPDATE`)之外，我要说当一篇文章被删除时会发生很多事情。

我们在控制台中没有找到的其他对象会怎么样？

如果您还记得我前面说过的话，在 Rails 关系中，所有没有被明确标记为“依赖”的东西在主对象销毁后仍然存在，所以它们都在 DB:

```
PracticalDeveloper_development=# select count(*) from comments where commentable_id = 25;
 count
-------
     3
PracticalDeveloper_development=# select count(*) from notifications where notifiable_id = 25;
 count
-------
     2
PracticalDeveloper_development=# select count(*) from buffer_updates where article_id = 25;
 count
-------
     1 
```

Enter fullscreen mode Exit fullscreen mode

我认为我们可以有一点信心，如果这篇文章在被删除之前真的很受欢迎，有很多反应、评论和通知，那么引发这篇文章的问题很可能会出现。

### 超时

我在对这个问题的评论中提到的另一个因素是 Heroku 的默认超时设置。dev.to IIRC 在 Heroku 上运行，一旦路由器处理了 HTTP 调用，它就有一个 30 秒的超时(所以它是你的应用程序代码的计时器)。如果应用程序在 30 秒内没有响应，它会超时并发送一个错误。

dev.to 聪明地使用[机架超时](https://github.com/heroku/rack-timeout#rails-apps)默认服务超时(15 秒)将该超时减半。

简而言之:如果点击“删除文章”按钮后，服务器没有在 15 秒内完成，就会出现超时错误。看到一篇受欢迎的文章可能会触发几十个 HTTP 调用，你就能理解为什么在某些情况下 15 秒墙会被打破。

## 重述

让我们回顾一下到目前为止我们所了解到的当一篇文章被删除时会发生什么:

*   如果文章有数百万个相关行(在这种情况下不太可能)，引用完整性可能是一个因素

*   Rails 顺序删除相关的对象是一个因素(考虑到在删除这些对象之前，它还必须将这些对象从 DB 加载到 ORM，因为如果它想要触发各种回调，它必须这样做)

*   内联回调和 HTTP 调用是另一个因素

*   Rails 一点也不聪明，因为它可以减少对 DB 的调用量(例如通过缓冲所有反应的`DELETE`语句，并使用一个`IN`子句)

*   Rails 魔法有时很烦人😛

## 可能的解决方案

这是我现在停下来的地方，因为我不熟悉代码库(嗯，在这之后肯定更:D)，因为我认为这可能是一个有趣的“集体”练习，因为它不是一个需要“昨天”修复的关键错误。

首先，人们脑海中可能出现的最简单的解决方案是，通过将所有事情委托给队列管理器将拾取的作业，来移动在进程外调用中删除文章时内联发生的所有事情。用户只需要文章从他们的视野中消失。正确的移除可以通过工作进程进行。除了我不确定我考虑了所有正在发生的事情(正如你所看到的，我偶然发现了标签)和所有的影响，我认为这只是一个快速的胜利。它可以通过掩盖报告的问题来解决用户的问题。

另一个可能的解决方案是将删除分为两个主要部分:需要更新或清空缓存，需要从 DB 中删除行。缓存都可以在进程外销毁，这样用户就不必等待 Fastly 或 Algolia(也许只有 Stream.io？我不知道)。这需要一点重构，因为我谈到的一些代码也被应用程序的其他部分使用。

一个更完整的解决方案是在第二个解决方案的基础上更进一步，同时清理所有剩余的内容(注释、通知和缓冲区更新)，但是它们留在那里可能是有原因的。所有这三个实体都可以在一个单独的作业中删除，因为三个实体中有两个在 destroy 回调之前有*，这些回调触发了我还没有看到的其他东西。*

这对用户来说应该足够了，再也不会遇到讨厌的超时错误。为了更进一步，我们还可以研究 ActiveRecord 为它从数据库中移除的每个对象发出一个单独的`DELETE`的事实，但是这对于现在来说肯定太多了。如果需要的话，我会在某处对此进行注释，并在重构后再回来讨论。

## 结论

如果你还和我在一起，谢谢。我花了很长时间来写这个:-D

我没有任何强有力的结论。我希望对 dev.to 源代码的深入研究至少有所帮助。对我来说，这是一个很好的方式来学习更多的东西，并写一些非 Rails 开发人员可能不知道的东西，但更重要的是帮助潜在的贡献者。

我当然希望得到一些反馈；-)