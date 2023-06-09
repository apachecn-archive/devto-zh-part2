# 对 CQRS 的解构

> 原文：<https://dev.to/kspeakman/a-deconstruction-of-cqrs-apis-4df0>

封面图片是《瓦解 II》系列的版权所有者 Fabian Oefner。这是我最喜欢的汽车之一，福特 GT40。在 CQRS 的早期，在网上搜索“cqrs”会自动更正为“cars”。旧的 [CQRS 博客](https://cqrs.wordpress.com/)幽默地写着副标题:你是指汽车吗？

我有一个很好的机会来实现一个系统，使用一些我在业余时间研究和玩的策略。快进到今天。我遇到了一些意想不到的问题，并从中吸取了一些教训。这篇文章讨论了一个特定的部分:请求/回复 API。

## 概述

我使用一种叫做命令/查询责任分离(CQRS)的策略。对于那些不熟悉它的人，这里有一个 CQRS 支持的 API 操作的快速总结。

|  | 返回数据 | 进行更改 |
| --- | --- | --- |
| 询问 | ✔️ | -好的 |
| 命令 | -好的 | ✔️ |

为什么使用这种模式？我喜欢它有几个原因。作为 API 的消费者，我从不担心问一个问题会产生意想不到的后果。相反，我确切地知道哪些 API 调用对系统进行了更改。没有任何歧义。这使得 API [很容易推理出](https://softwareengineering.stackexchange.com/questions/351244/easy-to-reason-about-what-does-that-mean)。但是从历史上看，这种模式[演变为](https://cqrs.wordpress.com/documents/cqrs-introduction/)，因为读和写的关注点通常非常不同。试图创建一个统一的接口来完成这两项工作，存在服务于两个主设备的典型问题。随着时间的推移，单一界面会变得越来越混乱。假以时日，它很可能会形成一种货物崇拜。“我们为什么要更新这个字段？我们不使用它。”回应:“我不知道，但是继续做下去，否则会有东西坏掉的。”
📦🛐

所以在本质上，CQRS 只是关注点分离的一个具体应用，也就是良好的组织实践。既然我们已经对模式做了介绍，我将回顾一下我们的一些实现细节和经验教训。

## 消息传递

我认为每个查询或命令都是一个**消息**。这意味着任何客户端系统都可以将它们表示为普通数据(类或结构),而不需要任何方法。然后以诸如 JSON 或 CapnProto 或 w/e 之类的有线格式轻松地传输它们。每个消息还有一个名称——通常只是类/结构名称——在 API 中唯一地标识它。比如`SearchCustomers`(一个查询)或者`DeactivateCourse`(一个命令)。名称用于标识所请求的操作，然后将其与消息解析器和处理函数进行匹配。安全授权可以简单到保留一个允许哪些用户发送哪些消息名称的列表。然后在处理任何用户的消息之前检查该列表。🤘🤘

> 如果您熟悉 [RPC](https://en.wikipedia.org/wiki/Remote_procedure_call) ，您也可以将消息传递视为该模式的超集。消息名称是“过程”,消息内容是过程参数。

## 操作

命令和查询应该如何工作似乎是显而易见的。但是我发现了一些细微的差别。

### 查询

嗯，查询*通常是*就像你所期望的那样。特别是，我们这样处理它们:

*   API 监听`/query/[Query Name]`
*   验证用户是否拥有`[Query Name]`权限
*   反序列化查询消息
*   将查询消息传递给它的处理函数，该函数将:
*   验证查询消息
*   从数据库加载和转换数据
*   序列化并返回数据

我们倾向于创建针对特定页面或回答常见问题的查询。感觉我们在这里遵循一个逆干法则。如果我需要一个页面查询，我可能会使用现有的查询。但前提是我不必更改现有的查询。如果需要更改，这意味着新页面的职责略有不同，尽管它显示的大部分数据是相同的。因此，我将进行新的查询。

### 命令

命令的目的是在系统上执行一些业务操作。在实践中，我们注意到命令是否需要改变一个或多个实体的区别<sup>📌</sup>。由于架构的原因，如何处理多实体变更是很重要的。

> 📌**实体**
> 这里的实体指的是逻辑单位的东西。在高度规范化的表中，一个实体可能包括一个父实体和一对多关系的任何后代。用 DDD 的术语来说，你可以称之为聚合。在事件源中，这是一个事件流。

#### 可扩展性

您*可以*在单个事务中执行多实体更改，以实现全有或全无的语义。这种方法很适合在代码中使用，但是它限制了可伸缩性。要参与一个事务，所有受影响的实体必须位于同一个数据库节点上。如果它们在不同的节点上，那么就会发生分布式事务(如果数据库支持的话)。随着负载的增加，分布式事务会越来越慢。跨实体事务是内部业务应用程序(或任何不可能超出单个数据库节点的应用程序)的有效方法。但是对于公众可用的互联网服务来说，也许不是。

一种更具规模友好性的方法是只使用单实体命令进行更改。当一个用例需要修改多个实体时，使用一个元命令，它本身不做任何修改，而是编排和运行单个实体的命令。我将单实体命令称为“基本命令”，将多实体命令称为“工作流”。

> ⚠️ **这些不是后端工作流**
> 工作流命令是为了方便前端。它们通常由用户可以通过 UI 采取的单个操作组成。但是我们没有让用户转到多个页面，而是呈现一个表单，并将所有需要的数据汇总到一个工作流命令中。这些都是尽力而为且有时间限制的(由于是请求/回复)，所以失败通常只会导致不完整的工作流，用户可以重试或单独修复剩余的项目。这些工作流并不意味着取代后端流程或提供故障案例的可靠处理。

#### 客户端工作流

你*可以*在客户端实现一个工作流——让 UI 编排所有必要的基本命令。然而，我选择将它们放在 API 端的一个主要原因是:清晰性(尤其是安全性)。我将用我们系统中的一个真实例子来说明。我们有一个培训师的角色。不允许此角色创建课程。但是，他们可以记录向员工提供的培训。记录培训用例的一部分可能包括创建选项有限的新课程。通过将记录培训用例作为 API 工作流来执行，它可以被表达为单个粒度权限。"培训师可以记录培训，但不能创建课程."在中，权限 UI 上的一个框被选中，但另一个框没有被选中。

为了在客户端做同样的事情，我们需要添加一个基本命令:Create Trainer Course。然后必须通知管理员用户:“要给某人录制培训的许可，你必须检查`Create Trainer Course`、`Permission X`和`Permission Y`。”因此，像这样的客户端工作流是文档/最终用户培训的负担。我们还可以创建一个伪命令，只是为了获得许可，它映射到必需的基本命令。这反而会增加开发人员保持更新的额外负担。这两种结果我都不喜欢，所以我更喜欢 API 端的工作流。

【2021 年 9 月 11 日更新

对于运行同一类命令的批处理，我们使用了一些客户端工作流。客户端制作一个命令列表。然后一次发送一个或并行发送。然后，当成功响应返回时，将它们标记出来。这也使得客户端仅重试失败的命令变得容易。

缺点是:这种方法“喋喋不休”——对于每个命令，它都需要与服务器进行一次往返通信。与“突发”的服务器端工作流相比，这增加了服务器/网络负载。此外，具有高网络延迟的用户将会看到一次一个的客户端工作流慢如蜗牛。问我怎么知道的。

> 无论您是单独发送命令还是突发发送，服务器都会为每个命令做相同的工作。但是，服务器也使用 cpu 和内存来发送/接收网络请求。所以更多的沟通意味着后端工作可用的资源更少。减少多少取决于服务器硬件支持的卸载类型。

如果您明智地使用客户端工作流，它们会很有意义。

### 指导原则

在实现 CQRS API 时，人们会问一些非常常见的问题。我将列出我所遵循的原则作为标题，然后详细说明这些原则背后的常见问题。

#### 返回错误不同于返回数据。

一个普遍的误解是命令不应该返回任何东西。这可以追溯到 [CQS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation) 模式(CQRS 只是延伸了这一模式)。这种模式应用于特定语言中的对象及其方法。许多语言使用异常作为错误传播策略。一个“命令”方法特别值得注意，因为它返回`void`。因此，命令不返回任何内容的概念就诞生了。但是，它暗示了一个错误将抛出一个异常，这实际上只是一个不同的返回路径。

所以事实是命令确实会返回一些东西。它们返回关于操作本身的元信息(是成功还是失败以及原因)。这与返回*业务数据*非常不同，后者是查询的工作。

#### 命令不做修改成功是可以的。

命令可以进行 0 次或多次更改。换句话说，“做出改变”是命令的目的，而不是要求的结果。因此，一个命令成功运行但没有任何改变是完全有效的。

我们有这样的例子，我们在运行命令之前和之后比较一个实体。如果完全相同，那么我们选择 0 修改，成功返回。

#### 命令处理代码调用查询是可以的。

许多问题的出现是基于这样一种误解，即 CQRS 原则应该适用于 API 的内部和外部。具体来说，有很多关于命令处理代码是否可以运行查询的问题。直觉上，这似乎违反了 CQRS 原则。但是 CQRS 只对 API 的外表面积提出了建议。命令的内部是实现细节，除了“进行更改”之外，它对这些细节没有任何意见。

因此，您可以随意运行查询来获取在命令中做出决策所需的一些信息。不过，有一点需要注意。在更高级的场景中，查询数据可能来自缓存，或者可能落后于“当前”数据。(通常被称为最终一致性。)在这种情况下，您必须考虑查询中的陈旧数据对命令决策的影响。点击查看更多关于[的信息。可能稍微陈旧的数据并不重要，配置数据通常就是这种情况。示例:当用户更改配置数据时，他们期望一些事情在旧配置下发生，但未来的事情将在新配置下发生。他们可能不会注意到或注意到，在他们做出更改后几百毫秒的最终一致性期间，用户在旧配置下的操作中出错了。他们会假设操作是在他们更改之前执行的。](http://codebetter.com/gregyoung/2010/08/12/eventual-consistency-and-set-validation/)

#### 自动递增 ID 不应作为主要 ID。

对不返回数据的命令的一个常见反对意见是:我需要返回自动生成的 ID。自动增量 id 非常方便，但是它们也有明显的缺点。一方面，他们无法扩展，另一方面，他们有安全顾虑。但是让我们暂时忽略它，关注一个常见的使用问题:重试。

##### 场景

用户填写表单来创建一个新实体，然后点击 Submit。请求超时。

##### 自动递增冒险

如果一个自动递增的字段是你唯一的 ID，你的应用程序无法知道请求是否成功。这种情况的补救措施通常依赖于用户的意识和参与。

如果用户只是再次点击 Submit(很有可能)，但是尽管超时，前一个请求还是创建了实体，那么现在有两个相同的实体具有不同的 id。为了正确地清理，用户现在应该搜索重复项并删除多余的实体(可能性极小)。

或者，在超时之后，用户可以搜索他们可能创建的实体。如果他们找不到，回来重新填写表格。根据我的经验，这种情况不太可能发生。如果你增加培训成本，让用户习惯这种思维方式，这种情况也许会发生。

你可以添加重复检查的外部系统，比如保存看到的操作及其结果的记忆。但是有一个更好的方法...

##### 预先生成的 ID 冒险

在用户开始输入任何内容之前，表单加载时就生成了一个 ID(或从服务器请求)。

在用户被告知请求超时后，她只需再次点击 Submit。UI 使用相同的预生成 ID 发送与之前完全相同的请求。在最好的情况下，它会正常成功。在最坏的情况下，API 的反应是:“这个实体已经存在。”如果 UI 能够识别这个特定的错误，它就可以像正常情况一样假装成功了。这种冒险带来了更好的用户体验，并且没有重复的机会。

##### 我们的战略

我们倾向于将 UUIDs 用于所有的标识目的。它们很容易在许多平台上生成。他们无视趋势分析。我们的大多数创建表单无论如何都必须运行一个查询(例如，获取下拉列表数据)，所以我们只是在结果中包含一个新的 UUID。

【2021 年 9 月 11 日更新

对于我们自己消费的内部 API 来说，上面的工作很好。但是随着我们进入外部 API，我们正在考虑一个不同的策略。特别是对于创建新实体的操作(如创建订单)。我们不能相信外部客户端会提供与我们的约束条件相匹配的唯一 ID。或者甚至返回他们在之前的查询中得到的 ID。

相反，我们考虑使用客户提供的 ID 作为参考数据。当一个实体被创建时，我们将为它生成我们自己的 ID。但是客户端的 ID 将被附加到实体上，并被索引以便查找。客户端可以使用自己的 ID 来调用我们的 API。但是我们内部依赖的 ID 仍然符合我们的标准。

另一种情况是客户端没有自己的 ID，而是使用我们的 ID。如果稍加修改，这种方法仍然有效。客户端提供一个请求 ID。一旦操作完成，客户机就可以使用请求 ID 请求关于创建的实体的信息(包括我们的 ID)。

## 结论

命令是变化的守门人。疑问是知识的图书馆。那是 CQRS。我发现这种模式引导我走向正确的方向。也是百搭图案。它不关心您的部署表面是单片的还是微型的。您甚至可以将命令和查询拆分到各自独立的服务中，以便将读取负载与写入负载分开。

但是请记住，这只是一个更大系统中的一部分，而不是适用于所有工作的工具。CQRS 模式在后端系统的边界工作得很好，与客户端应用程序接口。与任何模式一样，它只有在正确的情况下才会有用。

/∞