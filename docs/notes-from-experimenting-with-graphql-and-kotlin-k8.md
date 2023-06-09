# GraphQL 和 Kotlin 实验笔记

> 原文：<https://dev.to/baens/notes-from-experimenting-with-graphql-and-kotlin-k8>

*这篇文章最初是在[blog.baens.net](https://blog.baens.net/posts/experiment-notes-graphql-postgres/)T3 发表在我的博客上的*

# 这个 GraphQL 是什么东西

我终于有时间试用 GraphQL 了。我阅读了越来越多关于它的内容，并想了解如何用它构建一个数据 API。现在，如果你不知道 GraphQL 是什么，这里是我对它的看法。这是一种创建数据 API 的方法，允许客户告诉您他们实际需要什么数据。而不是你试图预先设计所有的用例，并强迫客户使用。你让他们告诉你他们需要什么数据，什么时候需要。因此，作为数据设计者，您只需公开您拥有的数据，并公开筛选数据和数据内部关系的方法。您不关心客户端如何使用它，您只需公开您拥有的数据。我相信数据 API 应该是这样设计的。这将“为什么”与“如何”分离开来，后者通常会增强 API 用户的能力，并使其在更长时间内更有用。

# kot Lin 上的 GraphQL 入门

我的第一个方法是创建一个 Jersey web 应用程序，它公开了 GraphQL 的正确端点。显然，这是错误的方法。我找到的库( [graphql-java](https://github.com/graphql-java/graphql-java) )并不是这样工作的。它的工作方式是直接向服务器添加一个 servlet。抱歉，如果这完全是希腊语。这么说吧，我正在尝试构建一个恰好具有一些 GraphQL 功能的 REST 端点。这是错误的方法，我需要做的实际上只是添加一个 HTTP 路由到一点逻辑，库为我处理一切。

[“如何用 Java 绘制 SQL”](https://www.howtographql.com/graphql-java/0-introduction/)教程非常有用。这向我展示了如何正确地连接库，并给了我足够的 snipplets 来弄清楚什么需要去哪里。所以让我们开始吧。

# 服务器配置

所以对于我的方法，我想尝试使用[嵌入式 Grizzly Java 服务器](https://javaee.github.io/grizzly/),因为这是我最近在 JVM 项目中使用的服务器。这使得事情变得有点复杂，因为它是打算用作 JAX 遥感服务器。这意味着目前所有的信息都包含了如何设置的信息。但是对于这个特殊的例子，我实际上需要一个 servlet。这绝对是偏离了正常的道路。几个帖子之后，经过一些修补，它确实工作了。

我发现的第一部分是解析 GraphQL 模式。这个嵌入在应用程序内部的文件需要被解析出来，以确定您拥有什么数据。除此之外，您还需要提供一个[根解析器](https://github.com/graphql-java/graphql-java-tools#root-resolvers)，它将包含如何拦截和执行请求的逻辑。这看起来像这样:

```
// Setting up graphql schema, servlet, and bindings to server
val graphqlSchema = SchemaParser.newParser()
        .file("schema.graphqls")
        .resolvers(Query()) // we can add any number of resolvers here
        .build()
        .makeExecutableSchema() 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这很简单。现在，我们将采用该模式并创建一个 servlet，我们可以将它添加到我们的服务器中。这个看起来是这样的:

```
val graphqlServlet = SimpleGraphQLServlet
            .builder(graphqlSchema)
            .build() 
```

Enter fullscreen mode Exit fullscreen mode

这也不是火箭科学。然而，接下来的部分是我花了最长时间的。对于 Grizzly，有一个没有很好记录的类可以帮助您设置 servlets。WebAppContext 是一个神奇的发现，除非你浏览 Java 源代码(...呸！...).这个类为 servlet 设置了它需要运行的所有东西(上下文和绑定，如果你知道 servlet 的话)。我希望有些东西只需要一些注释，但是 Grizzly 不支持(或者至少我不能让它工作)。如果您按照 GraphQL how to 进行操作，他们会使用注释魔法设置一个 servlet。但是在这里，我们必须自己做更多的工作。这是它最后的样子:

```
val webappContext = WebappContext("Graphql Context", "/")

webappContext.addServlet("GraphQL Endpoint", graphqlServlet)
             .addMapping("/graphql")

val server = HttpServer.createSimpleServer()

webappContext.deploy(server) 
```

Enter fullscreen mode Exit fullscreen mode

我还包括了服务器部分，因为您必须将 webapp 上下文“部署”到服务器。对于这几行代码来说，要完成它需要惊人的工作量。但最后我还是喜欢上了。这是一个简单明了的设置，如果需要的话，任何人都可以遵循。

现在，我想做的下一件事是提供一个允许用户使用 API 的接口。这就像是人们所熟悉的[霸气](https://swagger.io)界面的休息终点。对于 GraphQL，通常使用的接口是[graph QL](https://github.com/graphql/graphiql)。安装 GraphiQL 相对容易。您从他们的 github 库中复制了`index.html`文件，将其放在您的代码库中。调整文件指向一个 CDN，然后砰。完成了。但是，让 Grizzly 服务器提供该文件需要一点成本。同样，您需要添加一个 servlet 来正确地提供静态 HTML 文件。这实际上需要一个 [StackOverflow 问题](https://stackoverflow.com/questions/20924739/grizzly-server-with-static-content-and-rest-resource)才能让我走上正确的道路。由此看来，我需要类似于
的`CLStaticHttpHandler`类

```
server.serverConfiguration
          .addHttpHandler(CLStaticHttpHandler(Thread.currentThread().contextClassLoader), "/") 
```

Enter fullscreen mode Exit fullscreen mode

这真的不是突破性的，但它确实存在。困难的部分是把这些碎片按正确的顺序组合起来。但这也是为什么会有博文的原因。来帮助传播这种信息。

就是这样！我们现在已经配置好了服务器，可以开始工作了。

# 在 JAR 中嵌入模式和 html 文件

因此，我们的 Java 代码运行良好，现在我们需要将这些 HTML 文件和 GraphQL 模式文件嵌入到我们的 jar 中，以供服务使用。这确实很简单，但是我想指出，我个人不喜欢 Java 项目的默认文件夹结构。所以我当然要让生活变得艰难，调整一切。下面是我在 gradle 中做的调整，让 HTML 文件在文件夹`src/html`中，graphql 模式文件在`src/graphql`

```
sourceSets {
    main.resources.srcDirs += ['src/html', 'src/graphql']
} 
```

Enter fullscreen mode Exit fullscreen mode

这将手动设置资源目录(在 gradle 中，资源是任何非代码的东西),并将它们正确地放置在 jar 文件中。

# 整个沙浜

如果你想看到一切正常，这里是最后一个仓库:[https://github.com/baens/blog-experimental-graphql-servlet](https://github.com/baens/blog-experimental-graphql-servlet)

你可以通过`./gradlew run`下载并启动服务器，甚至使用 Docker 镜像。最后，所有这些做的事情都和 how to GraphQL 网站做的一样，但是上面的部分是工作所在。解析器和存储库在这一点上没有什么神奇的，并遵循其他地方的例子。

# 注意:目前需要魔法来完成一些事情

因此，希望这一部分不会被需要太久，但我想指出的是，有一个黑客使事情的工作。我偶然发现了一个 bug，这个 bug 会导致这个特定服务器的 GraphQL 处理崩溃。细节有些混乱，但是在这个问题解决之前，我们需要定制一个 servlet 库。这才是真正整洁的地方。那里有一种服务(人们到底是怎么做到免费的？！)它将获取任何 github 存储库并为您构建一个库。 [Jitpack.io](https://jitpack.io/) 简直太棒了，我想大声说这个服务简直不可思议。

所以我不得不在 gradle 文件中加入一些神奇的小东西

```
repositories {
    maven { url 'https://jitpack.io' }
}

dependencies {
    compile 'com.github.briancullen:graphql-java-servlet:master-SNAPSHOT'
} 
```

Enter fullscreen mode Exit fullscreen mode

相当直接。指向 jitpack 存储库，并指向包含该修复的存储库。