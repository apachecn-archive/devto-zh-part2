# 比较 ActiveRecord 和 LuckyRecord

> 原文：<https://dev.to/jwoertink/comparing-activerecord-and-luckyrecord-4nh5>

TL；LuckyRecord 医生是一个伟大的举动，并且一直在变得更好。

这不是一个真正的教程，但更多的只是用 ActiveRecord 作为我的 ORM 和用 LuckyRecord 作为我的 ORM 写代码的感觉。他们各自使用不同的模式，所以两者之间的思维过程大不相同。

我正在使用 postgres，我已经建立了一个带有`users`表的数据库，看起来有点像:

```
CREATE TABLE actors(
  id SERIAL PRIMARY KEY,
  name character varying,
  slug character varying,
  created_at timestamp NOT NULL DEFAULT now(),
  updated_at timestamp NOT NULL DEFAULT now()
); 
```

Enter fullscreen mode Exit fullscreen mode

## 有活动记录的生活

需要的宝石有

```
# Gemfile
source "https://rubygems.org/"

# This comes with rails
gem 'activerecord'
gem 'pg' 
```

Enter fullscreen mode Exit fullscreen mode

然后将所有位分解成一个文件

```
require "pg"
require "active_record" #c'mon rails, get your naming together >_<

ActiveRecord::Base.establish_connection(
  adapter: "postgresql",
  database: "my_database_name",
  username: "postgres",
  host: "localhost"
)

class Actor < ActiveRecord::Base
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用我们的模型来搜索和操作。

```
person = Actor.find(1)
puts "They call me Funky #{actor.name.capitalize}"

person.name = "Chris Pratt"
person.save 
```

Enter fullscreen mode Exit fullscreen mode

我们通过一个 ID 找到一个非常具体的记录，然后我们可以操作那个记录。很棒的东西...也就是说，除非数据库没有返回记录。使用 ActiveRecord 的`find`方法，当没有找到记录时，它将引发一个异常。这个例子有点做作，所以实际上你可能会有类似于
的东西

```
actor = Actor.find_by(slug: params[:slug])
puts "They call me Funky #{actor.name.capitalize}" 
```

Enter fullscreen mode Exit fullscreen mode

其中`params`是来自用户的一些输入。也许您的 URL 中有 ID，但是 URL 中的 ID 可能是泄漏的元数据，看起来不太好，尤其是出于 SEO 的原因。所以在我的例子中，我有 slugs，它是名称的参数化版本，比如`chris-pratt`。这样，网址读起来更像是`/actors/chris-pratt`对`/actors/145432523`。

使用 ActiveRecord 的`find_by`方法，如果没有找到记录，它将返回`nil`。这将导致上述示例中的问题。您会看到一个`Undefined method name for NilClass`错误。

```
actor = Actor.find_by(slug: params[:slug])
if actor.present?
    puts "They call me Funky #{actor.name.capitalize}"
end 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在它不会执行我们的动作，除非我们有一个记录。或者，我们可以使用约定`find_by!`让它在没有找到记录时引发一个异常。在一个遗留应用程序中，你可能会发现这两种惯例的混合(就像我的一个应用程序)。

我们花了时间来捕捉记录是否存在，但是我们忽略了一件事，那就是对于旧的模式，它是`first_name`和`last_name`。在某个时候，它被合并到了`name`中，但是那个字段从来没有被设置为必需的，或者对它是否应该是唯一的没有任何验证。我想有可能两个演员有相同的名字。像迈克尔·乔丹(篮球运动员/太空堵塞)，迈克尔·B·乔丹(黑豹的表弟)，或者凯特·哈德森(戈尔迪的女儿)，凯特·哈德森(凯蒂·佩里的真名)。

在这种情况下，我们现在注意到有时我们的 actors 索引页面会被`Undefined method capitalize for NilClass`放大。又是那个该死的 NilClass 错误！这使得我们的代码充满了`name.try(:capitalize)`和我最喜欢的`name&.capitalize`。它的工作，但它真的开始变得凌乱捕捉这些火灾在 bugsnag 和部署每天与一些“零捕捉修复”。

除了我们必须处理的所有坏数据，我们还有一些严重的速度问题。我们有一个索引页，列出了所有的演员。这个列表是按照最新的电影版本排序的。所以页面上的第一个演员正在看最近的电影。我们也有很多不同的网站，向观众展示不同类型的电影。某些电影将在某些网站上播放(想想儿童网站上的儿童电影，喜剧网站上的喜剧电影，等等...).这种结构有点令人困惑。

```
SELECT actors.*, movie_releases.latest_release_date FROM actors
INNER JOIN performances ON performances.actor_id = actors.id
INNER JOIN movies ON movies.id = performances.movie_id
INNER JOIN movie_releases ON movie_releases.releasable_id = movies.id
WHERE movie_releases.genre_site_id = ?
ORDER BY movie_releases.latest_release_date DESC 
```

Enter fullscreen mode Exit fullscreen mode

使用 ActiveRecord，当您有一个类似这样的复杂查询时，它将为找到的每条记录生成一个 ActiveRecord 对象。在我们添加大规模缓存机制之前，这个页面大约在 12 秒内加载。我们用[清漆](http://varnish-cache.org/)来处理这个。围绕这一点还有一大堆其他问题。

## 生活中的幸运记录

好了，所以 LuckyRecord 不是用 Ruby 写的，是用 [Crystal](https://github.com/crystal-lang/crystal) 写的。现在，就速度而言，将 Ruby 与 Crystal 进行比较是不公平的。Crystal 是编译的，Ruby 不是。但是因为 Crystal 是编译的，我们知道我们得到了开箱即用的速度。这应该可以解决我们加载时间长的问题，并让我们摆脱繁重的缓存工作。反过来，这将为我们节省资金。

为什么是 LuckyRecord？如果有这么多其他的框架和东西，为什么没有其他的呢？《幸运》是由恰好为[思维机器人](https://thoughtbot.com/)工作的[保罗·史密斯](https://medium.com/@paulcsmith)撰写的，他们是 Rails 社区中相当大的一部分。这意味着我至少可以相信代码是相当可靠的。其次，这不是我们的首选。我们的第一选择我们不能被解雇。医生很糟糕，感觉太笨拙了。我们在几个不同的框架中构建了一个概念验证，幸运应用是唯一一个从 A 到 B 而无需我们将笔记本电脑扔向墙壁的应用。我相信，如果我们今天就开始，事情可能会有所不同，因为其他一切都取得了如此大的进展。无论如何，我们对自己的选择很满意，所以它成功了。

让我们看看上面使用 LuckyRecord 的设置。

您首先需要将这些添加到您的`shard.yml`

```
dependencies:
  pg:
    github: will/crystal-pg
  # these come with lucky
  lucky_record:
    github: luckyframework/lucky_record
  lucky_inflector:
    github: luckyframework/lucky_inflector 
```

Enter fullscreen mode Exit fullscreen mode

现在来设置它。

```
require "pg"
require "lucky_inflector"
require "lucky_record"

LuckyRecord::Repo.configure do
  settings.url = "postgres://postgres@localhost/my_database_name"
end

class Actor < LuckyRecord::Model
  table :users do
    column name : String?
    column slug : String?
  end
end

class ActorForm < Actor::BaseForm
  fillable name
end 
```

Enter fullscreen mode Exit fullscreen mode

首先，你会注意到一些事情。Lucky 将每个组件分成更小的组件(类似于 rails)。这意味着如果你想在 Lucky 之外使用 LuckyRecord，你需要一些位。接下来，我们将看到模式就在模型中。在 Ruby 中，我记得有一次使用 MongoDB，必须在模型中设置模式。我真的很喜欢这样，因为模特是我所有模特需求的第一个去处。最后，您会注意到这里有一个额外的带有`ActorForm`的类。

### 表格

使用 ActiveRecord，您的模型连接到数据库，处理查询、业务逻辑和所有类型的东西。大多数 ruby 爱好者将抽象出这些模式，并添加突变、串行化器和其他模式。这很好，除了当涉及到模式时，开发人员往往过于固执己见。这意味着你开始越来越少看到这些 rails 应用看起来越来越不同。

Lucky 将这种方法从经典的 rails 中带了回来，因为开发人员无论如何都要打破常规，为什么不从一开始就这么做呢？`ActorForm`用于创建和更新您的记录。假设您有一个允许用户输入数据并创建/更新记录的表单。LuckyRecord 为您提供了一种安全的方式来允许他们可以更新哪些字段，并提供了一个位置来放置所有关于创建和更新记录的逻辑。这很好，因为当您创建一个记录时，您可能还需要创建其他几个记录。现在，您的模型不负责创建其他模型。它是一个表单对象，负责用户用来做它需要做事情的表单。

这也可以让你创建其他形式，比如`AdminActorForm`，或者其他。也许您的用户拥有更高的权限，可以对记录做更多的事情。它看起来像这样:

```
actor = ActorForm.new
actor.name.value = "Chris Pratt"
actor.slug.value = "chris-pratt"
actor.save! 
```

Enter fullscreen mode Exit fullscreen mode

当然，你也可以做验证和所有有趣的事情。最重要的是，这些都是在模型之外处理的。

好，那么查询模型呢？

```
query = Actor::BaseQuery.new.name("Chris Pratt")
query.map(&.name) #=> ["Chris Pratt"] 
```

Enter fullscreen mode Exit fullscreen mode

当您定义模型时，LuckyRecord 提供了一些现成的命名空间类。一个用于表单对象，另一个用于查询对象。这允许您存储您需要的所有不同的 SQL 查询。您可以创建一个子类，为您完成所有这些设置！

```
class ActorQueries < Actor::BaseQuery
  # SELECT actors.* FROM actors WHERE actors.name = ? ORDER BY actors.name ASC
  # returns Array(String)
  def self.by_name(name : String)
    self.new.name(name).name.asc_order.map(&.name)
  end
  # or a chainable query method
  def by_name(_name : String)
    name(_name).name.asc_order
  end
end

ActorQueries.by_name("Chris Pratt") 
```

Enter fullscreen mode Exit fullscreen mode

从 crystal 的角度来看，好的一面是，如果编译器捕捉到一个可能将 nil 传递给这个方法的地方，它会抛出一个编译时错误。`No method overload matches by_name for ActorQueries with compile-time (String | Nil)`。或者至少我认为这个错误是这样的😅。基本思想是，编译器说方法签名只接受一个字符串作为参数，但是在你的代码中有一个地方可能*潜在地*为零。

Lucky 的整个理念是，所有这些你通常在产品中看到的错误，在你的应用程序启动之前，你就会在开发中看到。抽象，对象的不同用法，甚至到你创建 HTML 有多幸运，都是经过设计的，所以在生产中一切都是坚如磐石。

当然，总会有一些东西漏掉，但这比在 Ruby 中要少得多。

## 重述时间

这有一些优点和缺点，所以让我们把它们列出来(不分先后)。

1.  幸运的是捕获编译时错误，这样生产中的问题就少了
2.  水晶给你编译的速度
3.  LuckyRecord 将逻辑分解成更小的组件，以使测试更容易，并专注于重要的事情
4.  LuckyRecord 只支持 postgres。如果你需要 MySQL 或者其他东西，你可以在不同的 ORM 上使用 Lucky。
5.  LuckyRecord 构建查询的方式对性能有一点影响。这可能以后会有所改善，但没什么大不了的。还是比 ActiveRecord 快了大约[12 倍。](https://github.com/jwoertink/crystal_orm_test/#results)
6.  存在所有这些编译时错误会大大降低开发速度。这不是真正的骗局，因为无论如何你都要付出代价。选择是发展，还是生产。
7.  目前 LuckyRecord 中还缺少一些功能，但由于整个 crystal 生态系统仍然是新的，你可以尝试使用任何东西。事实上，我已经使用了 crystal 中的所有 ORM，但没有一个能满足我们应用程序的所有需求。
8.  幸运的的[文件真的很好。而且非常漂亮！(是的，这很重要)。](https://luckyframework.org/guides/overview/)
9.  幸运的的[社区也真的很棒！帮助通常很快。](https://gitter.im/luckyframework/Lobby)
10.  我喜欢玉米卷。

感谢阅读！

(作者注:这篇文章是在几天内用 9 种不同的方式和不同的思维过程写成的。如果它令人困惑，让我知道，这样我可以使它更连贯)