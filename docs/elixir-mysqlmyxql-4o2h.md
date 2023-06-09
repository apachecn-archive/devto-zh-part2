# Elixir :开始开发新的 MySQL 驱动程序 MyXQL

> 原文：<https://dev.to/gumi/elixir-mysqlmyxql-4o2h>

Plataformatec 在 Elixir Forum 上宣布，[正在开发新的 MySQL 驱动程序“myxql”](https://elixirforum.com/t/announcing-a-new-mysql-driver-myxql/17124)。 [据说目标是与 Ecto](https://github.com/elixir-ecto/ecto) 集成，作为默认的 MySQL 驱动程序。

目前，与 MySQL 进行交互的主要驱动程序是[Mariaex](https://github.com/xerions/mariaex) 。 与 Mariaex 的合作是从 2014 年 12 月开始的。 对社区来说是极其重要的软件。 Mariaex 使许多依赖 MySQL 数据库的公司能够通过 Ecto 和 Phoenix 等库将 Elixir 用于产品。

# Mariaex 和 Elixir 的迄今为止

Mariaex 问世后，发生了以下事情。

*   MySQL 的 v5.7 和 v8.0 等新版本中添加了新的认证模式、协议更改或新的数据类型等。 Mariaex 项目必须支持所有因这些版本而异的功能。
*   Mariaex 的开发是在 Elixir v1.0 之后不久开始的。 从其经过来看，`Kernel.SpecialForms`的[`with`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#with/1)等最近的 Elixir 功能未必能充分发挥。 Elixir v1.3 采用了新的[`calendar`](https://hexdocs.pm/elixir/Calendar.html#t:calendar/0)型。 这将与 Mariaex 具备的`date`/ `type`型竞争。
*   Mariaex 开始时，Ecto 为 v0.3.0。 随后发布了 Ecto v1.0，并推出了 Ecto v2.0。 在 Ecto v1.0 的时候，Ecto 负责连接池和事务等许多事务。 但是，在 Ecto v2.0 中，我们通过一个名为 DBConnection 的公共项目将重心转移到了驱动器上。 这一新方法使您能够在 Ecto 之外更高效地利用驱动程序(请参见 James Fish「 [db connection-ecto ' s SQL sandbox](http://www.elixirconf.eu/elixirconf2017/james-fish)”)。 在受益的同时，此迁移将对现有驱动程序的代码进行修改。

加上这些因素和其他因素，Mariaex 存储库变得越来越复杂，维护也越来越困难( 40 个未解决的队列，9 个拉式请求)。 讨论了如何改善 Mariaex 的代码库后，得出了新开发 MyXQL 的结论。 消除迄今为止的隔阂，从头开始是最好的办法。

# MyXQL 的目标

MyXQL 的开发由[Wojtek Mach](http://blog.plataformatec.com.br/2018/08/welcome-wojtek-mach-to-plataformatec/) 先生主导。 MyXQL 的目标如下。

*   MyXQL 需要 Elixir v1.4 和 MySQL v5.7.9 或更高版本。 它将支持最近添加到 MySQL 中的许多功能和数据类型，并支持与此相同的 Elixir 功能。
    *   [Elixir v1.4.0](https://github.com/elixir-lang/elixir/releases/tag/v1.4.0) 将于 2017 年 1 月发布。
    *   MySQL v5.7.9 最初是从 2015 年 10 月发布的 5.7 分支开始向公众发布的。
*   利用从 Postgrex 和 Mariaex 驱动程序开发经验中获得的知识。 MyXQL 反映 Postgrex 的组织和代码结构，并利用从 Mariaex 获得的经验。 在这个计划中，两个项目的零件会根据需要被再利用吧。

# MyXQL 的开发

请注意，MyXQL 来不及发布 Ecto v3.0。 目前，开发必须继续使用 Mariaex。 我们已经提出了将 Mariaex 与 Ecto 3.0 兼容的拉式请求。 MyXQL 发布时，可以选择 Mariaex 进行开发。 大多数用户都可以顺利迁移到 MyXQL 吧。 尽管如此，还是确保了两个工具可以选择的时间。 最终，对 Mariaex 的支持将从 Ecto 中移除(更准确地说，从`Ecto.SQL`中移除)。

现在代码正在开发中。 对于严重依赖 MySQL 的开发人员，只要浏览[MySQL 资源库](http://github.com/elixir-ecto/myxql)，就可以了解进展情况并参与讨论。