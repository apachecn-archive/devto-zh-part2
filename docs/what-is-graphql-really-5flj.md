# Graphql 到底是什么？

> 原文：<https://dev.to/paigen11/what-is-graphql-really-5flj>

[![GraphQL logo](img/c1380c7dc4a68c471c118a4c93572b6a.png "GraphQL logo")T2】](///static/4475a2fc4ae5ede0337ec8837f7b1a9d/e85cb/graphql.png)

## 简介

我已经听说 GraphQL 好几个月了，但在很长一段时间里，我真的不明白它是什么。

它是一种新的查询语言吗？一种新型的数据库？其他一些 JavaScript 黑魔法？不，以上都不是。

> GraphQL 是一个新的 API 标准，它为 REST 提供了一个更高效、更强大、更灵活的替代方案。
> 
> [—如何绘制 SQL](https://www.howtographql.com)

* * *

## 是休息的替代，那又怎样？为什么要用 GraphQL 代替？

问得好。

这就是 GraphQL 与传统 REST 端点的不同之处:使用 GraphQL，客户端可以指定*确切地想要什么数据(来自多个源)*，并从单个端点返回。

传统的 REST 端点返回固定的数据结构，通常带有客户端不需要的额外信息，它们需要多个端点(和 HTTP 调用)来获取所有必要的信息。GraphQL 没有。

这里有两个传统的 HTTP 调用的例子，用来查找学生和他们的班级

```
//  Call  1:  HTTP  get  to:  'students/<id>  //  Returns:  {  "student":  {  "id":  1,  "firstName":"Jane",  "lastName":"Smith"  }  }  //  Call  2:  HTTP  get  to:  'students/<id>/classes'  //  Returns:  {  "classes":  [{  "id":  1,  "className":"math"  },  {  "id":  1,  "className":"english"  }]  }  /*  Here  is  the  same  information  as  one  GraphQL  query  */*  //  Query:  query  {  Student(  id:1  ){  firstName  lastName  classes  {  className  }  }  }  //  Returns:  {  "data":  {  "Student":  {  "firstName":"Jane",  "lastName":"Smith",  "classes":  [{  "className":  "math"  },  {  "className":"english"  }]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

> 有一件事怎么强调都不为过，那就是 GraphQL 不是一种用于数据库的查询语言。GraphQL 是用于*API*的查询语言。

这使得它与数据库无关，并且可以在任何使用 API 的环境中使用。

不，与无论何时提到 GraphQL 的事实相反，它似乎是与 React 串联在一起的(因为脸书发明了这两者)，GraphQL 可以用在客户端与 API 通信的任何地方:有 C、Go、Erlang、Java 等的服务器库。

JavaScript 是目前 GraphQL 使用的最流行的语言，它有一些非常优秀的服务器包，协同工作使 GraphQL 很容易在项目中实现。

当 Graphql.js 安装后，它可以直接从命令行运行 Node.js，但当它集成到一个项目中，GraphQL API 服务器作为客户端前端和服务器后端以及数据库之间的一个层时，它才真正闪耀。

我用过的实现是 **[Apollo](https://www.apollographql.com/)** ，它很容易与各种 Node.js HTTP 框架如 Express、KOA、哈比神等集成。但那是另一篇文章。

## 好吧，你激起了我的兴趣。那么 GraphQL 是如何工作的呢？

GraphQL 基于强类型的**模式**工作。

简而言之，您希望 GraphQL 返回给您的所有内容都必须在模式中明确定义(一些开发人员喜欢这一点，而其他人则觉得这让他们定义了两次数据)。

### Schemas:graph QL 如何定义和构造它接收的数据

```
//  ****  Simple  Schema  Example  for  a  Type  Called  Student  ****  type  Student  {  firstName:  String!  //  (the  exclamation  mark  means  it's  required)  lastName:  String!  } 
```

Enter fullscreen mode Exit fullscreen mode

**模式定义语言** (SDL)是用于编写模式的语法，而**查询**和**变异**被组合以从服务器取回所需的信息。由于从单个 GraphQL 端点返回的数据结构是完全灵活的，所以查询必须非常具体。

如果在模式中指定了某些东西，但是没有返回，那么响应将包含该字段的一个`null`值，但是它不会像 REST 端点有时发生的那样抛出异常。但是，如果一个特定的字段没有被指定，但却是必需的，那么在模式被更新为包含该字段之前，客户端不会收到该字段。

### 查询:GraphQL 如何请求数据

这里有一个基本的查询，向服务器询问所有学生的情况

```
//  ****  Basic  Query  Asking  the  Server  for  All  Students  ****  {  allStudents  {  firstName  lastName  }  }  //  Returns:  {"allStudents":  [{  "firstName":"Jane",  "lastName":"Smith"  },  {  "firstName":"John",  "lastName":"Doe"  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

### 突变:GraphQL 如何操纵数据

除了查询，GraphQL 还使用一种叫做**突变**的东西来操作存储在后端的数据。通常有三种突变:

*   创建新数据
*   更新现有数据
*   删除现有数据

这种突变在数据库中创造出一个新的学生

```
//  ****  A  Create  Mutation  to  Make  a  New  Student  ****  mutation  {  createStudent(firstName:  "Robert",  lastName:  "Johnson")  {  firstName  lastName  }  }  //  The  server  response  would  look  like  this:  "createStudent":  {  "firstName":"Robert",  "lastName":"Johnson"  } 
```

Enter fullscreen mode Exit fullscreen mode

在读取数据的查询和操纵数据的突变之间，传统 REST 端点提供的所有 CRUD 功能都由 GraphQL 处理。

无论您实现 GraphQL 服务器的方式如何(连接到单个数据库、通过多个第三方遗留系统或作为两者的混合)，当查询到达 GraphQL 服务器时，它**通过读取有效负载和获取数据来解析**查询。然后，它使用模式以正确的格式将数据返回给客户端。

这种清晰定义的模式结构有助于 GraphQL 成为传输层不可知的(TCP、Websockets 等)。—都不错)和数据库不可知(MySQL、MongoDB、Oracle 等。).

类似地，如果 GraphQL 是在多个系统上实现的(在拥有大量分散的遗留系统和 API 的大型公司中很可能是这种情况)，它可以从所有不同的端点整齐地提取请求的信息，并将其解析为一个清晰的响应，由模式进行布局。

### 解析器:GraphQL 如何为其查询(或变异)字段获取数据

组成 GraphQL 查询函数的每个字段都被称为一个**解析器**，解析器的唯一目的是获取其字段的数据。

```
//  ****  Sample  Query  and  the  Resolvers  Corresponding  to  Each  Field  ****  query  {  Student(id:1){  firstName  lastName  }  }  //  Resolvers:  Student(id:  String!):  Student  firstName:  (student:  Student!)  String  lastName:  (student:  Student!)  String 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## GraphQL 客户端库

GraphQL 对于前端开发人员来说是一个巨大的进步，因为不再需要通过以下方式强制获取数据:

1.  构建并发送一个 HTTP 请求(JavaScript 中的`fetch`)，
2.  接收并解析响应，
3.  存储数据(本地或持久存储)，
4.  最后在 UI 中显示数据，

客户端可以通过以下方式声明性地获取数据:

1.  描述数据要求，
2.  在 UI 中显示返回的数据

复杂性和数据存储被推到应用程序的服务器端，这更适合于处理繁重的计算工作，前端可以自由地做它被设计来做的事情:以用户可以理解的方式快速地向用户显示数据。

我上面描述的是 GraphQL 库像 **[阿波罗](https://www.apollographql.com/)** 和 **[中继](https://facebook.github.io/relay/)** 被设计来做什么。他们抽象出连接后端服务的基础设施实现，以便开发人员可以专注于应用程序的重要部分。

* * *

## 关于 GraphQL 的更多信息

如果你愿意，你可以在这里停下来，更好地理解 GraphQL 是什么，但是如果你想知道更多关于 GraphQL 在客户端和服务器端的好处，请继续阅读。

### GraphQL 和客户端

正如前面几节所讨论的，使用 GraphQL API 使得前端开发更容易进行抽象，并有助于在客户端实现常见的功能。

在这里，我将更详细地介绍它如何处理开发人员通常希望在他们的应用程序中使用的“基础设施”特性。

**直接发送无 HTTP 请求的查询和突变**

GraphQL 允许以声明的方式获取数据。客户端可以简单地查询它需要的数据，而不是进行低级别的 HTTP 调用，GraphQL 会为您处理请求和响应。

**查看图层集成& UI 更新**

一旦 GraphQL 客户端收到并处理了响应，有许多方法可以更新 UI 来反映数据。如果您正在使用 React，GraphQL 客户端利用 **[高阶组件](https://reactjs.org/docs/higher-order-components.html)** 概念(当一个函数获取一个组件并返回一个新组件时)来获取数据，使其可用于它们 props 中的组件。

**客户端缓存**

通常，您会希望维护某种缓存或以前从服务器获取的数据，这对于在用户与 UI 交互时拥有良好的用户体验和较短的加载时间至关重要。然而，对于大多数应用程序来说，完全按照从 GraphQL 客户端解析的方式存储数据并不是最有效的方式。

规范化数据(展平嵌套的查询结果)以便存储中只包含可以用唯一的全局 ID 引用的单个记录是一个更好的选择，以便在将来进行相同的查询时，只快速检索必要的数据。

**验证&基于模式优化查询**

此外，由于模式包含关于客户端可以使用 GraphQL API 做什么的所有信息，构建环境可以解析项目中的 GraphQL 代码，并将其与模式信息进行比较。这使得在过程的早期捕捉错误和打字错误变得更加容易(并且不太可能发生在用户手中)。

同样，UI 代码和数据需求实际上可以在一些语言中并行编写，如 JavaScript，这使得开发人员更容易看到正确的数据最终出现在 UI 的正确部分。

### GraphQL 和服务器端

虽然 GraphQL 围绕前端 API 谈了很多，但是 API 本身还是在服务器端实现的。它允许后端开发人员专注于描述数据，而不是实现和优化大量的 REST 端点。

**GraphQL 查询执行**

首先，GraphQL 使用一种简单的算法将查询转换成结果。它逐个字段地遍历查询，为每个字段执行解析器。

如果子查询需要父解析函数，父查询将首先进行解析，并将其结果传递给子查询，以便在自己的查询中使用。最后，一旦执行算法完成，它将所有数据形成正确的形状并返回。

```
//  ****  Sample  Query  and  Execution  Algorithm  ****  type  Query  {  director(id:  ID!):  Director  }  type  Director  {  movies:  [Movie]  }  type  Movie  {  title:  String  description:  String  }  //  ****  Sample  Query  to  the  Server  ****  query  {  director(id:  "2wsx3edc")  {  movies  {  title  description  }  }  }  //  ****  Execution  Algorithm  Visualized  ****  Query.director(root,  {  id:"2wsx3edc"  },  context)  ->  director  Director.movies(director,  null,  context)  ->  movies  for  each  movie  in  movies  Movie.title(movie,  null,  context)  ->  title  Movie.description(movie,  null,  context)  ->  description 
```

Enter fullscreen mode Exit fullscreen mode

**批量查询解析**

如果在一个查询中对服务器进行多次类似的调用，GraphQL 还提供了使数据获取更加智能的能力。

在 JavaScript 中，可以使用一个名为 **[DataLoader](https://github.com/facebook/dataloader)** 的实用程序来包装获取函数，该函数将等待所有解析器运行，然后确保每个项目只获取一次。

```
//  ****  Sample  Query  and  API  Calls  ****  query{  movies  {  title  director  {  firstName  lastName  }  }  }  /*  Regardless  of  the  fact  that  the  API  is  being  called  for  the  same  piece  of  data  multiple  times,  the  query  executes  for  the  director  info  */  fetch('/directors/1')  fetch('/directors/2')  fetch('/directors/2')  fetch('/directors/1')  fetch('/directors/1')  //  ****  Sample  API  Calls  with  DataLoader  ****  directorLoader  =  new  DirectorLoader()  //  Queue  up  all  the  fetches  directorLoader.load(1);  directorLoader.load(2);  directorLoader.load(2);  directorLoader.load(1);  /*  Then  the  loader  only  makes  the  necessary  amount  of  calls  for  each  unique  piece  of  information  */  fetch('/directors/1')  fetch('/directors/2') 
```

Enter fullscreen mode Exit fullscreen mode

### GraphQL 工具及其生态系统

正如我简单提到的，GraphQL 的类型系统允许我们定义 API 的表面区域，并根据模式验证查询。最酷的是，GraphQL 还允许客户端向服务器请求关于其模式的信息，这一举动被称为**自省**。

**图 QL 内省**

模式的设计者显然知道哪些字段是可用的，但是客户端也可以使用查询的根类型上可用的`__schema`元字段来查询 GraphQL，以找出答案。这是一种强大的技术，可以用来提供许多有趣、有用的特性。

```
//  ****  Introspection  Query  and  Return  Information  ****  query{  __schema  {  types  {  name  }  }  }  Schema  Definition:  type  Query  {  director(id:  ID!):  Director  }  type  Director  {  movies:  [Movie]  }  type  Movie  {  title:  String  description:  String  }  //  Results:  {  "data":  {  "__schema":  {  "types":  [  {  "name":  "Query"  },  {  "name":  "Director"  },  {  "name":  "Movie"  },  {  "name":  "ID"  },  {  "name":  "String"  },  {  "name":  "__Schema"  },  {  "name":  "__Type"  },  {  "name":  "__TypeKind"  },  {  "name":  "__Field"  },  {  "name":  "__InputValue"  },  {  "name":  "__EnumValue"  },  {  "name":  "__Directive"  },  {  "name":  "__DirectiveLocation"  }  ]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

**graph QL&graph QL 游乐场**

最后，有两个我熟悉的非常有用的 GraphQL IDEs。第一个是 **[GraphiQL](https://github.com/graphql/graphiql)** ，这是一个 JavaScript 的浏览器内 IDE，易于在项目中安装和使用(它还预捆绑了 Apollo，以便于使用)。

第二个是**[graph QL Playground](https://github.com/graphcool/graphql-playground)**，可以作为桌面 app 下载，也可以在其网站上使用。它包含了一些与 GraphQL 相同的组件，但拥有一些额外的特性，如自动模式重载、查询历史、一次处理多个 GraphQL APIs 的能力、交互式模式文档等等。

简而言之，两者都是优秀的工具，允许您在 GraphQL 服务器上调试和尝试查询，而不必通过 curl 编写查询。

* * *

## 结论

我希望这有助于阐明 GraphQL 这个热门话题。就它能做什么和如何做而言，这只是冰山一角，但是已经有大量的资源可供学习，并开始使用它构建自己的项目。

就我个人而言，我还没有使用它很长时间，但我已经开始喜欢它了。

过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

感谢阅读！

* * *

## 进一步引用&资源

*   [图形 SQL 文件](http://graphql.org/)
*   [如何绘制 QL](https://www.howtographql.com/advanced/0-clients/)
*   [阿波罗](https://www.apollographql.com/)
*   [继电器](https://facebook.github.io/relay)