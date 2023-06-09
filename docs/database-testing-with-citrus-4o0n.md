# 柑橘数据库测试

> 原文：<https://dev.to/svettwer/database-testing-with-citrus-4o0n>

当需要持久数据存储时，数据库通信是许多应用程序的重要组成部分。可能是订单、客户数据、产品推荐或产品信息，如果有持久存储，这些数据就包含一定的商业价值。因此，软件以正确的方式处理持久存储是很重要的。

在这篇博文中，您将学习如何使用 Citrus 测试您的数据库通信。

## 为什么要用 Citrus 测试你的数据库？

测试数据库通信根本不是一件容易的事情。如果您想要使用一个测试数据库并以一种有效的、自动化的方式使用它，有很多事情需要考虑。

*   您必须部署一个测试数据库。
*   您需要支付额外的硬件成本、许可费用等。
*   您必须设置您的测试模式。
*   您必须确保测试模式与生产模式相匹配，反之亦然。
*   您必须将复杂的样本数据添加到您的模式中，以测试复杂的用例。
*   离线测试你的软件是不可能的。
*   在数据库失败的情况下测试你的软件几乎是不可能的。
*   等等。

根据您个人的基础架构，您会发现需要应对更多的挑战。

我们认为这一切都要结束了，所以我们开发了柑橘 JDBC！

## 要求

要遵循本教程，您的系统应该满足以下要求:

*   饭桶
*   专家
*   Java 8
*   您选择的 IDE

## 简介

随着即将发布的 **citrus framework 2.7.4** ，您将能够验证应用程序的数据库通信，而无需设置测试数据库服务器甚至模式！

柑橘 JDBC 集成由两个基本部分组成，即*柑橘-JDBC-服务器*和*柑橘-JDBC-驱动*。
[![](img/7939dd53f33a4225de9731b77921dc67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4zFjSM2C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l8kz828gbgo3epctd648.png)

为了能够使用柑橘 JDBC 集成，您只需要做两件事:
1)在测试环境中，用柑橘 JDBC 驱动程序交换您的应用程序的生产性 JDBC 驱动程序。
2)通过添加`citrus-jdbc`依赖项，将 Citrus-JDBC 服务器添加到您的测试项目中。

该驱动程序将在 maven central 上的`com.consol.citrus.citrus-db-driver`下提供，而您可以在下面的代码片段中找到依赖关系。

```
<dependency>
  <groupId>com.consol.citrus</groupId>
  <artifactId>citrus-jdbc</artifactId>
  <version>${citrus.version}</version>
  <scope>test</scope>
</dependency> 
```

目前，柑橘 JDBC 支持以下功能。

*   验证:
    *   连接
    *   声明
    *   准备好的声明
    *   可调用语句
    *   处理
*   模拟数据库故障
*   模拟超时
*   模拟请求的结果集

详细信息请看[柑橘用户指南](http://citrusframework.org/docs/user-guide/)。

*请注意，也可以从下面的存储库获得文档的[快照版本以及 Citrus 及其组件的每夜构建:](http://christophd.github.io/citrus/)*

```
<repository>
    <id>consol-labs-snapshots</id>
    <url>http://labs.consol.de/maven/snapshots-repository/</url>
    <snapshots>
      <enabled>true</enabled>
    </snapshots>
    <releases>
      <enabled>false</enabled>
    </releases>
</repository> 
```

## 举例

因为这篇文章是关于 Citrus 的一个快照版本，所以当这个版本最终发布时，一些例子和输出看起来可能会稍有不同。
对于许多其他用例来说， [citrus-examples](https://github.com/christophd/citrus-samples) 项目包含了关于 jdbc 实现的例子。所以在第一步中，请克隆 citrus-examples 项目。

```
git clone https://github.com/christophd/citrus-samples.git 
```

*如果你是第一次接触[柑橘-范例](https://github.com/christophd/citrus-samples)项目，请看看[运行范例](http://www.citrusframework.org/samples/run/)指南。*

假设您已经执行了示例设置，让我们来执行示例！

```
mvn clean verify -f citrus-samples/sample-jdbc/pom.xml -Dembedded

[...]

CITRUS TEST RESULTS

ExecuteQueryIT.testCreateTable ................................. SUCCESS
ExecuteQueryIT.testDelete ...................................... SUCCESS
ExecuteQueryIT.testDropTable ................................... SUCCESS
ExecuteQueryIT.testSelect ...................................... SUCCESS
TodoListIT.selectTodoEntry ..................................... SUCCESS
TodoListIT.testAddTodoEntry .................................... SUCCESS
TodoListIT.testException ....................................... SUCCESS
TodoListIT.testIndexPage ....................................... SUCCESS

TOTAL: 8
SKIPPED:       0 (0.0%)
FAILED:        0 (0.0%)
SUCCESS:       8 (100.0%)

------------------------------------------------------------------------ 
```

既然您已经执行了您的第一个数据库集成测试，让我们看看它们是如何编写的。
*如果你对 Citrus 完全陌生，在继续之前，先阅读 citrusframework.org[网站上的设置指南，了解基本概念可能会有所帮助。](http://citrusframework.org/)*

这个测试对我们的 SUT API 执行一个`GET`请求，要求 todo 条目的列表。之后，当`SELECT`语句命中数据库时，会生成一个结果集，并作为查询结果发送给 SUT。最后，http 客户端的接收步骤验证 SUT 是否产生了正确的数据表示——这里是 XHTML。

```
public class TodoListIT extends TestNGCitrusTestDesigner {

    @Autowired
    private JdbcServer jdbcServer;

    @Autowired
    private HttpClient todoClient;

    @Test
    @CitrusTest
    public void selectTodoEntry(){
        variable("todoName", "citrus:concat('todo_', citrus:randomNumber(4))");
        variable("todoDescription", "Description: ${todoName}");

        http()
            .client(todoClient)
            .send()
            .get("/todolist")
            .fork(true)
            .accept("text/html");

        receive(jdbcServer)
            .message(JdbcMessage.execute("SELECT id, title, description FROM todo_entries"));

        send(jdbcServer)
            .messageType(MessageType.JSON)
            .message(JdbcMessage.success().dataSet("[ {" +
                    "\"id\": \"" + UUID.randomUUID().toString() + "\"," +
                    "\"title\": \"${todoName}\"," +
                    "\"description\": \"${todoDescription}\"," +
                    "\"done\": \"false\"" +
                    "} ]"));

        http()
            .client(todoClient)
            .receive()
            .response(HttpStatus.OK)
            .messageType(MessageType.XHTML)
            .xpath("(//xh:li[@class='list-group-item']/xh:span)[last()]", "${todoName}");
    }
} 
```

正如您在突出显示的部分中看到的，您可以使用 JDBC 服务器端点的`receive`和`send`操作来配置数据库行为，包括返回的结果集。这使您能够在没有额外的测试数据库部署、测试模式或其他操作开销的情况下测试您的系统。

你可能会问自己，`todoClient`和`jdbcServer`从何而来。这些组件在 spring 配置类中指定。

```
@Configuration
public class EndpointConfig {

    @Bean
    public HttpClient todoClient() {
        return CitrusEndpoints.http()
                .client()
                .requestUrl("http://localhost:8080")
                .build();
    }

    @Bean
    public JdbcServer jdbcServer() {
        return CitrusEndpoints.jdbc()
                .server()
                .host("localhost")
                .databaseName("testdb")
                .port(3306)
                .timeout(10000L)
                .autoStart(true)
                .build();
    }
} 
```

## 柑橘-JDBC 特色

既然您已经知道了这些类型的测试是如何设置的，那么让我们来讨论一下通过将`citrus-jdbc`依赖项添加到您的项目中所获得的特性集。由于所有测试的设置都是相似的，所以我将把多余的部分从例子中去掉，把重点放在与特性相关的部分。
*柑橘 XML DSL 中的所有特性也都可用。你可以在[柑橘示例](https://github.com/christophd/citrus-samples)项目中找到相关示例。*

### 模拟数据库故障

柑橘 JDBC 的核心概念之一是关于数据库操作的`success`和`error`之间的区别。这个概念使得在`jdbcServer`收到某个消息后模拟数据库故障变得非常容易。

```
receive(jdbcServer)
        .message(JdbcMessage.execute("@startsWith(" +
                "'INSERT INTO todo_entries (id, title, description, done) " +
                "VALUES (?, ?, ?, ?)')@"));

send(jdbcServer)
        .message(JdbcMessage.error().exception("Something went wrong")); 
```

消息从柑橘 JDBC 服务器发送到柑橘 JDBC 驱动程序后，SUT 接收到数据库故障作为`SQLException`，然后负责处理它。之后，您可以通过使用其他 Citrus 组件(如 Citrus HTTP 客户端)来验证您的 SUT 的行为。

### 准备好/可调用的报表

即使准备好的语句和可调用的语句用于不同的数据库操作和用例，从 API 的角度来看，它们也有相似的工作流程。

```
connection.prepareStatement("INSERT INTO todo_entries (id, title, description, done) VALUES (?, ?, ?, ?)");
connection.prepareCall("{CALL limitedToDoList(?)}"); 
```

这两种语句都是从 JDBC 连接中创建的，使用 SQL 命令表达式作为`String`参数。正因为如此，我们能够像验证常规语句一样验证它们。与常规语句相比，预准备语句和可调用语句之间的唯一区别是，具体调用的参数被附加到查询字符串中以进行验证。

```
http()
        .client(todoClient)
        .send()
        .get("api/todolist/1")
        .fork(true);

receive(jdbcServer)
        .message(JdbcMessage.execute("{CALL limitedToDoList(?)} - (1)"));

send(jdbcServer)
        .messageType(MessageType.JSON)
        .message(JdbcMessage.success().dataSet("[ {" +
                "\"id\": \"${todoId}\"," +
                "\"title\": \"${todoName}\"," +
                "\"description\": \"${todoDescription}\"," +
                "\"done\": \"false\"" +
                "} ]")); 
```

如您所见，API 调用路径参数`api/todolist/1`反映在语句执行验证`"{CALL limitedToDoList(?)} - (1)"`中。多个参数如下所示`"{CALL limitedToDoList(?, ?, ?)} - (1,2,3)"`。当然，你也可以像往常一样使用常规的柑橘匹配器。

### 交易处理

当涉及到数据库的复杂修改时，通常使用事务。Citrus 能够在测试中验证您的系统在事务的启动、提交和回滚操作方面的行为。交易验证必须在服务器 Citrus-JDBC 服务器配置中启用。

```
@Configuration
public class EndpointConfig {
    @Bean
    public JdbcServer jdbcServer() {
        return CitrusEndpoints.jdbc()
                .server()
                .host("localhost")
                .databaseName("testdb")
                .port(3306)
                .timeout(10000L)
                .autoStart(true)
                .autoTransactionHandling(false)
                .build();
    }
} 
```

有了这样配置的`JdbcServer`端点，您就能够在您的测试用例中陈述和验证您的 SUT 的事务行为。

```
receive(jdbcServer)
        .message(JdbcMessage.startTransaction());

receive(jdbcServer)
        .message(JdbcMessage.execute("@startsWith('INSERT INTO todo_entries (id, title, description, done) VALUES (?, ?, ?, ?)')@"));

send(jdbcServer)
        .message(JdbcMessage.success().rowsUpdated(1));

receive(jdbcServer)
        .message(JdbcMessage.commitTransaction()); 
```

由于创建数据库故障很容易，您也可能会导致事务失败，并检查您的 SUT 是否执行了正确的回滚。

```
receive(jdbcServer)
        .message(JdbcMessage.startTransaction());

receive(jdbcServer)
        .message(JdbcMessage.execute("@startsWith('INSERT INTO todo_entries (id, title, description, done) VALUES (?, ?, ?, ?)')@"));

send(jdbcServer)
        .message(JdbcMessage.error().exception("Could not execute something"));

receive(jdbcServer)
        .message(JdbcMessage.rollbackTransaction()); 
```

即使是最糟糕的情况，回滚失败，也可以用另一个 send 语句来模拟。

```
receive(jdbcServer)
        .message(JdbcMessage.startTransaction());

receive(jdbcServer)
        .message(JdbcMessage.execute("@startsWith('INSERT INTO todo_entries (id, title, description, done) VALUES (?, ?, ?, ?)')@"));

send(jdbcServer)
        .message(JdbcMessage.error().exception("Could not execute something"));

receive(jdbcServer)
        .message(JdbcMessage.rollbackTransaction());

send(jdbcServer)
        .message(JdbcMessage.error().exception("Could not perform database rollback")); 
```

### 超时模拟

用真实的数据库模拟超时可能很棘手，如果不是不可能实现的话。使用 Citrus，您只需配置您的服务器来验证连接语句，但不进行测试。

下面是一个实施连接验证的服务器配置。

```
@Bean
public JdbcServer jdbcServer() {
    return CitrusEndpoints.jdbc()
            .server()
            .host("localhost")
            .databaseName("testdb")
            .port(3306)
            .timeout(1000L)
            .autoStart(true)
            .autoConnect(false)
            .build();
} 
```

以下测试不接受来自驱动程序的连接尝试，因为缺少服务器的接收指令。因此，连接将超时，因为没有应答被发送到 SUT。然而，Citrus HTTP 客户端期望 SUT 像预期的那样响应。

```
http()
    .client(todoClient)
    .send()
    .post("/todolist")
    .fork(true)
    .contentType("application/x-www-form-urlencoded")
    .payload("title=${todoName}&description=${todoDescription}");

http()
    .client(todoClient)
    .receive()
    .response(HttpStatus.I_AM_A_TEAPOT); 
```

### 高级验证

有一些高级验证选项可以验证您的 SUT 在数据库操作方面的基本行为，例如:

*   打开和关闭连接
*   创建和结束语句
*   执行事务(见上一节)

因为大多数现代应用程序使用某种框架来进行数据库操作，所以作为开发人员不负责管理这些基本的 JDBC 驱动程序交互，但是如果您想确保万无一失，您完全可以做到！至于事务，必须通过配置`JdbcServer`端点来激活连接和语句验证。

```
@Configuration
public class EndpointConfig {
    @Bean
    public JdbcServer jdbcServer() {
        return CitrusEndpoints.jdbc()
                .server()
                .host("localhost")
                .databaseName("testdb")
                .port(3306)
                .timeout(10000L)
                .autoStart(true)
                .autoConnect(false)
                .autoCreateStatement(false)
                .build();
    }
} 
```

现在，`JdbcServer`实例将验证连接相关的操作，包括语句创建。

```
receive(jdbcServer)
        .message(JdbcMessage.openConnection());

receive(jdbcServer)
        .message(JdbcMessage.createCallableStatement("{CALL limitedToDoList(?)}"));

receive(jdbcServer)
        .message(JdbcMessage.execute("{CALL limitedToDoList(?)} - (1)"));

send(jdbcServer)
        .messageType(MessageType.JSON)
        .message(JdbcMessage.success().dataSet("[]"));

receive(jdbcServer)
        .message(JdbcMessage.closeStatement());

receive(jdbcServer)
        .message(JdbcMessage.closeConnection()); 
```

## 总结

在这篇博文中，你已经执行了你的第一个 citrus 数据库测试，学习了这些测试是如何设置的，如何使用`citrus-jdbc`的特性来验证你的数据库通信，包括底层 JDBC 驱动相关的操作。这使您能够通过使用 Citrus 摆脱大多数测试数据库部署。如果你对进一步的阅读感兴趣，我强烈推荐你访问下面的链接。

*   [柑橘](http://citrusframework.org/)
*   [柑橘文件快照](http://christophd.github.io/citrus/)
*   [GitHub 上的柑橘](https://github.com/christophd/citrus)
*   [柑橘示例](https://github.com/christophd/citrus-samples)
*   [跑柑橘的例子](http://www.citrusframework.org/samples/run/)

本文原载于[labs . consol . de](https://labs.consol.de/development/citrus/2018/03/01/database-testing-with-citrus.html)2018-03-01