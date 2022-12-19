# ASTs 如何增强 GraphQL 模式处理

> 原文：<https://dev.to/contentful_blog/how-asts-power-the-graphql-schema-handling-3hi9>

[![How abstract syntax trees come into play with schemas and GraphQL](../Images/23b147ff159fc0a5006cc3ada620f9be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5HBAXE_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/fo9twyrwpveg/w7OPcsNYiWquQY8YuUAq8/5ce2c0125423aff8927f85654da34bee/5-Jul-GraphQL-AST.png)

在 Contentful，我们目前正在努力开发新的 GraphQL API，它已经在 alpha 阶段可用🎉。当您今天阅读 GraphQL 时，您会很快发现它提供的主要优点，普通 GraphQL API 用户可以利用这些优点:

*   你可以查询你想要的并保存请求！
*   您正在处理一个强类型模式！
*   它为您提供了一个丰富的、不断发展的生态系统！

但是实现 GraphQL API 是另一回事。对于实施者，您很可能会遇到以下建议:

> "将您的数据库模式一对一地映射到您的类型."

然而，在我们这边，这并不容易，因为我们的内容基础设施让用户自由定义他们内容的结构。这意味着我们可以为一个特定的用户提供一个非常扁平的数据输入结构，同时向另一个用户提供几个层次的完整内容树。这种灵活性意味着我们可以处理所有类型的数据结构，这使得对 GraphQL 的支持更加棘手，因为我们现在必须动态地创建 GraphQL 模式，并基于抽象语法树处理域对象，而不是简单地将数据库模式映射到 GraphQL。如果这听起来很复杂，不要担心——本文将详细介绍所有内容。

作者注:本文基于我的一次 meetup 演讲；这篇文章的最后有一段录音链接。

## graph QL 抽象语法树-曙光模式

任何 GraphQL API 的基础都是所谓的抽象语法树，它在服务器端大量用于处理模式定义和实际 GraphQL 查询的解析。

### 但是什么是抽象语法树呢？

对我来说，抽象语法树(AST)这个词只是描述深度嵌套对象的一种奇特方式，这些对象保存了一些源代码的所有信息，或者在我们的例子中，GraphQL 查询。

例如，让我们以 Babel 为例，这是一个非常流行的 JavaScript 编译器，它允许您编写尚未得到广泛支持的 JavaScript，并将其转换为较旧的语法。Babel 将你扔给它的所有源代码转换成一棵抽象语法树，然后在这棵树上执行转换。然后，更新和转换后的树被用来生成源代码，这些源代码不仅可以在最新和最棒的浏览器中运行，还可以在一段时间没有更新的浏览器中运行。

### 抽象语法树包括哪些内容？

检查抽象语法树的一个很好的工具是 [AST Explorer](http://astexplorer.net) 。该网站允许您快速地将代码从 JavaScript 粘贴到 PHP，再到 TypeScript，甚至 GraphQL 查询，然后提供最终的抽象语法树。

当我们看下面的 GraphQL 查询时…

```
{
  course(id: "1toEOumnkEksWakieoeC6M") {
    fields {
      title
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

...最终的抽象语法树(不用太担心)看起来像这样:

```
{
  "kind": "Document",
  "definitions": [
    {
      "kind": "OperationDefinition",
      "operation": "query",
      "name": null,
      "variableDefinitions": null,
      "directives": [],
      "selectionSet": {
        "kind": "SelectionSet",
        "selections": [
          {
            "kind": "Field",
            "alias": null,
            "name": {
              "kind": "Name",
              "value": "course",
              "loc": {
                "start": 4,
                "end": 10
              }
            },
            "arguments": [
              {
                "kind": "Argument",
                "name": {
                  "kind": "Name",
                  "value": "id",
                  "loc": {
                    "start": 11,
                    "end": 13
                  }
                },
                "value": {
                  "kind": "StringValue",
                  "value": "1toEOumnkEksWakieoeC6M",
                  "loc": {
                    "start": 15,
                    "end": 39
                  }
                },
                "loc": {
                  "start": 11,
                  "end": 39
                }
              }
            ],
            "directives": []
            ...
            ...
            ...
          }
        ],
        "loc": {
          "start": 0,
          "end": 79
        }
      },
      "loc": {
        "start": 0,
        "end": 79
      }
    }
  ],
  "loc": {
    "start": 0,
    "end": 79
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

AST 包括许多元数据，比如源中的位置，或者标识符，比如参数名；多亏了这个深度嵌套的 JSON 对象，我们现在拥有了处理 GraphQL 模式和查询所需的所有能力。在开发自己的 GraphQL 服务器时，所有这些元信息都会派上用场；例如，从那里，我们可以告诉你你的查询中容易引起问题的那一行。

对于模式，这些 POJOs(普通的旧 JSON 对象)通常被翻译成所谓的域对象。它们封装了 AST 中包含的信息，但是用方法进行了丰富，并且是 GraphQL 基本类型的适当实例。例如，每个有字段可供选择的类型都将被创建为 GraphQLObjectType 实例。现在你可以在它上面定义一个函数来获取数据。

假设您的 API 将笛卡尔坐标和地理值中的位置数据作为“位置”提供给您。对于 GraphQL `Location`类型，您总是希望返回地理坐标，因此您定义了如下的`makeLocationFieldResolver`:

```
const resolverRoot = {
  cartesian: {},
  geographic: {
    latitude: 52.501817,
    longitude: 13.411247
  }
}

function makeLocationFieldResolver (field) {
  return (root) => root.geographic[field]
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们的类型定义在系统定义语言(SDL)格式中可用，我们可以从它构造 AST，并通过使用以函数作为其最叶值的嵌套对象将解析器分配给字段:

```
// graphql-tools

const typeDefs = `
  type Location {
    lat: Float!
    lon: Float!
  }
`

const resolvers = {
  Location: {
    lat: makeLocationFieldResolver('latitude'),
    lon: makeLocationFieldResolver('longitude')
  }
}

const executableSchema = makeExecutableSchema({
  typeDefs,
  resolvers,
}); 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果我们手头没有一个可以解析的系统定义语言(SDL ),那么在 Contentful 上肯定会有一点不同。因此，我们所做的只是根据从数据库中获得的内容模型，“手动”创建那些域对象。

```
// graphql-js

const locationType = new GraphQLObjectType({
  name: 'Location',
  fields: {
    lat: { type: GraphQLFloat, resolve: makeLocationFieldResolver('latitude') },
    lon: { type: GraphQLFloat, resolve: makeLocationFieldResolver('longitude') }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

“我的错误的行号是什么？😱“我听到你在问。幸运的是，我们只需要在模式生成时这样做——我们可以充分利用通常的 GraphQL 流来处理您发送给我们的查询文档，从您发送给我们的字符串一直到响应 JSON。

### graph QL-type 系统定义语言和查询文档的两面

要使 GraphQL 工作，您必须关注两个主要部分:

*   GraphQL API 端点的服务器实现必须以所谓的**类型系统定义语言**提供一个模式，该模式定义了什么数据在该端点可用。
*   在客户端，开发人员可以发出包含一个**查询文档**的请求，该文档定义了响应中应该包含哪些数据。

#### SDL -类型系统定义语言

GraphQL 的优势之一是它基于强类型模式定义。这些类型定义定义了数据应该是什么样子，以及 GraphQL API 实际上允许哪些查询。类型定义如下所示:

```
type AssetFile {
  contentType: String
  fileName: String
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的定义定义了类型`AssetFile`正好有两个字段(`contentType`和`fileName`)，都是类型`String`。这个定义很酷的一点是，现在我们可以在其他类型定义中使用它。

```
type Person {
  image: AssetFile
} 
```

Enter fullscreen mode Exit fullscreen mode

SDL 使得定义完整的数据集成为可能:

*   条目包括什么？
*   条目之间是如何关联的？
*   可以访问什么，在哪里访问？

当您使用像 [GraphiQL](https://github.com/graphql/graphiql) (一个浏览器内 IDE)这样的工具来探索 GraphiQL 端点时，您可能已经注意到，通过打开 docs 部分，您可以很容易地发现 API 端点上可用的数据。docs 部分包括基于在您定义的 SDL 中编写的模式的所有信息。

[![Using a tool like GraphiQL, an in-browser IDE to explore GraphQL endpoints](../Images/adf913b26aa1f87f6ba3c6fd9089a365.png)T2】](//images.ctfassets.net/fo9twyrwpveg/6gPqNGJ2rCseU4ykIUSa4c/a0ae78f44cec9790b25871a87cc641ca/image_0__1_.png)

*旁注:来自 Prisma 的人们也构建了一个名为 GraphQL Playground 的工具，它位于 GraphQL 之上，增加了一些额外的功能和一个“更新”的 UI*

这些 GraphQL 工具的工作方式是它们在启动时发送一个初始请求——所谓的`IntrospectionQuery`,这是一个标准的 GraphQL 请求，使用 POST 并在请求有效负载中包含一个 GraphQL 查询。GraphQL 用户执行的请求可以根据不同查询类型的使用而有所不同。

```
query IntrospectionQuery {
  __schema {
    queryType { name }
    mutationType { name }
    subscriptionType { name }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对这个自省查询的响应提供了提供 API 文档所需的所有模式信息，使自动完成成为可能，并为客户端开发人员提供了所有的指导，以便愉快地查询她感兴趣的任何数据。

#### 客户端事物-查询文档

现在我们已经定义了可用的数据模式，缺少的是包含查询文档的 GraphQL 请求。查询文档是您在本文开头已经看到的实际的 GraphQL 查询。

```
{
  course(id: "1toEOumnkEksWakieoeC6M") {
    fields {
      title
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

查询文档基本上是一个字符串值，它包含在到达 GraphQL 端点的有效负载中。GraphQL 和 GraphQL Playground 工具将帮助您轻松编写第一个查询。

### 查询文档和 SDL 的组合

那么，为什么 ast 对 GraphQL 如此重要呢？

当一个请求到达我们的 GraphQL 端点时，用 SDL 编写的模式和包含在请求负载中的查询文档将被读取并转换成 ASTs。如果解析成功，我们可以确定查询和模式都是有效的；否则，我们可以显示错误，详细说明语法不正确的地方。

然后，我们访问查询文档中的每个字段名，检查模式中是否存在相应的类型定义，以及它们是否兼容——它们的参数数量是否相同，它们的类型是否相同？

如果这些验证通过，我们就可以通过解析查询中请求的资源来响应请求。解析器是一个我们不会在本文中涉及的主题，但是如果你感兴趣，你可以阅读 Prisma 的介绍[“GraphQL 服务器基础:graph QL 模式，TypeDefs &解析器解释”](https://www.prisma.io/blog/graphql-server-basics-the-schema-ac5e2950214e/)—这是一个很好的阅读！

[![Interaction of query documents and the SDL](../Images/991b205b15aea73e0d247cd915cee34c.png)T2】](//images.ctfassets.net/fo9twyrwpveg/2rEkv7xsG0aGKm4oeY62i4/5923d0d1eccb423a7e77fd0f3ea4268a/graphql.jpg)

## 得益于抽象语法树，语言处理变得简单

GraphQL 的强大之处在于它的模式和类型定义将 API 开发推向了一个全新的水平。由于丰富的生态系统、工具和抽象语法树的概念，在 Contentful 开发我们新的 GraphQL 端点很有趣。

此外，这不仅仅是关于开发者的体验，而是关于一整套新的可能性。使用 ASTs，您可以轻松地转换得到的模式定义——例如，这使得模式拼接变得容易。

> [模式拼接](https://dev-blog.apollodata.com/graphql-schema-stitching-8af23354ac37)的思想是，你可以获取两个或更多的 GraphQL 模式，并将它们合并成一个可以从所有这些模式中提取数据的端点。

想一想——使用 GraphQL，我们可以非常容易地将几个 API 组合成一个强大的 API。[将这一点与无服务器技术](https://www.contentful.com/blog/2018/04/05/graphql-and-serverless-where-cloud-computing-is-heading/?utm_campaign=graphql-abstract-syntax-tree-new-schema&utm_medium=referral&utm_source=devto&utm_content=graphql-abstract-syntax-tree-new-schema&utm_term=)和 API 开发的力量结合起来，因为你现在知道这将成为过去。**准备好！**；)

了解有关 GraphQL 和 Contentful 入门的更多信息[。如果你还没有的话，先创建一个](https://www.contentful.com/developers/docs/tutorials/general/graphql/?utm_campaign=graphql-abstract-syntax-tree-new-schema&utm_medium=referral&utm_source=devto&utm_content=graphql-abstract-syntax-tree-new-schema&utm_term=)[免费内容丰富的账户](https://www.contentful.com/sign-up/?utm_campaign=graphql-abstract-syntax-tree-new-schema&utm_medium=referral&utm_source=devto&utm_content=graphql-abstract-syntax-tree-new-schema&utm_term=)，看看我们的内容基础设施如何轻松地与你的代码和静态网站项目一起工作。

附注:Nikolas Burg 也给了[一个关于如何使用 Contentful 和 Prisma 做模式拼接的精彩演讲](https://www.youtube.com/watch?v=w1loiyLD4eY&t=1814s),在我们之前在柏林的 Contentful 会议上。值得一看！

### 谈话的录音

如果你不喜欢阅读，我也在我们的一次内容丰富的用户会议上谈到过这个话题。[点击这里查看](https://www.youtube.com/watch?v=sbOe6fU0SCI)。