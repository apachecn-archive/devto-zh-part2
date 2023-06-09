# 通过观察频谱，学习如何构建和测试 GraphQL 服务器

> 原文：<https://dev.to/iwilsonq/learn-to-architect-and-test-graphql-servers-by-observing-spectrum-5din>

# 通过观察频谱学习架构和测试 GraphQL 服务器

[![alan King](img/9fac27c99ed6ce2707ab296e8bc539e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--taG948Tp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1501041158226-5842bd1f76ac%3Fixlib%3Drb-0.3.5%26s%3D13803e446342e7d1dd138ad63adc12f2%26dpr%3D1%26auto%3Dformat%26fit%3Dcrop%26w%3D1000%26q%3D80%26cs%3Dtinysrgb) 
照片由艾伦·金在 Unsplash 上拍摄

最近，我一直在寻找更好的方法来构建和测试 JavaScript 应用程序，尤其是那些使用 GraphQL 的应用程序。

假设我有一个用 Node.js 编写的 GraphQL 服务器，我应该如何安排我的文件夹结构？我应该把我的模式和解析器放在哪里？我的类型定义应该和它们各自的解析器放在一起吗？

对于所有不同的查询和变异，测试我的 */graphql* 端点的好方法是什么？

最近， [spectrum.chat](https://spectrum.chat) 开源了他们的整个栈。这意味着你和我可以去他们的回购处研究他们的源代码。我的计划是观察他们如何构建他们的 JavaScript 应用程序，并为我自己的应用程序窃取一些想法。希望我们能够回答我上面提出的一些问题。

通过深入这个开源课堂，您可以像专业人士一样学习如何使用这些技术(无耻地从他们的自述文件中窃取):

*   RethinkDB:数据存储
*   Redis:后台作业和缓存
*   GraphQL: API，由整个 Apollo 工具链提供支持
*   Flowtype:类型安全的 JavaScript
*   PassportJS:验证
*   React:前端和移动应用
*   博览会:移动应用(React Native)
*   DraftJS:网上所见即所得的写作体验

今天，我们将从了解他们布局 GraphQL API 的方式开始。

# GraphQL 文件夹结构

我们首先要看的是 Spectrum 的文件夹结构是如何工作的。

```
server/
├── loaders
├── migrations
├── models
├── mutations
├── queries
├── routes
├── subscriptions
├── test
├── types
│   └── scalars.js
├── README.md
├── index.js       # Runs the actual servers
└── schema.js 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们注意到已经有文档描述了应用程序的每个部分在处理什么。在那里，你还可以了解到所有后端服务的奇怪的希腊命名惯例。

*   *加载器*为每个频谱资源实现[脸书的数据加载器](https://github.com/facebook/dataloader)，以便批量&缓存。优化的东西，但我们才刚刚开始，所以让我们不要担心它。

*   *迁移*允许开发人员播种数据以测试应用程序。它包含一堆静态默认数据，但它也使用 [faker](https://github.com/Marak/faker.js) 库，允许你伪造一大堆数据，如用户、渠道和消息线程。

*   *模型*描述 API 如何与数据库接口；对于每个资源(用户、频道等)，都存在一组函数，可用于查询或改变数据库中数据。

*   *查询*保存解析器函数，描述如何获取数据、哪些项目、字段以及如何对它们进行分页。

*   *突变*保存解析器函数，描述如何创建新数据、删除或更新现有数据。

解析器是一种简洁的方式来描述调用适当的服务来获取客户端所需数据的函数。例如，考虑这个查询:

```
query  GetChannelsByUser  {  user(id:  "some-user-id")  {  channels  {  members  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这个特定的查询通过 ID 获取单个用户，同时还获取他们所属的所有通道以及这些通道的成员。要弄清楚如何做到这一点，这就是解析器功能的作用。

在这种情况下，有 3 个解析器函数:一个获取用户，一个获取该用户的通道，另一个获取每个获取的通道的所有成员。最后一个解析器功能甚至可能为每个通道运行 n 次。

您可能会注意到这个查询会变得非常繁重。如果多个渠道有几千个会员呢？这就是装载机派上用场的地方。但是我们今天不去那里。

*   *订阅*允许服务器使用 WebSocket 服务器将消息和通知推送到移动或 web 客户端上的用户。

*   *Test* 包含对查询和变异本身的测试，通过对实际数据库尝试查询来进行。我们稍后会讨论几个问题。

*   *类型*是指 GraphQL 模式类型，可以查询的字段以及它们之间的关系。当服务器启动时，通过将类型合并在一起来创建模式。

*   *Routes* 包含路由处理器和中间件，用于更传统的 RESTful webhooks。例子包括松弛集成和电子邮件退订。

与这些文件夹在同一层的是`schema.js`文件，它将所有的类型定义和解析器合并到一个可用的 GraphQL 模式中。

最后是`index.js`,它启动了我们的后端 API 以及用于处理订阅的 WebSocket 服务器。我对最后一个文件不感兴趣；我已经知道如何用中间件设置 Node.js 服务器。

# 图式优先发展

根据脸书的观点，在开始任何业务逻辑之前，你应该先构建好你的模式。如果您的模式做得很好，您可以更有信心执行您的业务逻辑。

## 扩展根类型

让我们看一下根 schema.js 文件，其中所有的查询、突变和类型定义都被导入到项目中。我要注意根查询的形状:

```
type  Query  {  dummy:  String  }  type  Mutation  {  dummy:  String  }  type  Subscription  {  dummy:  String  }  schema  {  query:  Query  mutation:  Mutation  subscription:  Subscription  } 
```

Enter fullscreen mode Exit fullscreen mode

在项目所有者的评论中，他们只是在定义类型时扩展了根查询！这太神奇了，因为在我看到这个项目之前，我一直在做这样的事情:

```
type  Query  {  contents(offset:  Int  =  0,  limit:  Int  =  10):  [Content]  tags(offset:  Int  =  0,  limit:  Int  =  10):  [Tag]  users(offset:  Int  =  0,  limit:  Int  =  20,  field:  String):  [User]  # And many more queries...  }  type  Mutation  {  createContent(text:  String):  Content  updateContent(id:  ID!,  text:  String):  Content  deleteContent(id:  ID!):  Content  createUser(username:  String!):  User  updateUser(id:  ID!,  username:  String!):  User  # I don't want to write all of these here...  } 
```

Enter fullscreen mode Exit fullscreen mode

尽管我很喜欢意大利面条，但这种模式在大型应用程序中肯定会失控。这就是 Spectrum 如何扩展他们的查询，你也可以从阅读文档到最后学到这一点。

```
extend  type  Query  {  channel(id:  ID,  channelSlug:  String,  communitySlug:  String):  Channel  @cost(complexity:  1)  }  extend  type  Mutation  {  createChannel(input:  CreateChannelInput!):  Channel  editChannel(input:  EditChannelInput!):  Channel  deleteChannel(channelId:  ID!):  Boolean  # ...more Channel mutations  } 
```

Enter fullscreen mode Exit fullscreen mode

## 定义输入类型

关于上述要点，你可能注意到的另一点是，它们的输入类型没有列出它们需要的每一个字段(像我上面那样😮).

相反，他们为每个不同的突变创建特定的类型，这些类型需要更多的参数，而不仅仅是 ID。这些类型在 GraphQL 模式中被定义为输入类型。

```
input  CreateChannelInput  {  name:  String!  slug:  String!  description:  String  communityId:  ID!  isPrivate:  Boolean  isDefault:  Boolean  }  input  EditChannelInput  {  name:  String  slug:  String  description:  String  isPrivate:  Boolean  channelId:  ID!  } 
```

Enter fullscreen mode Exit fullscreen mode

果不其然，如果我真的阅读了所有的文件，我可能会看到这个。当我在写 GraphQL APIs 时，我觉得有些部分很有趣，“为什么我必须在这里写所有这些输入字段！”，我想。

> 当你用蛮力做一件事很多次，然后找到正确的方法去做的时候，这对你真的有帮助。

这适用于软件开发领域以及其他领域的许多事情。这就像当你发现你的乒乓球击球一直是错误的，尽管它为你赢得了几场比赛。嗯，我的划水还是不对，但至少我意识到了。😅

## 连接和边缘

构建良好的 GraphQL APIs 倾向于为其数据集中的项目提供一种接口，这种接口在获取数据时有助于游标或分页。例如，假设我们想要抓取特定频道中的所有成员:

```
type  Channel  {  id:  ID!  createdAt:  Date!  modifiedAt:  Date  name:  String!  description:  String!  slug:  String!  memberConnection(first:  Int  =  10,  after:  String):  ChannelMembersConnection!  @cost(complexity:  1,  multiplier:  "first")  memberCount:  Int!  # other fields omitted for brevity  } 
```

Enter fullscreen mode Exit fullscreen mode

通过指定成员类型是一个*连接*，API 的消费者将知道他们正在处理一个定制的非原始类型，一个符合他们的游标工作方式的类型。

在 spectrum API 中，他们使用参数`first`和`after`来处理光标移动。

*   `first`只是一个数字，告诉查询要获取多少项；一些 API 对此有限制。
*   `after`是一个作为偏移量的字符串，也就是说，如果我指定了一个字符串“some-item-id”，那么它将获取该项目之后的第一个 *n 个*项目。基本上，除了在 Spectrum API 中，他们实际上用 base64 编码。

`ChannelMembersConnection`型是这样的:

```
type  ChannelMembersConnection  {  pageInfo:  PageInfo!  edges:  [ChannelMemberEdge!]  }  type  ChannelMemberEdge  {  cursor:  String!  node:  User!  } 
```

Enter fullscreen mode Exit fullscreen mode

当我们在 GraphQL 中定义的一个类型引用另一个自定义类型时，就像我们的`Channel`引用一个成员(它只是一个`User`)一样，我们可以像这样定义类型，以便与其他类型一起工作。我们可能关心的数据在 edge 的`node`字段中，edge 只是一个被获取的项目的花哨术语。

连接的`pageInfo`带回一些元数据，关于集合中是否有下一页或上一页。现在让我们看看这个 membersConnection 的运行情况。

## 示例查询:成员连接

```
export default (
  { id }: DBChannel,
  { first, after }: PaginationOptions,
  { loaders }: GraphQLContext
) => {
  const cursor = decode(after);

  const lastDigits = cursor.match(/-(\d+)$/);
  const lastUserIndex = lastDigits && lastDigits.length > 0 && parseInt(lastDigits[1], 10);

  return getMembersInChannel(id, { first, after: lastUserIndex })
    .then(users => loaders.user.loadMany(users))
    .then(result => ({
      pageInfo: {
        hasNextPage: result && result.length >= first,
      },
      edges: result.filter(Boolean).map((user, index) => ({
        cursor: encode(`${user.id}-${lastUserIndex + index + 1}`),
        node: user,
      })),
    }));
}; 
```

Enter fullscreen mode Exit fullscreen mode

当我们发送一个查询来获取一个`Channel`并请求`membersConnection`时，服务器将执行这个解析器函数。

你会注意到它在顶部的函数参数中有一些奇怪的语法。无需惊慌；他们使用[流型](https://flow.org)。

该函数首先通过对 after 参数进行编码来创建一个游标，然后在编码后的字符串中搜索最后一位数字。它使用这些数字来计算何时开始查询。

然后，它从处理与数据库交互的层中调用一个函数。当执行数据库查询时，这个函数获取结果并构建我们前面提到的`pageInfo`和`edges`。

也可以一窥光标是如何编码的；这些边用项目的 id 和它们在查询结果中出现的索引组成一个字符串。这样，当光标被解码时，它将知道它正在查看的类型和索引。

# 测试 GraphQL 查询

最近我一直在想，我应该如何测试我的 GraphQL 服务器？我应该只对解析器功能进行单元测试吗？看看 Spectrum，他们实际上通过直接调用测试数据库来测试他们的查询。根据他们团队的说法，当单元测试套件运行时，

> 在运行测试之前，这将在本地建立一个名为“testing”的 RethinkDB 数据库。它将在其上运行迁移，然后插入一些虚拟数据。这很重要，因为我们针对真实的数据库测试我们的 GraphQL API，我们不模仿任何东西，以确保一切都 100%工作。

在他们这样做之后，他们可以利用一个请求实用程序函数作为路由处理程序，否则就会命中 API 的`/graphql`路由。

```
// @flow
import { graphql } from 'graphql';
import createLoaders from '../loaders';

import schema from '../schema';

type Options = {
  context?: {
    user?: ?Object,
  },
  variables?: ?Object,
};

// Nice little helper function for tests
export const request = (query: mixed, { context, variables }: Options = {}) =>
  graphql(
    schema,
    query,
    undefined,
    { loaders: createLoaders(), ...context },
    variables
); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个工具，我们现在可以对我们的服务器执行自动化测试查询。这里有一个示例查询，可以测试我们之前检查的`membersConnection`查询。

```
import { request } from '../../utils';
import { SPECTRUM_GENERAL_CHANNEL_ID } from '../../../migrations/seed/default/constants';

it('should fetch a channels member connection', async () => {
  const query = /* GraphQL */ `
    {
      channel(id: "${SPECTRUM_GENERAL_CHANNEL_ID}") {
        id
        memberConnection(after: null) {
          pageInfo {
            hasNextPage
            hasPreviousPage
          }
          edges {
            cursor
            node {
              id
              name
              contextPermissions {
                communityId
                reputation
              }
            }
          }
        }
      }
    }
  `;

  expect.assertions(1);
  const result = await request(query);

  expect(result).toMatchSnapshot();
}); 
```

Enter fullscreen mode Exit fullscreen mode

假设它们的测试数据在两次执行之间是相同的，我们实际上可以在这里利用快照！我认为这是一个非常好的用例；给定一些默认数据集，您总是希望查询返回特定形式的数据。

如果与该查询相关的某个解析器函数发生了变化，Jest 会提醒我们注意快照中的差异。

多棒啊。

* * *

对我来说差不多了，通过梳理 Spectrum 的 API，我确实学到了很多关于构建更好的 GraphQL 服务器的知识。

有几件事我没有真正涉及，比如订阅、指令或认证。

如果你渴望了解这些主题，也许可以查看以下链接:

*   马克斯·斯托伊伯的《[保护你的 GraphQL API 免受恶意查询](https://dev-blog.apollodata.com/securing-your-graphql-api-from-malicious-queries-16130a324a6b)
*   Jonas Helfer 的《GraphQL 认证指南》
*   Ben Newman 的“可重用的 GraphQL 模式指令”
*   刘小源的《阿波罗客户端中的 GraphQL 订阅》

好奇更多帖子或者妙语连珠？在[媒体](https://medium.com/@iwilsonq)、 [Github](https://github.com/iwilsonq) 和 [Twitter](https://twitter.com/iwilsonq) 上关注我吧！