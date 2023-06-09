# 在 Elixir 中实现有界上下文

> 原文：<https://dev.to/leandrocp/implementing-bounded-contexts-in-elixir-h76>

如果我们可以编写松散耦合的服务，而没有常见微服务架构的开销，会怎么样？2015 年，Valim 写了一篇名为微服务时代的[Elixir](http://blog.plataformatec.com.br/2015/06/elixir-in-times-of-microservices/)的文章，阐述了他对 Elixir 如何适应微服务架构的想法。这是那种令人兴奋的帖子，它挑战我们尝试不同的方法来编写软件，并且那篇帖子是我在这个问题上的实际方法。

**TL；博士**

*   创建一个伞式项目；
*   每个有界上下文一个应用程序；
*   一个 app 永远不要从另一个 app 调用内部代码；
*   在每个 app 上使用一个模块作为公共 API，在 app 之间交换数据，也可以调用函数对数据进行变异；
*   一个应用程序不应该在公共 API 上返回其内部数据结构，它应该只返回原始数据，如*地图*和*列表*；

## 首先，什么是有界语境？

如果你对此一无所知，我推荐你在继续之前阅读一下马丁·福勒的文章。

根据大书[领域建模制作功能](https://pragprog.com/book/swdddf/domain-modeling-made-functional):

> [……]问题空间中的域和子域被映射到 DDD 术语所称的有界上下文——我们实现中的一种子系统。每个有界上下文本身就是一个小型领域模型。我们使用短语有界上下文而不是子系统之类的东西，因为它有助于我们在设计解决方案时专注于重要的东西:意识到上下文并意识到边界。为什么是语境？因为每个上下文代表解决方案中的一些专门知识。在上下文中，我们有共同的语言，设计是连贯和统一的。但是，正如在现实世界中一样，脱离上下文的信息可能会令人困惑或无法使用。为什么有界？在现实世界中，领域具有模糊的边界，但是在软件世界中，我们希望减少独立子系统之间的耦合，以便它们能够独立发展。我们可以使用标准的软件实践来做到这一点，比如在子系统之间使用显式 API，避免依赖，比如共享代码。

这给了我们一些提示，告诉我们应该如何实现有界上下文:

*   应该独立
*   应该有一个明确的 api 来交换数据
*   避免共享代码

## 保护伞项目

如果你知道[伞状项目的概念，](https://elixir-lang.org/getting-started/mix-otp/dependencies-and-umbrella-projects.html#umbrella-projects)你可能会想我们可以将每个有界的上下文实现为伞状项目的应用。你是对的，但是为了保持整洁的组织和避免造成难以发展的混乱，需要一些规则。

但是什么是伞式项目和应用呢？

一个应用程序只是一个代码包，它可以是一个微型库、一个组件、一个子系统或者一个完整的应用程序。ERP 系统可以由客户、计费、交付等应用组成。

而伞状项目就是由那些 app 组成的项目，它们生活在同一个项目中，可以相互依赖。

想了解更多相关信息吗？前往[使用仙丹伞](https://8thlight.com/blog/georgina-mcfadyen/2017/05/01/elixir-umbrella-projects.html)帖子。

## 显示代码

说够了，让我们看看它是如何工作的。让我们用 Martin Fowler 文章中的同一个例子来建立一个可以用于我们系统的例子。

[![Bounded Context example from Martin Fowler article](img/15debeed896c3fde338beb200ac8fa24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vDlb7aVM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://leandrocp.com.br/img/posts/bounded_contexts.png)

*马丁·福勒文章中的有界上下文示例*

在这个例子中，我们有两个有界的上下文，*销售*和*支持*，它们有自己的规则和知识。请记住，每个上下文应该是独立的，所以让我们从创建一个名为“SystemX”的新的 Elixir 保护伞项目开始，该项目包含两个应用:

```
➜ mix new system_x --umbrella
➜ cd system_x/apps
➜ mix new sales
➜ mix new support 
```

您将得到下面的目录结构:

```
➜ tree
.
├── README.md
├── apps
│ ├── sales
│ │ ├── README.md
│ │ ├── config
│ │ │ └── config.exs
│ │ ├── lib
│ │ │ └── sales.ex
│ │ ├── mix.exs
│ │ └── test
│ │ ├── sales_test.exs
│ │ └── test_helper.exs
│ └── support
│ ├── README.md
│ ├── config
│ │ └── config.exs
│ ├── lib
│ │ └── support.ex
│ ├── mix.exs
│ └── test
│ ├── support_test.exs
│ └── test_helper.exs
├── config
│ └── config.exs
└── mix.exs 
```

### 实体

正如您在我们的领域中看到的，我们在销售和支持上下文中都有实体客户。这是因为每个上下文可能都有特定的规则或数据，并独立发展，这使我们可以在不破坏整个系统的情况下修改其中的每个规则或数据，因为它仅限于内部上下文。

我们来定义一下每个客户:

```
# apps/sales/lib/domain/customer.ex
defmodule Sales.Domain.Customer do
  defstruct [:id, :name, :address, :score, :has_support_ticket]
end

# apps/support/lib/domain/customer.ex
defmodule Support.Domain.Customer do
  defstruct [:id, :name, :last_ticket_id, :email]
end 
```

### 公共 API

如果销售上下文需要从客户那里获得一些关于支持上下文的信息，会发生什么情况呢？这时 explict API 就派上用场了。假设我们需要知道客户是否有支持票证，那么我们需要询问此信息以获得支持上下文。因此，让我们创建支持显式 API 来返回客户:

```
# apps/support/lib/support.ex
defmodule Support do
  def get_customer(customer_id) when is_integet(customer_id) do
    find(customer_id) # get customer from db
    |> Map.from_struct()
  end
end 
```

返回一个*映射*而不是一个*结构*给了我们应用程序之间的松散耦合。我们一会儿会详细讨论它。

### 付诸实施

现在，让我们在销售上下文中创建一个计算当前客户分数的服务。业务规则是:如果客户没有机票支持，它会给出更高的分数(*我知道这不是世界上最好的例子……*)

```
# apps/sales/lib/client/support.ex
defmodule Sales.Client.Support do
  alias Sales.Domain

  def get_customer(%Domain.Customer{id: customer_id} = customer) do
    customer_has_support_ticket =
      customer_id
      |> Support.get_customer()
      |> has_support_ticket()

    %Domain.Customer{customer | has_support_ticket: customer_has_support_ticket}
  end

  defp has_support_ticket(%{last_ticket_id: last_ticket_id}) when is_nil(last_ticket_id), do: false
  defp has_support_ticket(_), do: true
end

# apps/sales/lib/service/customer.ex
defmodule Sales.Service.Customer do
  alias Sales.Domain
  alias Sales.Client

  def current_score(%Domain.Customer{} = customer) do
    customer = Client.Support.get_customer(customer)

    case customer.has_support_ticket do
      false -> 100
      true -> 50
    end
  end
end 
```

好吧，不仅仅是简单的服务。让我解释一下。

[![Contexts API](img/f3342ef4e265087c02c828e94d48760b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V-t-pMjF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://leandrocp.com.br/img/posts/contexts.png)

应用程序只能通过公共 API 相互通信。灰色方框仅供内部使用。

销售上下文中的客户实体对支持票证一无所知，这是意料之中的，因为该信息由支持上下文负责，但是我们需要它来构建我们的服务`Sales.Service.Customer`。

为了克服销售上下文中的信息缺乏，我们可以向支持上下文询问此信息，但请记住，我们不能在应用程序之间共享代码，也不能从另一个应用程序调用内部代码。这是公共 API 的目标，也是它只能返回原始数据，如*地图*和*列表*而不是从支持应用返回内部*结构*的原因。

但是我们不希望在销售应用程序中内部使用无意义的*地图*，我们实际上希望我们的域通过将它映射到*结构*来反映真实生活，这是在`Sales.Client.Support`中完成的——接收*地图*，使用它需要的信息并填充`Sales.Domain.Customer`。注意，在`Sales.Domain.Customer`中，我们没有保留属性 *last_ticket_id* ，因为我们不关心销售上下文中的实际票证 id，我们只需要知道它是否为空。请记住，来自真实世界的相同实体根据上下文可能具有不同的属性。

转换边界上的数据结构使*服务层*更清晰、更易于编写和理解，因为在公共 API 以下的层中，我们处理为当前上下文准备的经过验证的数据结构，这意味着您不需要担心脏数据，这使我们不必编写条件代码。

### 维护

在边界上进行这种数据转换还有一个原因:维护。

如果您在服务、领域、测试等销售上下文中使用 struct `Support.Domain.Customer`,并且出于某种原因，您必须将属性 *last_ticket_id* 重命名为任何其他名称，会发生什么情况？是的，没错…你必须修改很多代码。但是使用客户端允许您在客户端处理这种情况，同时保持*结构* `Sales.Domain.Customer`完全相同，避免重大重构。只是客户端层发生了变化。这是一个简单的例子，但我认为它足以得到 ideia。

如何组织内部上下文代码不在本文讨论范围之内。

### 合同

你可能会疑惑:如果公共 API 返回一个*地图*，我怎么知道这个*地图*的内容(契约)？哪些属性存在或不存在，我应该期待哪种数据类型等等？我如何知道调用是否成功？答案是:使用[类型规格](https://hexdocs.pm/elixir/typespecs.html)。

```
defmodule Support do
  @type customer :: %{id: integer, last_ticket_id: integer}
  @spec get_customer(integer) :: {:ok, customer} | {:error, binary}
  def get_customer(customer_id) when is_integet(customer_id) do
    # your logic here ...
    # return the common pattern {:ok, data} or {:error, data}
    {:ok, customer}
  end
end 
```

Typespecs 是关于透明度的。您知道调用该函数会得到什么，也知道该函数作为参数会得到什么。它还具有使用 [ex_doc](https://github.com/elixir-lang/ex_doc) 库生成有用文档的优势，甚至可以使用支持该功能的文本编辑器生成实时文档。

然而，它并不强制“自动数据验证”，这意味着即使 typespec 说它需要一个*字符串*，您也可以传递一个*整数*。为了帮助你完成遵守合同的任务，我推荐使用 [Dialyxir](https://github.com/jeremyjh/dialyxir) 这个工具会进行静态代码分析，如果有任何不符合预期的情况，它会警告你。

## 什么时候该用雨伞 app？

把所有东西都放在一个应用程序中，或者把每个部分都分成几个伞状应用程序，这是一个平衡的问题，需要遵循一些规则。创建许多应用程序可能有些多余，但在某些情况下，值得考虑创建一个新的应用程序:

*   系统的这一部分是由另一个团队开发的，可以是同一公司的内部或离岸团队，也可以是外包团队。

*   系统的这一部分由多个应用程序共享。这是共享库更常见的情况。

*   系统的这一部分需要单独部署在另一个服务器或不同的权限模式中(有些是公共的，有些是私有的)。— [酒厂发布的](https://hexdocs.pm/distillery/introduction/understanding_releases.html)可以帮你做到这一点。

*   需要在同一个系统中使用多个框架。

其他的都可以完美的活在同一个 app 里。

## 关于伞形项目的一个说明

如果你不像我们在这里讨论的那样遵循一些规则，一个伞状项目可能会失去控制。把每个应用程序都看作是独立的，也就是说:你可以把它从雨伞上拿下来，而不会弄坏太多东西。这就是为什么我们不能从另一个应用程序调用内部函数，而需要依赖公共 API 的原因。

Elixir 中没有任何东西禁止或阻止您从另一个应用程序调用内部代码，而只是使用公共 API。这是一个你必须采用的惯例，并且总是检查代码以不违反这个规则。

伞式项目的另一个常见问题是循环依赖，当应用程序 A 依赖于应用程序 B 时，由于某种原因，你也试图让 B 依赖于 A。然后你会得到一个循环依赖错误。为了避免这种情况，你必须仔细设计有界上下文之间的[关系。](https://markhneedham.com/blog/2009/03/30/ddd-recognising-relationships-between-bounded-contexts/)

另一个观察是，不是每个人都喜欢从事伞状项目，这没关系。如果你使用不同的应用程序，同样的想法和规则也适用。

总而言之，保护伞项目介于整体服务和微服务之间。如果做得好，你会两全其美。

## 奖励:分布式应用

伞形项目是启动一个新项目并让事情运转起来的好方法，但是它有[个已知问题](https://elixir-lang.org/getting-started/mix-otp/dependencies-and-umbrella-projects.html#dont-drink-the-kool-aid)可能会在未来引发问题。但不要对此过于担心，因为这里讨论的方法可以通过将每个公共 API 实现为 GenServer 来将这些应用程序分解为分布式应用程序，这些应用程序可以使用像 [Swarm](https://github.com/bitwalker/swarm) 或 [Horde](https://github.com/derekkraan/horde) 这样的工具进行全球注册。但这是另一个帖子的主题，如果你想了解更多，请告诉我。

## 我没说什么

域类型、反腐败层、CQRS、dto、Sagas、CRDT 和其他一些技术和模式在这种架构中可能是有用的，但是我不能在一篇文章中讨论所有的内容，而且你可能不需要所有的内容。请记住，第一步也是最重要的一步是组织您的项目，使其以敏捷的方式发展。

感谢 Eduardo Hernandes 花了几个小时讨论长生不老药、DDD 和一些疯狂的东西，也感谢你审阅这篇文章。