# GraphQL 作为您的服务层

> 原文：<https://dev.to/emasuriano/graphql-as-your-service-layer-4n99>

如今，每个应用程序都连接到服务器。该服务器可以使用不同的协议( *HTTP* 、 *FTP* 、 *HTTPS* )和设计( *SOAP* 、 *REST* ，类似 REST 的东西)发送数据，我们的应用程序必须处理这些，因此我们总是希望在我们的架构中有一个服务层。

让我们来看一个服务层的常见实现:

```
const myService = await params =\> {
 const requestParams = adaptParamsForRequest(params);
 const response = fetch(MY\_SERVICE\_URL, {
 headers: SERVICE\_HEADERS,
 method: SERVICE\_METHOD,
 body: requestParams,
 ...more
 });

return parseResponse(response); 
```

Enter fullscreen mode Exit fullscreen mode

这种服务层有一些缺点:

*   由于修改和解析数据而导致的性能问题。
*   web 应用程序必须知道 API 使用的协议和设计(HTTP、FTP 等。).
*   为了获得更多信息，我们可能需要执行另一个请求，或者可能不止一个请求。
*   可能会发生这样的情况，我们不需要响应中的所有信息，解析器会完成这项工作，但是如果我们将来不会使用它，我们就不应该首先要求它。

GraphQL 在所有这些方面都会有所帮助！但首先我们需要了解它是什么..

### GraphQ 什么！？🤔

如果我们检查官方文档，我们会发现类似这样的内容:

> GraphQL 是一种 API 查询语言，也是一种用现有数据完成这些查询的运行时语言。GraphQL 为 API 中的数据提供了完整且易于理解的描述，使客户能够准确地要求他们需要的东西，使 API 更容易随时间发展，并支持强大的开发工具。

[![](../Images/f8abcd3dae79962b02124a0fcca2ff37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BiqgcoM9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2AHHEU3G2ojo6uFtYjwE21Ig.gif)

哇，信息量真大！让我们冷静下来，看看他们在谈论什么🙏

#### GraphQL 是一种用于 API 的查询语言

先从维基百科的定义说起。

> *查询语言是指通过发送查询从数据库和信息系统中请求和检索数据的任何计算机编程语言。*

总而言之，GraphQL 是一种允许我们向数据库或其他东西发送数据语言(我们将在接下来的章节中看到“某些东西”是什么意思)。每种语言都有一套定义它的规则，在 GraphQL 中这被称为 Schema。

#### 图式🧠

模式是服务器和客户机之间的契约，它指定 API 的功能，并定义客户机如何与数据交互。编写模式的语法称为[模式定义语言(SDL)](https://blog.graph.cool/graphql-sdl-schema-definition-language-6755bcb9ce51) 。

基本上，SDL 有两个主要组成部分:

*   类型:有一个名称，可以扩展一个或多个接口。

```
type Post implements Item {
 # ...
} 
```

Enter fullscreen mode Exit fullscreen mode

*   字段:有名称和类型。

```
age: Int 
```

Enter fullscreen mode Exit fullscreen mode

如果我们合并这两个概念，我们可以声明我们的第一个 ObjectType！

```
type Car {
 patent: String!
 color: String!
} 
```

Enter fullscreen mode Exit fullscreen mode

汽车类型在我们的应用程序中使用 [ScalarTypes](https://graphql.org/learn/schema/#scalar-types) 定义了汽车模型的结构，它应该有一个专利，颜色属性必须是 string 和 mandatory。

需要注意的一点是，ObjectType 或 ScalarTypes 不向客户端应用程序公开任何功能，为此我们应该为我们的服务器定义我们的*入口点*。

#### 查询类型🔍

客户端使用查询向服务器请求所需的数据。与 REST APIss 不同，在 REST API 中，从每个端点返回的信息都有一个明确定义的结构，GraphQL 总是只公开一个端点，允许客户端决定它真正需要什么数据！

假设我们有一个查询，它返回所有的汽车及其各自的信息，但是客户只希望检索它们的专利，其他什么都不想。因此，我们可以执行以下查询:

```
{
 allCars {
 patent
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将返回当前存储在我的服务器上的所有汽车的列表，其形状如下:

```
{
 "data": {
 "allCars": [
 {
 "patent": "ABC 123"
 },
 {
 "patent": "BQK 893"
 },
 {
 "patent": "POI 098"
 }
 ]
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 突变型⛓

突变允许客户端对存储在服务器内部的数据进行更改。这些变化可能是:

*   创建新数据
*   更新现有数据
*   删除现有数据

突变的语法看起来几乎和查询一样，但是它们必须以突变关键字开始。

```
mutation {
 createCar(patent: “QWE 112”, color: “red”) {
 patent
 color
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 订阅类型📩

订阅为客户端提供了与服务器建立实时连接的可能性，以便在重要事件发生后获得通知。每当特定事件发生时，服务器将相应的数据推送到客户端。

它们使用与查询相同的语法编写，但以 subscription 关键字开头。

```
subscription {
 createCar {
 patent
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

### GraphQL 是一个运行时，用于使用现有数据完成这些查询

GraphQL 本身不提供任何信息或数据，它将接收来自客户端的查询或变异，并通过与其实体的通信来解决它。它能够与许多不同类型的实体进行通信，这些实体可以是 SQL 或 NoSQL 数据库、REST APIs、第三方 API、遗留系统甚至其他 GraphQL APIs。

如果我们将一个本地数据库与两个外部服务结合起来，我们最终会得到下面的架构。

<figure>[![](../Images/db79d78e5bc5c98ce13976a3cd8d6a8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_7rxPVhE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/742/1%2AMwvNPnnT92MqL2WmY9jUnQ.jpeg) 

<figcaption>GraphQL 架构</figcaption>

</figure>

但是，如果所有的服务都不一样，我们如何从这些服务中检索信息呢？在上面的句子中使用 resolve 这个词并不是偶然的。我来介绍一下解析器！

#### 解析器📡

众所周知，查询/变异/订阅由一组字段组成。在 GraphQL 服务器实现中，这些字段中的每一个实际上都对应于一个称为解析器的函数。

解析函数的唯一目的是获取其字段的数据。由于每个领域都有它自己的 reducer，我们可以很容易地组合不同服务的响应。

```
const CarResolver = {
 patent: async ({ id }) =\> {
 const patent = await getPatentFromDb(id);
 return patent;
 },
 owner: async ({ id }) =\> {
 const owner = await fetch(getOwnerInformatioById(id));
 return owner;
 }
}; 
```

Enter fullscreen mode Exit fullscreen mode

一旦所有解析器返回，服务器将以查询描述的格式收集所有数据，并将其发送回客户端。

### GraphQL 工具🔧

GraphQL 于 2015 年发布，有许多工具可以帮助您构建自己的服务器，但只有一个工具是您必须拥有的。它的名字叫 [GraphiQL](https://github.com/graphql/graphiql) 。

GraphQL 通过提供一个便于执行查询和变异的图形界面，允许其用户测试和呈现 GraphQL APIs。它使用模式来提供关于数据结构和类型的信息，并且它还支持自动完成。

我喜欢把它看作是邮递员和大摇大摆之间的融合😂

<figure>[![](../Images/0e4882fd89e96d7f8edc1f3ff7b5079c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JGB4aVgi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AIL7DumVFy5hJdvRdL1OdjA.gif) 

<figcaption>动作中的 GraphiQl</figcaption>

</figure>

### GraphQL 不是框架⛔

我已经解释了 GraphQL 是什么，但我没有提到任何关于框架或库的内容。所以让我们看看如何实现 GraphQL！

根据您希望运行 GraphQL 的服务器，您必须为您的特定技术安装一个依赖项。例如，如果您运行的是 express 后端，那么您必须安装 express-graphql。happy、koa 等也是如此。

我真的想提一下，有一家名为 Apollo 的大公司在 GraphQL 上下了很大的赌注。他们已经构建了大量有用的库来启动和运行您自己的 GraphQL 服务器，并将其连接到您的客户端。请[查看它们](https://www.apollographql.com/)！

### 用例💪

这太棒了，这听起来像是一项优秀的技术，那么为什么不是每个人都在构建 GraphQL 服务器呢？🤔

简单的回答是因为也许大多数情况下我们并不需要它，也许构建一个 REST 服务器就足以构建一个质量很好的软件。我认为 GraphQL 在这些情况下是必不可少的:

*   **与多个服务的连接:**它将毫无问题地与不同的服务协作，类似于一个门面。
*   **包装来自服务器的响应:**可能会发生这样的情况:您必须与一个端点通信，而它的响应格式不正确。
*   **不同的客户端平台:**当你在几个平台上工作时，显示不同的信息是很常见的，所以只要在查询中指定它们就足够了(这是 SDL 让我高兴的地方)。

我确信还会有更多的案例，但是在我看来这些是最重要的，所以为了缩短列表，我只选择了这三个。

### 我的经历💡

如果我没有写下我使用它的经验，这就不是一篇完整的文章！考虑到上面描述的用例，我在第二种情况下:我需要建立一个应用程序来获取侏儒，在列表中显示他们，当点击时查看其中一个的信息。

我将这个项目命名为 [brastlewark-finder](https://github.com/EmaSuriano/brastlewark-finder) 。我将从头开始解释将 GraphQL 添加到项目中的整个过程。

<figure>[![](../Images/010eed296aa67b55d3051f1a357f1091.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0jvs28lr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1020/1%2Aep3nRcvC3JnSNNv1u2e-Xg.gif)

<figcaption>brastle wark-finder 示例</figcaption>

</figure>

### 上下文

让我们看看他们给我的端点。它返回包含 1336 项的列表，结构如下。

```
{
 "Brastlewark": [
 {
 "id": 0,
 "name": "Tobus Quickwhistle",
 "thumbnail":
 "[http://www.publicdomainpictures.net/pictures/10000/nahled/thinking-monkey-11282237747K8xB.jpg](http://www.publicdomainpictures.net/pictures/10000/nahled/thinking-monkey-11282237747K8xB.jpg)",
 "age": 306,
 "weight": 39.065952,
 "height": 107.75835,
 "hair\_color": "Pink",
 "professions": [
 "Metalworker",
 "Woodcarver",
 "Stonecarver",
 " Tinker",
 "Tailor",
 "Potter"
 ],
 "friends": ["Cogwitz Chillwidget", "Tinadette Chillbuster"]
 },
 {
 "id": 1
 }
 ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我注意到的第一件事是，如果不按名称从响应中过滤整个数组，我就无法获得朋友的信息。使用旧风格提取的可能实现可以是:

```
const getGnomes = () =\> fetch('gnomeURL'); //will return the whole list of gnomes

const getGnomeById = (id, loadFriends = true) =\> {
 const gnome = getGnomes().then(gnomes =\> {
 const result = gnomes.filter(gnome =\> gnome.id === id);
 if (loadFriends) {
 const friendsId = gnomes
 .filter(({ name }) =\> result.friends.includes(name))
 .map(gnome =\> gnome.id);
 result.friends = Promise.all(
 friendsId.map(id =\> getGnomeById(id, false))
 );
 }
 return result;
 });
}; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这将导致严重的性能问题和非常糟糕的 UX。可能还有一些可以改进的地方，但我看到这是 GraphQL 的完美匹配。现在，让我们看看相同的结果，但是在本例中，使用来自 GraphQL 的查询！

```
export const GET\_GNOME\_BY\_ID = gql`
 query getGnomeById($id: ID!) {
 gnome(id: $id) {
 name
 thumbnail
 age
 weight
 height
 hair\_color
 professions
 friends {
 id
 name
 thumbnail
 professions
 }
 }
 }
`; 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![](../Images/d296402d73b815365bdaa15eee3ba884.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iGRSY1CX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/275/1%2AljTFIMmjzU5_IxkLssllAA.gif)

<figcaption>magic CCC</figcaption>

</figure>

### 实现👷

如我之前所说，您必须决定使用哪种实现来启动和运行您的服务器。我决定使用 Zeit 的 [Micro](https://github.com/zeit/micro) 和 [Apollo server](https://github.com/apollographql/apollo-server) ，因为他们有非常好的解释例子。

您可以在这里通过 GraphiQL [试用 GraphiQL 服务器的最终版本！](https://brastlewark-finder-sotqubsuzd.now.sh/graphiql)

#### 服务器定义

我们服务器的入口是 Micro 的实例化，并为我们的 GraphQL 服务器添加路由。这主要是你会在阿波罗的例子中发现的。

```
import { microGraphiql, microGraphql } from 'apollo-server-micro';
import cors from 'micro-cors';
import micro, { send } from 'micro';
import { get, post, router } from 'microrouter';
import schema from './schema';

const graphqlHandler = microGraphql({ schema });
const graphiqlHandler = microGraphiql({ endpointURL: '/graphql' });

const corsUpdater = cors();

const server = micro(
 corsUpdater(
 router(
 get('/graphql', graphqlHandler),
 post('/graphql', graphqlHandler),
 get('/graphiql', graphiqlHandler),
 (req, res) =\> send(res, 404, 'not found')
 )
 )
);

server.listen(3001, () =\> {
 console.log(`Go to [http://localhost](http://localhost/):${3001}/graphiql to run queries!`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### GraphQL 模式

正如我们所知，模式是我们为 GraphQL 服务器定义结构的地方。当我们调用 makeExecutableSchema 时，我们应该发送所有的类型定义(QueryType、ObjectType、MutationType 等。)以及它们各自的解析器。

在我定义的 typeDefs 中:

*   gnome:这是代表服务器内部 Gnome 实体的 ObjectType，它存储了一个 Gnome 的所有相关信息，并将作为对象发送到客户端。
*   查询:
*   allgnomes:接收过滤 Gnomes 的标准(名字和一组职业)并返回一组 Gnomes。
*   gnome:接收一个 id(强制字段)并返回带有该 Id 的 Gnome。

当服务返回的对象的关键字匹配时，Gnome ObjectType 中的每个字段都会被自动解析，除了朋友！如果你看一下解析器的内部，你会发现 Gnome 重新定义了获取 Gnome 好友的功能，这非常有用，因为我们可以用一种非常简单的方式修改来自服务器的数据😃

```
import { makeExecutableSchema } from 'graphql-tools';
import { getGnomes, getGnomeById } from './query';

const typeDefs = `
 type Query { allGnomes(name: String, professions: [String]): [Gnome], gnome(id: ID!): Gnome }
 type Gnome {
 id: ID!,
 name: String,
 thumbnail: String,
 age: Int,
 weight: Float,
 height: Float,
 hair\_color: String,
 professions: [String],
 friends: [Gnome],
 createdAt: Int,
 }
`;

const resolvers = {
 Query: { allGnomes: getGnomes, gnome: getGnomeById },
 Gnome: {
 friends: async ({ friends }) =\> {
 const gnomes = await getGnomes();
 return gnomes.filter(({ name }) =\> friends.includes(name));
 }
 }
};

export default makeExecutableSchema({
 typeDefs,
 resolvers
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 查询

这是我们从 API non-REST 获取数据的地方，也应用了按姓名和/或职业过滤的逻辑。我使用 memoize 只是为了避免多次获取相同的资源，因为它将总是返回相同的数据…

```
import fetch from 'node-fetch';
import memoize from 'fast-memoize';
import BASE\_URL from './constants';

const fetchGnomes = memoize(async () =\> {
 const rawData = await fetch(BASE\_URL);
 const jsonData = await rawData.json();
 return jsonData.Brastlewark;
});

const getGnomes = async (\_, args) =\> {
 const gnomes = await fetchGnomes();
 if (!args) return gnomes;

const { name = '', professions = [] } = args;
 return gnomes.filter(
 gnome =\>
 (!name || new RegExp(name, 'i').test(gnome.name)) &&
 (!professions.length ||
 professions.every(prof =\> gnome.professions.includes(prof)))
 );
};

const getGnomeById = async (\_, { id }) =\> {
 const gnomes = await fetchGnomes();
 return gnomes.find(gnome =\> gnome.id == id);
};

export { getGnomes, getGnomeById }; 
```

Enter fullscreen mode Exit fullscreen mode

我真的很喜欢使用 GraphQL 和使用 Apollo 实现，当你到了所有东西都连接起来的时候，开发就棒极了！🤩

### 相关链接

*   [graph QL 简介](http://graphql.org/learn/)
*   [graph QL 的全栈教程](https://www.howtographql.com/)
*   [可视化的 GraphQL 概念](https://dev-blog.apollodata.com/the-concepts-of-graphql-bc68bd819be3)
*   [GraphQL Server Basics](https://blog.graph.cool/graphql-server-basics-the-schema-ac5e2950214e)
*   [如何用 GraphQL 包装 REST API](https://blog.graph.cool/how-to-wrap-a-rest-api-with-graphql-8bf3fb17547d)
*   [使用 GraphQL 的五大理由](https://blog.graph.cool/top-5-reasons-to-use-graphql-b60cfa683511)