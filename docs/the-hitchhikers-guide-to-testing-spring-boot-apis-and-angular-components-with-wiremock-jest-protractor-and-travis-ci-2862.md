# 用 WireMock、Jest、量角器和 Travis CI 测试 Spring Boot API 和角形组件的指南

> 原文：<https://dev.to/oktadev/the-hitchhikers-guide-to-testing-spring-boot-apis-and-angular-components-with-wiremock-jest-protractor-and-travis-ci-2862>

用 Spring Boot 和 Ionic(基于 Angular)编写应用程序可能会很有趣，因为这两个框架都提供了良好的开发者体验(DX)。你更改一个文件，保存它，应用程序会自动重新载入你的更改。这个特性通常被称为热重装，这是一种很好的开发方式。这太好了，它可以让你忘记自动化测试。

我承认，编写测试很难。编写测试通常并不困难，毕竟这只是代码。然而，先写一个*测试*是一个很难养成的习惯。然而，如果你进行测试驱动开发(TDD ),你将有机会开发出更好的 API。毕竟，您的测试将充当您的 API 的客户端，您将看到对于您的用户来说代码是什么样子的。

几个月前，我写了一些关于开发一个[加密货币财富跟踪 PWA](https://dev.to/blog/2018/01/18/cryptocurrency-pwa-secured-by-okta) 和[将你的持有量存储为 Okta 自定义档案属性](https://dev.to/blog/2018/01/23/replace-local-storage-with-okta-profile-attributes)的帖子。为了向您展示如何为后端和前端组件编写测试，我招募了[布莱恩·德默斯](https://twitter.com/briandemers)来帮我。Brian 是 Okta Java SDK 的首席开发人员，和他在一起很有趣。

Brian 写了这篇文章中关于后端代码的部分(Java + Spring Boot)，而我写了关于前端的部分(TypeScript + Angular)。当你在每个部分看到“我”时，它要么是布莱恩，要么是我自己，这取决于上下文。

在本教程中，您将看到如何实现测试现代应用程序的许多方面:

*   [为你的 Spring Boot 控制器添加单元测试](#add-unit-tests-for-your-spring-boot-controllers)
*   [用 WireMock 模拟 Okta 的 API](#mock-oktas-api-with-wiremock)
*   [为您的离子/角度组件添加单元测试](#add-unit-tests-for-your-ionic--angular-components)
*   [驾驶并使用量角器测试您的 Ionic PWA](#drive-and-test-your-ionic-pwa-with-protractor)
*   [添加与 Travis CI 的持续集成](#add-continuous-integration-with-travis-ci)
*   [添加代码覆盖报告](#add-code-coverage-reports)

我喜欢称之为搭便车指南，因为你可以跳到本指南的任何部分，学习如何测试那些特定的组件。如果您对某个特定部分有任何改进建议，请告诉我们，以便我们进行改进！我们尽可能保持我们的博客帖子是最新的。

首先，您需要克隆包含 Ionic PWA 和 Spring Boot API 的 GitHub 库，并检查`testing-start`分支。

```
git clone -b testing-start https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example.git 
```

Enter fullscreen mode Exit fullscreen mode

下面的说明假设您正在这个克隆项目的`okta-ionic-crypto-java-sdk-example`目录下操作。

## 设置秋田

首先，Okta 是什么？简而言之，我们是一个 API 服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   在一个地方存储和维护您的所有用户
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

简而言之:我们让[用户账户管理](https://developer.okta.com/product/user-management/)比你可能习惯的更容易、更安全、更可扩展。

要开始这个教程，你首先需要登录你的永远免费的 Okta 开发者账户，或者如果你还没有的话，注册。

注册并登录后，您需要将一个`holdings`属性添加到您组织的用户资料中。登录 Okta 开发者控制台，然后导航到**用户** > **个人资料编辑器**。点击**轮廓**查看表格中的第一个轮廓。你可以通过它的 Okta 标志来识别它。点击**添加属性**并使用以下值:

*   显示名称:`Holdings`
*   变量名:`holdings`
*   描述:`Cryptocurrency Holdings`

您将需要创建一个 API 令牌和 OIDC 应用程序来获取值，以执行身份验证并在 Okta 中存储数据。

导航至**应用** > **添加应用**。点击**单页应用**，点击**下一个**，给应用取一个你会记住的名字。点击**完成**。

为了让 Okta Java SDK 与 Okta 的 API 对话，您需要创建一个 API 令牌。简化的步骤如下:

1.  登录您的开发人员控制台
2.  导航到 **API** > **令牌**并点击**创建令牌**
3.  给你的令牌一个名字，然后复制它的值

### 您的 Spring Boot API 的服务器配置

打开`holdings-api/src/main/resources/application.properties`并添加 API 令牌作为属性。当你在那里时，设置`issuer`和`clientId`来匹配你的 OIDC 应用。

**注意:**`{yourOktaDomain}`的值应该是类似于`dev-123456.oktapreview`的东西。确保您没有将`-admin`包含在值中！

```
okta.oauth2.issuer=https://{yourOktaDomain}.com/oauth2/default
okta.oauth2.clientId={yourClientId}
okta.client.token=XXX 
```

Enter fullscreen mode Exit fullscreen mode

> **注意:**如果您不想在源代码中指定这些值，您可以将它们设置为环境变量。例如，`OKTA_CLIENT_TOKEN`是将覆盖`okta.client.token`属性的环境变量的名称。参见 Spring Boot 的[外部化配置文档](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)了解更多信息。

### Ionic PWA 的客户端配置

对于客户端，设置`issuer`并将`clientId`复制到`crypto-pwa/src/pages/login/login.ts`中。

```
const config = {
  issuer: 'https://{yourOktaDomain}.com/oauth2/default',
  redirectUri: window.location.origin + '/implicit/callback',
  clientId: '{clientId}'
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 为您的 Spring Boot 控制器添加单元测试

每个人都对单元测试是什么以及它意味着什么有自己的看法。对我来说，单元测试关注于尽可能执行最小的代码块。简单来说，就是运用代码的公共方法。

以下部分中的测试被添加到[拉式请求](https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example/pull/3)中。除了添加测试之外，我还将`Holding` bean 的 setters 改为 fluent，这样就可以将这些方法链接在一起，如下所示。

Spring 有很多辅助测试基础设施来帮助设置应用程序的上下文。如果可能的话，我会避免使用它，这样可以更集中和更快地运行测试。将依赖项注入代码的方式会改变测试的选择。例如，如果我们测试的类`HoldingsController`是用字段注入编写的，它看起来会像这样:

```
public class HoldingsController {

    @Autowired
    private Client client;

    public HoldingsController() {}
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的实现只能在 Spring 容器中运行。相反，同样的代码可以被重构以使用构造函数注入:

```
public class HoldingsController {
    private final Client client;

    public HoldingsController(Client client) {
        this.client = client;
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这两者之间的区别对你来说可能很明显，也可能不明显。第二个例子是普通的 ol' Java 代码:`Client`字段是 final，一旦对象被构造，它就可以使用了，对我来说最重要的是，它可以在没有 Spring 的情况下使用。构造函数注入使得编写测试更加容易；您可以孤立地测试您的代码，并模拟出您正在测试的行为。你可以阅读奥利弗·吉尔克的文章。

如果可以选择的话，我首选的测试框架是 [Mockito](https://site.mockito.org/) 和 [Hamcrest](http://hamcrest.org/JavaHamcrest/) 。Mockito 使 stub 行为变得很容易，Hamcrest 既有优秀的默认断言消息，又有助于将您的测试代码与 JUnit 和 TestNG 之间的差异隔离开来。我通常也用 Groovy 编写测试来帮助减少一些样板文件，但是今天我将坚持使用 Java。

为了提醒您，我将为`HoldingsController`编写测试；这个类有一个单独的构造函数和方法用于`@GetMapping`和`@PostMapping`。我来重点介绍一下`saveHoldings(@RequestBody Holding[] holdings, Principal principal)`方法:

```
@PostMapping
public Holding[] saveHoldings(@RequestBody Holding[] holdings, Principal principal) {
    User user = client.getUser(principal.getName());
    try {
        String json = mapper.writeValueAsString(holdings);
        user.getProfile().put(HOLDINGS_ATTRIBUTE_NAME, json);
        user.update();
    } catch (JsonProcessingException e) {
        logger.error("Error saving Okta custom data: " + e.getMessage());
        e.printStackTrace();
    }
    return holdings;
} 
```

Enter fullscreen mode Exit fullscreen mode

该方法将参数`holdings`保存到与用户相关联的 Okta 自定义配置文件属性中。

### 用 Mockito 嘲讽 Okta 的 Java SDK

Mockito 和 Hamcrest 通常都用于静态导入，比如:

```
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;
import static org.mockito.Mockito.*; 
```

Enter fullscreen mode Exit fullscreen mode

对于那些不喜欢通配符导入的人:

```
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.contains;
import static org.hamcrest.Matchers.is;

import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.eq; 
```

Enter fullscreen mode Exit fullscreen mode

我在 [HoldingsControllerTest](https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example/blob/master/holdings-api/src/test/java/com/okta/developer/holdingsapi/HoldingsControllerTest.java) 中定义了一个`saveHoldingsTest`，在其中我创建了一些模拟对象:

```
Client client = mock(Client.class);
User user = mock(User.class);
UserProfile userProfile = mock(UserProfile.class);
Principal principal = mock(Principal.class); 
```

Enter fullscreen mode Exit fullscreen mode

然后定义模拟的行为:

```
String username = "joe.coder@example.com";
when(principal.getName()).thenReturn(username);
when(client.getUser(username)).thenReturn(user);
when(user.getProfile()).thenReturn(userProfile); 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到行为驱动的 Given-When-Then 风格的 Mockito(在本例中,“Given”是模拟定义)。任何时候调用`principal.getName()`都将返回`"joe.coder@example.com"`，当调用`client.getUser("joe.coder@example.com")`时，我们模拟的`User`实例将返回。

现在对于简单的部分，调用`HoldingsController.saveHoldings()`

```
Holding[] inputHoldings = new Holding[] {
        new Holding()
            .setCrypto("crypto1")
            .setCurrency("currency1")
            .setAmount("amount1"),
        new Holding()
            .setCrypto("crypto2")
            .setCurrency("currency2")
            .setAmount("amount2")
};

HoldingsController holdingsController = new HoldingsController(client);
Holding[] outputHoldings = holdingsController.saveHoldings(inputHoldings, principal); 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的，但这就是重点！使用构造函数注入允许我们像对待任何其他 Java 对象一样对待这个对象。

剩下的唯一事情就是验证结果。使用 Hamcrest，我们可以验证`saveHoldings`方法返回一个等于输入的`Holding[]`。

```
assertThat(outputHoldings, is(inputHoldings)); 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要验证自定义属性`holdings`是否被设置，以及`user.update()`是否被调用，为此我们将使用一个 Mockito [ArgumentCaptor](http://site.mockito.org/javadoc/current/org/mockito/ArgumentCaptor.html) 来捕获`holdings` :
的 JSON 字符串值

```
ArgumentCaptor<String> holdingsJsonCaptor = ArgumentCaptor.forClass(String.class);
verify(userProfile).put(eq("holdings"), holdingsJsonCaptor.capture());
verify(user).update(); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以验证 JSON 字符串。 [Spotify Hamcrest](https://github.com/spotify/java-hamcrest) (是的，就是你在编码时播放的那个 [Spotify](https://www.spotify.com) )是我最喜欢的测试库，它展示了定制 Hamcrest 匹配器的强大功能和可读性。

```
JsonNode holdingsParsed = new ObjectMapper().readTree(holdingsJsonCaptor.getValue());
assertThat(holdingsParsed, jsonArray(contains(
        jsonObject()
            .where("crypto", jsonText("crypto1"))
            .where("currency", jsonText("currency1"))
            .where("amount", jsonText("amount1")),
        jsonObject()
            .where("crypto", jsonText("crypto2"))
            .where("currency", jsonText("currency2"))
            .where("amount", jsonText("amount2"))
))); 
```

Enter fullscreen mode Exit fullscreen mode

我在上面提到过，Hamcrest 有很好的默认断言消息，希望你永远不会看到它们，但这不太可能，这里是一个失败的 JSON 断言的输出示例:

```
java.lang.AssertionError:
Expected: an array node whose elements iterable containing [{
  "crypto": a text node with value that is "crypto1"
  "currency": a text node with value that is "currency1"
  "amount": a text node with value that is "amount1"
}, {
  "crypto": a text node with value that is "crypto2"
  "currency": a text node with value that is "currency2"
  "amount": a text node with value that is "amount2"
}]
     but: was an array node whose elements item 0: {
  "crypto": was a text node with value that was "wrong-crypto"
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用 WireMock 模拟 Okta 的 API

集成测试稍微复杂一些。有时，您会启动整个应用程序及其所有必需的依赖项。其他时候，您模拟外部依赖，这样您就可以专注于您的代码(而不用担心仅仅为了运行您的测试就需要互联网连接)。我是后者的忠实粉丝，并在可能的情况下尝试采用这种方法。

`HoldingsApiApplication`有两个外部依赖项，Okta OAuth 2.0 IdP 和 Okta 管理 API。我将对两者都使用 [WireMock](http://wiremock.org/) 。对于 OAuth 模拟，您还有另一个选项，但是它要求在测试时改变您的应用程序的运行方式。我喜欢在集成测试期间对我的应用程序做尽可能少的更改。在这一节的最后，我会给你一个比较这两种策略的链接。

当你开始观察 [HoldingsApiIT](https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example/blob/master/holdings-api/src/test/java/com/okta/developer/its/HoldingsApiIT.java) 时，你会发现一些有趣的事情:

```
@RunWith(SpringRunner.class)
@ContextConfiguration(initializers = HoldingsApiIT.RandomPortInitializer.class)
@SpringBootTest(classes = {HoldingsApiApplication.class},
                webEnvironment = RANDOM_PORT,
                properties = {
                    "okta.client.token=FAKE_TEST_TOKEN",
                    "okta.oauth2.localTokenValidation=false",
                    "okta.oauth2.discoveryDisabled=true",
                    "okta.client.orgUrl=http://localhost:${wiremock.server.port}",
                    "okta.oauth2.issuer=http://localhost:${wiremock.server.port}/oauth/issuer",
                    "security.oauth2.resource.userInfoUri=http://localhost:${wiremock.server.port}/oauth/userInfoUri"
                })
public class HoldingsApiIT { 
```

Enter fullscreen mode Exit fullscreen mode

上面是一个配置为使用`SpringRunner`运行的 JUnit 测试。`ContextConfiguration`注释定义了一个`RandomPortInitializer`，它将在测试运行之前为属性`wiremock.server.port`分配一个随机端口。这样做是为了让自动配置的 Okta `Client`和 OAuth 组件访问本地 WireMock 服务器(通过设置上面的`properties`)。

如果您还没有猜到，WireMock 是一个可以用来模拟 HTTP 响应的漂亮的小测试库，它旋转出一个嵌入式 [Jetty](https://www.eclipse.org/jetty/) 实例来处理真正的 HTTP 请求/响应。例如，如果您想要模仿对`/my/resource`的响应并返回 XML 内容`<hello>still using XML</hello>`，它将会用下面的语法表示:

```
stubFor(get(urlEqualTo("/my/resource"))
    .withHeader("Accept", equalTo("text/xml"))
    .willReturn(aResponse()
        .withStatus(200)
        .withHeader("Content-Type", "text/xml")
        .withBody("<hello>still using XML</hello>"))); 
```

Enter fullscreen mode Exit fullscreen mode

在简单的情况下，您还可以使用 JUnit 规则来自动启动/停止 WireMock 服务器。然而，当与`SpringRunner`结合使用时，它就不能很好地工作了，按照规则和字段注入发生的顺序来做。为了解决这个问题，我们简单地回到久经考验的`@Before`和`@After`测试注释。

```
private WireMockServer wireMockServer;

@Before
public void startMockServer() throws IOException {
    wireMockServer = new WireMockServer(wireMockConfig().port(mockServerPort));
    configureWireMock();
    wireMockServer.start();
}

@After
public void stopMockServer() {
    if (wireMockServer != null && wireMockServer.isRunning()) {
        wireMockServer.stop();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我还定义了一个方法`configureWireMock()`，它将配置 WireMock 如何响应请求:

```
private void configureWireMock() throws IOException {
     // load a JSON file from the classpath
    String body = StreamUtils.copyToString(getClass().getResourceAsStream("/its/user.json"), StandardCharsets.UTF_8);

    // respond to GET for user
    wireMockServer.stubFor(WireMock.get("/api/v1/users/" + TEST_USER_EMAIl)
            .willReturn(aResponse().withBody(body)));

    // respond to PUT for user
    wireMockServer.stubFor(WireMock.put("/api/v1/users/" + TEST_USER_ID)
            .willReturn(aResponse().withBody(body)));

    // OAuth userInfoUri
    String userInfoBody = StreamUtils.copyToString(getClass().getResourceAsStream("/its/userInfo.json"), StandardCharsets.UTF_8);
    wireMockServer.stubFor(
            WireMock.get("/oauth/userInfoUri")
                .withHeader("Authorization", WireMock.equalTo("Bearer "+ TEST_ACCESS_TOKEN))
            .willReturn(aResponse()
                    .withBody(userInfoBody)
                    .withHeader("Content-Type", MediaType.APPLICATION_JSON_VALUE)
            ));
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的方法从类路径`/its/user.json`加载一个 JSON 文件，并为`/api/v1/users`端点的 GET 和 PUT 请求返回其内容。对于向`/oauth/userInfoUri`发出的 GET 请求，类似的回应被嘲笑。

其余的测试代码看起来非常简单:

```
@Autowired
private TestRestTemplate restTemplate;

@Test
public void testGetHoldings() {

    List<Holding> holdings = Arrays.asList(restTemplate.getForObject("/api/holdings", Holding[].class));

    // use Spotify's hamcrest-pojo to validate the objects
    assertThat(holdings, contains(
            pojo(Holding.class)
                .withProperty("crypto",   is("crypto-1"))
                .withProperty("currency", is("currency-1"))
                .withProperty("amount",   is("amount-1")),
            pojo(Holding.class)
                .withProperty("crypto",   is("crypto-2"))
                .withProperty("currency", is("currency-2"))
                .withProperty("amount",   is("amount-2"))
    ));
} 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，这个集成测试比上面相应的单元测试更复杂，速度也更慢。我觉得开发人员通常会创建集成测试，因为这些测试有较少的代码行。单元测试几乎立即完成，另一方面，每个集成测试都会为每个测试旋转两个 servlet 容器(一个用于我们的应用程序，另一个用于 WireMock )!拥有单元测试和集成测试是很重要的，你应该写更多的单元测试而不是集成测试。查看[实用测试金字塔](https://martinfowler.com/articles/practical-test-pyramid.html)以了解更多相关信息。

### 用 Spring MVC 测试的 MockMVC 代替

正如我上面提到的，您可以用一些小技巧跳过 OAuth 验证检查，而不是使用 WireMock 来伪造 OAuth 响应。

我不打算在这里赘述，如果你感兴趣，你可以看看我是如何用不同的技术写同样的测试的。需要关注的关键领域包括:

*   `TestResourceServerConfiguration`禁用资源服务器的无状态配置(不推荐用于生产)
*   指示测试框架为您创建一个 Spring SecurityContext
*   如何用`apply(springSecurity())`创建`MockMvc`
*   调用`MockMvc.perform()`时使用`with(securityContext(SecurityContextHolder.getContext()))`

## 为你的离子/角度组件添加单元测试

Ionic 3.x 基于 Angular 构建。这意味着您应该能够使用角度测试工具来验证您的应用程序是否按预期工作。当我创建 Angular 应用程序时，我倾向于使用 [Angular CLI](https://cli.angular.io) 。Angular CLI 内置了一个测试框架(Jasmine ),以及通过命令行或在持续集成服务器中进行测试的工具。如果你有兴趣了解更多关于如何为 Angular 应用编写测试，我邀请你访问我的 Angular CLI 教程的[测试部分](https://github.com/mraible/ng-demo#testing)。提示:阅读 DocGist 上的测试教程对[来说更容易。](http://gist.asciidoctor.org/?github-mraible/ng-demo//README.adoc#_testing)

Ionic CLI 不像 Angular CLI 那样包含`test`命令。为了解决这个问题，我求助于丹尼尔·索尔的[离子超级启动器](https://github.com/danielsogl/ionic-super-starter)。它使用 [Jest](https://facebook.github.io/jest/) 进行单元测试，模拟许多 Ionic 类，并且也有端到端的测试支持。

我通过用 Daniel 的 starter:
创建一个应用程序，学会了如何给 Ionic 应用程序添加测试支持

```
ionic start ionic-super-starter danielsogl/super 
```

Enter fullscreen mode Exit fullscreen mode

然后，我将配置复制到`crypto-pwa`项目中。我在下面简化了这些步骤。

1)在您的`package.json`的`scripts`部分添加脚本来运行单元测试和端到端(e2e)测试:

```
"test":  "jest",  "test:watch":  "jest --watch",  "test:ci":  "jest --runInBand",  "test:coverage":  "jest --coverage",  "e2e":  "npm run e2e-update && npm run e2e-test",  "e2e-test":  "protractor ./test/protractor.conf.js",  "e2e-update":  "webdriver-manager update --standalone false --gecko false" 
```

Enter fullscreen mode Exit fullscreen mode

2)将以下依赖项添加到`package.json`的`devDependencies`部分:

```
"typescript":  "2.8.1",  "@types/jest":  "22.2.3",  "@types/node":  "9.6.5",  "html-loader":  "0.5.1",  "ionic-mocks-jest":  "1.0.3",  "istanbul-instrumenter-loader":  "3.0.0",  "jasmine":  "3.1.0",  "jasmine-spec-reporter":  "4.1.0",  "jest":  "22.4.3",  "jest-preset-angular":  "5.2.1",  "null-loader":  "0.1.1",  "protractor":  "5.1.1",  "ts-loader":  "4.2.0",  "ts-node":  "6.0.0" 
```

Enter fullscreen mode Exit fullscreen mode

3)增加 Jest 的配置，与`package.json` :
中的`devDependencies`和`scripts`在同一层

```
"jest":  {  "preset":  "jest-preset-angular",  "setupTestFrameworkScriptFile":  "<rootDir>/test/jest.conf.ts",  "transformIgnorePatterns":  [  "node_modules/(?!@ngrx|@ionic-native|@ionic)"  ],  "coveragePathIgnorePatterns":  [  "<rootDir>/e2e/"  ],  "collectCoverageFrom":  [  "**/*.{ts}",  "!**/*d.{ts}",  "!**/*interface.{ts}",  "!**/*module.{ts}",  "!**/*main.{ts}",  "!**/node_modules/**"  ],  "moduleFileExtensions":  [  "ts",  "js",  "json"  ],  "coverageReporters":  [  "json",  "lcov",  "text"  ],  "coverageDirectory":  "./public/coverage"  } 
```

Enter fullscreen mode Exit fullscreen mode

4)创建一个`crypto-pwa/test`目录，并在其中创建两个文件:`jest.conf.ts`和`protractor.conf.js`。

```
// test/jest.conf.ts
import 'jest-preset-angular'; 
```

Enter fullscreen mode Exit fullscreen mode

```
// test/protractor.conf.js
const { SpecReporter } = require('jasmine-spec-reporter');

exports.config = {
  allScriptsTimeout: 11000,
  specs: [
    '../e2e/**/*.e2e-spec.ts'
  ],
  capabilities: {
    'browserName': 'chrome'
  },
  directConnect: true,
  baseUrl: 'http://localhost:8100/',
  framework: 'jasmine',
  jasmineNodeOpts: {
    showColors: true,
    defaultTimeoutInterval: 30000,
    print: function() {}
  },
  onPrepare() {
    require('ts-node').register({
      project: 'e2e/tsconfig.e2e.json'
    });
    jasmine.getEnv().addReporter(new SpecReporter({ spec: { displayStacktrace: true } }));
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

5)创建`src/tsconfig.spec.json`并用下面的 JSON 填充它:

```
{  "extends":  "../tsconfig.json",  "compilerOptions":  {  "outDir":  "../out-tsc/spec",  "module":  "commonjs",  "target":  "es5",  "allowJs":  true  },  "include":  [  "**/*.spec.ts"  ],  "exclude":  [  "**/*e2e-spec.ts"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

6)创建`e2e/tsconfig.e2e.json`并配置其设置:

```
{  "extends":  "../tsconfig.json",  "compilerOptions":  {  "outDir":  "../out-tsc/e2e",  "baseUrl":  "./",  "module":  "commonjs",  "target":  "es5",  "typeRoots":  [  "../node_modules/@types"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**做了所有这些更改后，我通过安装 [npm-check-updates](https://www.npmjs.com/package/npm-check-updates) 并运行`ncu -u`来更新`crypto-pwa/package.json`中的所有依赖项。

7)运行`npm i`安装您添加的新依赖项。

### 用 Jest 增加离子成分的单元测试

根据 Jest 的主页，脸书用它来测试所有的 JavaScript 代码，包括 React 应用。Jest 力求零配置，但从上面的文件可以看出，它仍然需要一些配置。它有内置的代码覆盖报告，这是一个很酷的东西。

> 如果你是 Jest 新手，想学习基础知识，我邀请你阅读它的[入门](https://facebook.github.io/jest/docs/en/getting-started.html)指南。

Jest 类似于[因果报应](https://karma-runner.github.io)和[茉莉](https://jasmine.github.io)。Karma 是一个测试运行器，Jasmine 是一个行为驱动的开发框架，用于测试 JavaScript 代码。对于 Java 开发者来说，Jasmine 就像 JavaScript 生态系统的 JUnit。一个简单的 Jasmine 测试如下:

```
describe('A suite is just a function', () => {
  let a;

  it('and so is a spec', () => {
    a = true;

    expect(a).toBe(true);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

Jest 测试看起来非常相似，但是有一些细微的区别。如果你有兴趣了解更多，我建议你谷歌一下“ [jest vs jasmine](https://www.google.com/search?q=jest+vs+jasmine) ”

在您的 Ionic 项目中添加 Jest 支持之后，您有几个选项来运行单元测试:

1.  运行`npm test`来执行所有的单元测试。
2.  运行`npm run test:watch`执行测试并观察变化。如果有任何变化，测试会自动执行。
3.  运行`npm test:ci`在持续集成(CI)服务器中执行测试。这将在当前进程中连续运行所有测试，而不是创建运行测试的子进程的工作池。
4.  运行`npm run test:coverage`来生成一个测试覆盖报告。

知道这些命令很好，但是只有在有单元测试的情况下才有用！

创建`crypto-pwa/src/app/app.component.spec.ts`并填充以下代码:

```
import { async, TestBed } from '@angular/core/testing';
import { IonicModule, Platform } from 'ionic-angular';
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';
import { PlatformMock, SplashScreenMock, StatusBarMock } from 'ionic-mocks-jest';
import { MyApp } from './app.component';
import { OAuthService } from 'angular-oauth2-oidc';

describe('MyApp Component', () => {
  let fixture;
  let component;
  let oauthService = {
    hasValidIdToken() {
      return false;
    }
  };

  beforeEach(
    async(() => {
      TestBed.configureTestingModule({
        declarations: [MyApp],
        imports: [IonicModule.forRoot(MyApp)],
        providers: [
          {provide: StatusBar, useFactory: () => StatusBarMock.instance()},
          {provide: SplashScreen, useFactory: () => SplashScreenMock.instance()},
          {provide: Platform, useFactory: () => PlatformMock.instance()},
          {provide: OAuthService, useFactory: () => oauthService}
        ]
      });
    })
  );

  beforeEach(() => {
    fixture = TestBed.createComponent(MyApp);
    component = fixture.componentInstance;
  });

  it('should be created', () => {
    expect(component instanceof MyApp).toBe(true);
  });

  it('should show login page', () => {
    expect(component.rootPage).toEqual('LoginPage');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个测试会通过的，但是还有很多事情要做，所以让我来给你分析一下。首先，你可能会注意到有一些来自`ionic-mocks-jest`的导入。如果使用`ionic start`生成一个 Ionic 应用程序，并选择“tabs”启动器，您将得到一个看起来如下的`MyApp`组件:

```
export class MyApp {
  rootPage:any = TabsPage;

  constructor(platform: Platform, statusBar: StatusBar, splashScreen: SplashScreen) {
    platform.ready().then(() => {
      // Okay, so the platform is ready and our plugins are available.
      // Here you can do any higher level native things you might need.
      statusBar.styleDefault();
      splashScreen.hide();
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

每当一个组件在其构造函数中定义了依赖项，您就需要在组件的测试中“提供”这些依赖项。

为了让依赖于 Ionic 组件的组件变得更容易， [Daniel Sogl](https://twitter.com/sogldaniel) 分叉了 [ionic-mocks](https://github.com/stonelasley/ionic-mocks) 项目，创建了 [ionic-mocks-jest](https://github.com/danielsogl/ionic-mocks-jest) 。这个项目使用 [Jasmine Spy Objects](https://jasmine.github.io/2.0/introduction.html#section-Spies) 提供了许多模拟对象，并支持 Jest。间谍可以拦截任何函数并跟踪对它的调用，以及传入的所有参数。你可以想象这是多么有用，尤其是在测试的时候。

`crypto-pwa`项目旨在成为一个 PWA，而不是一个本地应用，所以这些依赖关系不存在于它的`MyApp`组件中。然而，我想我应该指出它们，以防你的应用程序使用它们。

有一些样板代码来测试一个角度组件。如果我简化上面的测试，只得到一个基本组件和测试，它看起来像下面这样:

```
describe('MyApp Component', () => {
  let fixture;
  let component;

  beforeEach(
    async(() => {
      TestBed.configureTestingModule({
        declarations: [MyApp]
      });
    })
  );

  beforeEach(() => {
    fixture = TestBed.createComponent(MyApp);
    component = fixture.componentInstance;
  });

  it('should show login page', () => {
    expect(component.rootPage).toEqual('LoginPage');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

该测试将失败，因为`MyApp`组件装载了含有离子元素的`app.html`。

```
 FAIL  src/app/app.component.spec.ts
  MyApp Component
    ✕ should be created (647ms)
    ○ skipped 1 test

  ● MyApp Component › should be created

    Template parse errors:
    Can't bind to 'root' since it isn't a known property of 'ion-nav'.
    1\. If 'ion-nav' is an Angular component and it has 'root' input, t 
```

Enter fullscreen mode Exit fullscreen mode

导入`IonicModule`你会走得更远一点。这是一种常见的模式。如果您的模板中有定制组件，您将需要在您的测试中导入它们的模块，就像您在`app.module.ts`中所做的一样。

```
beforeEach(
  async(() => {
    TestBed.configureTestingModule({
      declarations: [MyApp],
      imports: [IonicModule.forRoot(MyApp)]
    });
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在尝试运行测试，它会抱怨`MyApp`中的构造函数依赖不可用。

```
StaticInjectorError(DynamicTestModule)[MyApp -> OAuthService]:
  StaticInjectorError(Platform: core)[MyApp -> OAuthService]:
    NullInjectorError: No provider for OAuthService! 
```

Enter fullscreen mode Exit fullscreen mode

要解决这个问题，创建一个模拟实例`oauthService`并将其指定为提供者。

```
const oauthService = {
  hasValidIdToken() {
    return false;
  }
};

beforeEach(
  async(() => {
    TestBed.configureTestingModule({
      declarations: [MyApp],
      imports: [IonicModule.forRoot(MyApp)],
      providers: [
        {provide: OAuthService, useFactory: () => oauthService}
      ]
    });
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

**提示:**你也可以用`{provide: OAuthService, useValue: oauthService}`来代替你的假`OAuthService`来代替真的。另一个选项是`useClass`，它允许你指定一个不同的类。

你只需要在`oauthService`中定义`hasValidIdToken()`，因为这是在`MyApp` :
中使用的唯一方法

```
import { Component } from '@angular/core';
import { OAuthService } from 'angular-oauth2-oidc';

@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage: any = 'HomePage';

  constructor(oauthService: OAuthService) {
    if (oauthService.hasValidIdToken()) {
      this.rootPage = 'HomePage';
    } else {
      this.rootPage = 'LoginPage';
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以创建一个测试，确保在不存在 ID 标记时显示登录页面。

```
it('should show login page', () => {
  expect(component.rootPage).toEqual('LoginPage');
}); 
```

Enter fullscreen mode Exit fullscreen mode

跑`npm test`一切都该过去了！

```
> jest

 PASS  src/app/app.component.spec.ts
  MyApp Component
    ✓ should be created (517ms)
    ✓ should show login page (330ms)

Test Suites: 1 passed, 1 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        4.363s, estimated 5s
Ran all test suites. 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**您可能会遇到以下测试失败:

```
Cannot find module 'rxjs-compat/Observable' from 'Observable.js' 
```

Enter fullscreen mode Exit fullscreen mode

如果出现这种情况，是因为运行`ncu -u`将 [RxJS](http://reactivex.io/rxjs/) 从 5.5.8 更新到 6.x，要修复它，可以修改你的`package.json`恢复到 5.5.10(最新版本)或者运行`npm i -rxjs-compat`。更多信息请参见[版本 6 迁移指南](https://github.com/ReactiveX/rxjs/blob/master/MIGRATION.md)。

在用户通过其`ionViewDidLoad`方法验证后，`HomeComponent`加载用户持有的资产列表。

```
ionViewDidLoad(): void {
  if (!this.oauthService.hasValidIdToken()) {
    this.navCtrl.push('LoginPage');
  }
  this.holdingsProvider.loadHoldings();
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试一切是否按预期工作，为其提供者创建带有存根的`crypto-pwa/src/pages/home/home.spec.ts`。

```
import { IonicModule, NavController } from 'ionic-angular';
import { OAuthService } from 'angular-oauth2-oidc';
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { HomePage } from './home';
import { HoldingsProvider } from '../../providers/holdings/holdings';
import { By } from '@angular/platform-browser';

describe('HomePage', () => {
  let fixture: ComponentFixture<HomePage>;
  let component: HomePage;
  let oauthService = {
    hasValidIdToken() {
      return true;
    },
    getIdentityClaims() {}
  };
  let holdingsProvider = {
    holdings: [{crypto: 'BTC', currency: 'USD', amount: 5, value: '10000'}],
    loadHoldings() {
      return this.holdings;
    }
  };
  let loadHoldings, getIdentityClaims;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [HomePage],
      imports: [IonicModule.forRoot(HomePage)],
      providers: [NavController,
        {provide: OAuthService, useValue: oauthService},
        {provide: HoldingsProvider, useValue: holdingsProvider}
      ]
    });
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(HomePage);
    component = fixture.componentInstance;
    loadHoldings = jest.spyOn(holdingsProvider, 'loadHoldings');
    getIdentityClaims = jest.spyOn(oauthService, 'getIdentityClaims');
  });

  it('should be created', () => {
    expect(component).toBeDefined()
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个测试使用`jest.spyOn`到*去探查*应该被调用的方法，并验证它们被调用了。如果你想模仿函数并返回假数据，你可以用`jest.fn()`和[模仿函数](https://facebook.github.io/jest/docs/en/mock-functions.html)来实现。

添加测试以确保调用了`HoldingsProvider.loadHoldings()`并显示了货币列表。

```
it('should call loadHoldings', () => {
  component.ionViewDidLoad();
  fixture.detectChanges();
  expect(loadHoldings).toHaveBeenCalled();
  expect(getIdentityClaims).toHaveBeenCalled();
});

it('should show list of currencies', () => {
  component.ionViewDidLoad();
  fixture.detectChanges();
  const list: HTMLDivElement = fixture.debugElement.query(By.css('ion-list')).nativeElement;
  expect(list.innerHTML).toMatch(/ion-item/);
  const amount = fixture.debugElement.query(By.css('.amount')).nativeElement;
  expect(amount.innerHTML).toMatch(/<strong>Coins:<\/strong> 5 <strong>Value:<\/strong> 10000/)
}); 
```

Enter fullscreen mode Exit fullscreen mode

跑`npm test`一切都该过去了。

```
Test Suites: 2 passed, 2 total
Tests:       5 passed, 5 total
Snapshots:   0 total
Time:        5.98s 
```

Enter fullscreen mode Exit fullscreen mode

与其向您展示如何对每个组件进行单元测试，我将向您展示如何编写另一个组件:提供者测试。要测试使用`HttpClient`的
提供者(在常规 Angular 应用中通常称为*服务*，可以使用`HttpTestingController`。创建`crypto-pwa/src/providers/holdings/holdings.spec.ts`,并用下面的代码填充它。

```
import { getTestBed, TestBed } from '@angular/core/testing';
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';
import { HoldingsProvider } from './holdings';
import { OAuthService } from 'angular-oauth2-oidc';

describe('HoldingsProvider', () => {
  let injector: TestBed;
  let provider: HoldingsProvider;
  let httpMock: HttpTestingController;
  let oauthService = {
    hasValidIdToken() {
      return true;
    },
    authorizationHeader() {
      return "random-string";
    }
  };

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [HoldingsProvider,
        {provide: OAuthService, useValue: oauthService}
      ]
    });

    injector = getTestBed();
    provider = injector.get(HoldingsProvider);
    httpMock = injector.get(HttpTestingController);
  });

  afterEach(() => {
    httpMock.verify()
  });

  it('should be created', () => {
    expect(provider).toBeTruthy();
  });

  it('should retrieve holdings', () => {
    const fakeHoldings = [
      {crypto: 'BTC', currency: 'USD', amount: 5, value: '10000'},
      {crypto: 'ETH', currency: 'USD', amount: 100, value: '700'}
    ];

    provider.loadHoldings();
    const req = httpMock.expectOne(provider.HOLDINGS_API);
    expect(req.request.method).toBe('GET');
    req.flush(fakeHoldings);

    expect(provider.holdings.length).toBe(2);
    expect(provider.holdings[0].crypto).toBe('BTC');
    expect(provider.holdings).toEqual(fakeHoldings);

    // calls to get prices
    httpMock.expectOne('https://api.cryptonator.com/api/ticker/BTC-USD');
    httpMock.expectOne('https://api.cryptonator.com/api/ticker/ETH-USD');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

真正的行动通过以下步骤发生:

1.  在本例中，提供者的方法被称为`loadHoldings()`
2.  使用`expectOne()`模拟请求
3.  刷新传递假值的请求

发生这种情况后，你可以相应地设定你的期望。如果你的提供商有任何额外的电话(比如`fetchPrices()`)，你也需要为这些电话设定期望值。`afterEach`函数用于验证没有意外的 HTTP 调用。

**帽子提示:**我从 [Ciro Nunes 用 Angular HttpClient API](https://medium.com/netscape/testing-with-the-angular-httpclient-api-648203820712) 的测试中学到了如何测试使用`HttpClient`的提供者。

运行`npm test`来验证你所有的单元测试都通过了。

既然已经有了单元测试，那么添加一些端到端测试来确保整个应用程序按预期工作。

## 驾驶并用量角器测试你的离子 PWA

量角器推荐[使用页面对象组织测试](https://github.com/angular/protractor/blob/master/docs/page-objects.md)。页面对象通过将关于页面元素的信息封装在单个对象中，帮助您编写更干净的测试。

通过以下步骤对您的应用程序主页进行基本的 e2e 测试:

1)在`crypto-pwa/e2e/pages/app.po.ts` :
创建一个通用的`Page`对象

```
import { browser } from 'protractor';

export class Page {

  navigateTo(destination) {
    return browser.get(destination);
  }

  getTitle() {
    return browser.getTitle();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

2)创建`crypto-pwa/e2e/spec/app.e2e-spec.ts`并定义一个“默认屏幕”测试套件:

```
import { Page } from '../pages/app.po';
import { browser, ExpectedConditions as ec } from 'protractor';

describe('App', () => {
  let page: Page;

  beforeEach(() => {
    page = new Page();
  });

  describe('default screen', () => {
    beforeEach(() => {
      page.navigateTo('/#/home');
    });

    it('should redirect to login', () => {
      browser.wait(ec.urlContains('/#/login'), 5000);
    });

    it('should have the correct title', () => {
      page.getTitle().then(title => {
        expect(title).toEqual('Cryptocurrency PWA with Authentication');
      });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**为了让`getLoginButton()`工作，你需要在`crypto-pwa/src/pages/login/login.html`的登录按钮上添加`id="login"`。

要执行量角器测试，在一个终端运行`ionic serve`，在另一个终端运行`npm run e2e`。[这段(无声)视频](https://www.youtube.com/watch?v=MO_ZWxI7Yi4)展示了应该发生的事情。

您也可以配置量角器启动自己的网络服务器。为此，在[节点-http-服务器](https://www.npmjs.com/package/node-http-server) :
上添加一个 devDependency

```
npm i -D node-http-server@8.1.2 
```

Enter fullscreen mode Exit fullscreen mode

然后修改`crypto-pwa/test/protractor.conf.js`以在端口 8100 上提供`www`目录。

```
const server = require('node-http-server');

exports.config = {
  ...
  onPrepare() {
    require('ts-node').register({
      project: 'e2e/tsconfig.e2e.json'
    });
    server.deploy({port: 8100, root: 'www'});
    jasmine.getEnv().addReporter(new SpecReporter({ spec: { displayStacktrace: true } }));
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这种技术的唯一缺点是，您必须在运行项目之前构建项目(用`npm run build`填充`www`)。无论如何，在运行 e2e 测试之前进行 prod 构建(`npm run build --prod`)是一个好主意，因此这个缺点也可以被视为一个好的实践。

更好的是，您可以通过持续集成实现自动化！我们一会儿就会谈到这一点。首先，创建一个`crypto-pwa/e2e/pages/login.po.ts`，它定义了您需要进行身份验证的元素和方法。

```
import { browser, by, element } from 'protractor';
import { Page } from './app.po';

export class LoginPage extends Page {
  username = element(by.name('username'));
  password = element(by.name('password'));
  oktaLoginButton = element(by.css('input[type=submit]'));
  loginButton = element(by.css('#login'));
  logoutButton = element(by.css('#logout'));
  header = element(by.css('ion-title'));

  getHeader() {
    return this.header.getText();
  }

  setUserName(username) {
    this.username.sendKeys(username);
  }

  getUserName() {
    return this.username.getAttribute('value');
  }

  clearUserName() {
    this.username.clear();
  }

  setPassword(password) {
    this.password.sendKeys(password);
  }

  getPassword() {
    return this.password.getAttribute('value');
  }

  clearPassword() {
    this.password.clear();
  }

  login(username: string, password: string) {
    // Entering non angular site, tell webdriver to switch to synchronous mode.
    browser.waitForAngularEnabled(false);
    this.username.isPresent().then(() => {
      this.username.sendKeys(username);
      this.password.sendKeys(password);
      this.oktaLoginButton.click();
    }).catch(error => {
      browser.waitForAngularEnabled(true);
    });
  }

  clickLoginButton() {
    return this.loginButton.click();
  }

  logout() {
    return this.logoutButton.click();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后用测试创建`crypto-pwa/e2e/spec/login.e2e-spec.ts`,确保登录按钮存在，无效的凭证导致错误，有效的凭证显示欢迎消息，并且您可以成功注销。

```
import { browser, element, by, ExpectedConditions as ec } from 'protractor';
import { LoginPage } from '../pages/login.po';

describe('Login', () => {

  let loginPage;

  beforeAll(() => {
    loginPage = new LoginPage();
    loginPage.navigateTo('/');
    browser.waitForAngular();
  });

  it('should show a login button', () => {
    expect(loginPage.getHeader()).toMatch(/Login/);
    expect(loginPage.loginButton.isPresent());
  });

  it('should fail to log in with bad password', () => {
    loginPage.clickLoginButton();
    loginPage.login('admin', 'foo');
    const error = element.all(by.css('.infobox-error')).first();
    browser.wait(ec.visibilityOf(error), 2000).then(() => {
      expect(error.getText()).toMatch("Sign in failed!");
    });
  });

  it('should log in successfully with demo account', () => {
    loginPage.clearUserName();
    loginPage.setUserName(process.env.E2E_USERNAME);
    loginPage.clearPassword();
    loginPage.setPassword(process.env.E2E_PASSWORD);
    loginPage.oktaLoginButton.click();

    const welcome = /Welcome/; // Use /Welcome, First Last/ if you want to verify full name
    const success = element.all(by.css('h1')).first();
    browser.wait(ec.visibilityOf(success), 5000).then(() => {
      expect(success.getText()).toMatch(welcome);
    });
  });

  it('should log out successfully', () => {
    loginPage.logout();
    browser.wait(ec.urlContains('/#/login'), 2000);
    expect(loginPage.loginButton.isPresent());
  })
}); 
```

Enter fullscreen mode Exit fullscreen mode

`process.env.*`变量提供了一种在环境变量中存储凭证的便捷方式，因此您不会在源代码控制系统中暴露它们。您需要定义`E2E_USERNAME`和`E2E_PASSWORD`环境变量来通过这个测试。这些值应该与 Okta 租户中的有效凭据相匹配。

在您定义它们并验证它们设置正确后，在一个终端中使用`mvn`启动 Spring Boot 应用程序，然后在`crypto-pwa`目录中运行以下命令。

```
npm run build --prod
npm run e2e 
```

Enter fullscreen mode Exit fullscreen mode

当一切都过去了，拍拍自己的背；你正在成为一名经验丰富的打字测试员！

**提示:**量角器使用 Jasmine，它允许您轻松地在运行中包含/排除特定的测试。你可以给你的`describe`或`it`函数加上一个`f`前缀，这样它们就变成了`fdescribe`或`fit`。如果您想要排除测试，您可以给这些相同的代码块添加一个`x`前缀(例如`xdescribe`和`xit`)。

到目前为止，您编写的 e2e 测试的一个问题是，它们没有使用 API 来保存 Okta 的持有量。要创建一个测试该功能的测试，为 Add Holding page 创建一个`crypto-pwa/e2e/pages/add-holding.po.ts` page 对象。这个类有一些方便的方法来填写表单字段并单击 submit 按钮。

```
import { $, by, element } from 'protractor';
import { Page } from './app.po';

export class AddHoldingPage extends Page {
  cryptoCode = element.all(by.css('input[type=text]')).first();
  displayCurrency = element.all(by.css('input[type=text]')).get(1);
  amountHolding = element.all(by.css('input[type=number]'));
  addHoldingButton = element(by.buttonText('Add Holding'));
  pageTitle = $('ion-title');

  setCryptoCode(code) {
    this.cryptoCode.sendKeys(code);
  }

  setCurrency(currency) {
    this.displayCurrency.sendKeys(currency);
  }

  setAmount(amount) {
    this.amountHolding.sendKeys(amount);
  }

  clickAddHoldingButton() {
    this.addHoldingButton.click();
  }

  getPageTitle() {
    return this.pageTitle;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为主页添加一个`crypto-pwa/e2e/pages/home.po.ts`,显示其**、添加硬币**按钮和删除按钮。

```
import { by, element } from 'protractor';
import { Page } from './app.po';

export class HomePage extends Page {
  addCoinsButton = element(by.buttonText('Add Coins'));
  deleteButton = element.all(by.css('button[color=danger]')).last();

  clickAddCoinsButton() {
    this.addCoinsButton.click();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在已经有了这些页面对象，您可以添加一个新的 e2e 测试，登录，添加三个美元的比特币，然后删除持有的比特币。编写这个测试最难的部分是弄清楚如何用量角器向左滑动，所以我建议记下这是如何完成的。

```
import { browser, by, element, ExpectedConditions as ec } from 'protractor';
import { LoginPage } from '../pages/login.po';
import { AddHoldingPage } from '../pages/add-holding.po';
import { HomePage } from '../pages/home.po';

describe('Manage Holdings', () => {

  let loginPage, homePage, addHoldingPage;

  beforeAll(() => {
    loginPage = new LoginPage();
    homePage = new HomePage();
    addHoldingPage = new AddHoldingPage();
    loginPage.navigateTo('/');
    browser.waitForAngular();
  });

  beforeEach(() => {
    loginPage.clickLoginButton();
    loginPage.login(process.env.E2E_USERNAME, process.env.E2E_PASSWORD);
    loginPage.oktaLoginButton.click();

    browser.wait(ec.urlContains('home'), 5000);
  });

  afterEach(() => {
    loginPage.logout();
  });

  it('should add and remove a holding', () => {
    homePage.clickAddCoinsButton();

    browser.wait(ec.urlContains('add-holding'), 1000);

    addHoldingPage.setCryptoCode('BTC');
    addHoldingPage.setCurrency('USD');
    addHoldingPage.setAmount(3);
    addHoldingPage.clickAddHoldingButton();

    // wait for everything to happen
    browser.wait(ec.urlContains('home'), 5000);

    // verify message is removed and holding shows up
    element.all(by.css('.message')).then((message) => {
      expect(message.length).toBe(0);
    });

    // wait for holding to show up
    const addedHolding = element.all(by.css('ion-item')).last();
    browser.wait(ec.presenceOf(addedHolding), 5000).then(() => {

      // delete the holding - https://forum.ionicframework.com/t/move-ion-item-sliding-by-protractor/106918
      browser.actions().mouseDown(addedHolding)
        .mouseMove({x: -50, y: 0})
        .mouseMove({x: -50, y: 0})
        .mouseMove({x: -50, y: 0})
        .mouseUp()
        .perform();

      homePage.deleteButton.click();
      element.all(by.css('.message')).then((message) => {
        expect(message.length).toBe(1);
      });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

参见[okta-ionic-crypto-Java-SDK-example # 5](https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example/pull/5)获取包含所有 UI 测试的 pull 请求。

## 添加与 Travis CI 的持续集成

做测试很好，但你知道什么更好吗？每次为您的项目创建拉请求(PR)时运行它们。您确实使用 Git 将项目存储在源代码控制中，不是吗？！我假设你知道。我是说，如果你在读这篇文章，我知道你是一个聪明的开发者。朋友不让朋友写认证，你来了，就不用了吧？😉

两个最受欢迎的 CI 服务器是特拉维斯 CI T1 和 T2 詹金斯 T3。因为 Travis 不需要任何本地设置，所以我们将快速概述如何使用它来结束这篇文章。

如果你已经将你的项目登记到 [GitHub](https://github.com/) 中，你可以使用 Travis CI。

1.  登录到 [Travis CI](https://travis-ci.org/) 并为您的项目的 GitHub repo 启用构建。
2.  在你的根目录下添加下面的`.travis.yml`，为它创建一个分支(例如`git branch -b ci`，并`git commit/push`它。

```
os:
  - linux
services:
  - docker
language: node_js
node_js:
  - "9.8.0"
addons:
  apt:
    sources:
    - google-chrome
    packages:
    - google-chrome-stable
jdk:
  - oraclejdk8
sudo: false
cache:
  directories:
    - crypto-pwa/node_modules
before_install:
  - jdk_switcher use oraclejdk8
  - java -version
  - export CHROME_BIN=/usr/bin/google-chrome
  - export DISPLAY=:99.0
  - sh -e /etc/init.d/xvfb start
  - npm install -g ionic@3.20.0
script:
  - chmod +x holdings-api/mvnw
  - cd holdings-api && ./mvnw -q clean verify
  - cd ../crypto-pwa && npm i && npm test
  - cd ../holdings-api && mvnw -q spring-boot:run &
  - cd ../crypto-pwa && npm run build --prod
  - npm run e2e
notifications:
  webhooks:
    on_success: always
    on_failure: always
    on_start: false 
```

Enter fullscreen mode Exit fullscreen mode

3)在浏览器中导航到您的 GitHub repo，并使用您刚刚推送的分支创建一个 pull 请求。这将启动 Travis 中的流程来测试您的分支。

上面的`.travis.yml`脚本将使用 Java 8 运行您的 Spring Boot 测试，运行 Jest 测试，启动后端，启动前端，然后运行量角器测试以验证一切正常。

**注意:**您可能会注意到 Java 单元测试和集成测试是用`./mvnw verify`运行的，而不是作为两个独立的命令。这是因为`mvn test`只运行单元测试，而`mvn verify`运行单元测试*和*集成测试。

当 Travis 第一次运行这个脚本时，您可能会看到如下所示的错误。

[![Travis failure](img/9e81350d361fbf4c266d71671ac62a3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VjlZVjrx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/cryptocurrency-pwa-java-sdk-testing/travis-required-keys-3744e30473b6ea6022ced9cc2d15103c2fc37431647d63bb737e8c4f944827ef.png)

这是因为没有设置`E2E_USERNAME`和`E2E_PASSWORD`环境变量。Travis 使得[设置加密的环境变量](https://docs.travis-ci.com/user/environment-variables/#Defining-encrypted-variables-in-.travis.yml)变得很容易。执行以下步骤为您的构建设置这些。

1.  使用`gem install travis`安装`travis`宝石。
2.  在 repo 的根目录中，运行以下命令为用户名、密码和 API 令牌创建加密的环境变量。该命令假设您已经在本地定义了这些值。

```
travis encrypt E2E_USERNAME=$E2E_USERNAME \
    E2E_PASSWORD=$E2E_PASSWORD OKTA_CLIENT_TOKEN=$OKTA_CLIENT_TOKEN --add env.matrix 
```

Enter fullscreen mode Exit fullscreen mode

3)提交并推动这个变更，您的下一个构建应该会成功。

你可以在下面的截图中看到一个[成功构建的](https://travis-ci.org/oktadeveloper/okta-ionic-crypto-java-sdk-example/builds/371729753)。

[![Travis success](img/0a30927d36bd51572f19194e4d323ced.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ltMnEX4N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/cryptocurrency-pwa-java-sdk-testing/travis-success-cf2671318148bc1f84835c62db80c934879229e19039706d9f193dba2bde0e7f.png)

关于添加 Travis 配置的 pull 请求，请参见[okta-ionic-crypto-Java-SDK-example # 4](https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example/pull/4)。

我们鼓励您阅读 Travis CI 的[保护数据安全的最佳实践](https://docs.travis-ci.com/user/best-practices-security/)，以了解更多有关如何避免泄露机密的信息。

## 添加代码覆盖报告

报告测试了多少代码是一个很好的方法，可以看出你有多少技术债务，以及你的应用程序中哪些部分需要更多的测试。

### 添加 JaCoCo Java 代码覆盖库

对于 Java 代码覆盖率，我通常使用 [JaCoCo](https://github.com/jacoco/jacoco) 。它通过添加一个 Java 代理来工作，但是因为它有所有主要构建工具的插件，你通常不需要直接这样做。例如，我已经用下面的`pom.xml`片段配置了这个项目来报告 UT、IT 和总测试覆盖率。

```
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.1</version>
    <executions>
        <execution>
            <id>prepare-agent</id>
            <phase>process-test-classes</phase>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>
        <execution>
            <id>prepare-agent-integration</id>
            <phase>pre-integration-test</phase>
            <goals>
                <goal>prepare-agent-integration</goal>
            </goals>
        </execution>
        <execution>
            <id>jacoco-report</id>
            <phase>verify</phase>
            <goals>
                <goal>report</goal>
                <goal>report-integration</goal>
            </goals>
        </execution>
        <execution>
            <id>jacoco-merge-report</id>
            <phase>verify</phase>
            <goals>
                <goal>merge</goal>
                <goal>report</goal>
            </goals>
            <configuration>
                <dataFile>${project.build.directory}/jacoco-merge.exec</dataFile>
                <outputDirectory>${project.reporting.outputDirectory}/jacoco-merge</outputDirectory>
                <destFile>${project.build.directory}/jacoco-merge.exec</destFile>
                <fileSets>
                  <fileSet>
                    <directory>${project.build.directory}</directory>
                    <includes>
                      <include>*.exec</include>
                    </includes>
                  </fileSet>
                </fileSets>
            </configuration>
        </execution>
    </executions>
</plugin> 
```

Enter fullscreen mode Exit fullscreen mode

id 为`jacoco-merge-report`的最后一位合并了 UT，它报告创建了一个新的报告，包含了总的项目测试覆盖率。如果你正在使用一个 Maven 多模块构建，那就简单多了，你可以使用 [`report-aggregate`](https://www.eclemma.org/jacoco/trunk/doc/report-aggregate-mojo.html) 目标来代替。

### 用 Jest 和伊斯坦布尔覆盖打字稿代码

对于事物的棱角方面，Jest 有内置的代码覆盖。你只需要跑`npm run test:coverage`。你可以从下面的截图中看到，仍然有几个页面需要更多的测试。

[![Jest coverage](img/f55995546c8349b2f9728726039bebbf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cJc2TA9R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/cryptocurrency-pwa-java-sdk-testing/jest-coverage-0edf67f79e80d18ef5538b05ec21d66a52edd81ac2adaadc51371faa851df88b.png)

[伊斯坦布尔](https://istanbul.js.org/)是 JavaScript 生态系统中另一个受欢迎的代码覆盖工具。是 Angular CLI 用的[。如果您在 Angular CLI 生成的项目中，从项目的根目录运行`ng test --sr --cc`以在`coverage/index.html`中生成报告。](https://github.com/angular/angular-cli/blob/master/docs/documentation/stories/code-coverage.md)

### 用 Travis 报告代码覆盖率

对于 Java 代码覆盖率，我一直在使用 [Codecov](https://codecov.io/) ，主要是因为它太容易使用了。在您的`travis.yml`中，您只需添加一个指向您的覆盖率文件的指针:

```
after_success:
 - bash <(curl -s https://codecov.io/bash) -f holdings-api/target/site/jacoco-merge/jacoco.xml 
```

Enter fullscreen mode Exit fullscreen mode

Codecov 对 JavaScript 和 TypeScript 也有极好的支持!要将 Codecov 添加到 Travis for Jest，您需要配置您的`package.json`来收集覆盖率。

```
"jest":  {  "coverageDirectory":  "./coverage/",  "collectCoverage":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

当然，在使用之前，您需要配置 Travis 来安装`codecov`命令行工具。你可以用`npm i -g codecov`或者像上面例子中的 Brian 那样使用`bash <(curl -s https://codecov.io/bash)`。

```
install:
  - npm install -g codecov 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以在一切成功后收集覆盖信息。

```
after_success:
  - cd $TRAVIS_BUILD_DIR && codecov 
```

Enter fullscreen mode Exit fullscreen mode

`codecov`命令足够智能，可以找到 Java 端的`jacoco.xml`文件，以及 Jest 创建的`lcov.info`文件。

```
==> Scanning for reports
    + /home/travis/.../crypto-pwa/public/coverage/lcov.info
    + /home/travis/.../holdings-api/target/site/jacoco/jacoco.xml
    + /home/travis/.../holdings-api/target/site/jacoco-it/jacoco.xml
    + /home/travis/.../holdings-api/target/site/jacoco-merge/jacoco.xml
==> Uploading reports
    Success!
    View report at: https://codecov.io/gh/oktadeveloper/okta-ionic-crypto-java-sdk-example/tree/ee2382623ada72cd78188f4d25318dfaf3d24882 
```

Enter fullscreen mode Exit fullscreen mode

[![Codecov report](img/3eb99af0d8997994d0a79e1e2a46e87e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TGDD_9mZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/cryptocurrency-pwa-java-sdk-testing/codecov-report-a80586a7f60ad4f45266500f6bdc0717f18947c85fa676f85836398c498f66ae.png)

**提示:** Codecov [允许您在代码中添加`unittests`、`integration`和`ui`标志](https://docs.codecov.io/docs/flags)来对测试类型进行分组。

您也可以[配置 Codecov](https://github.com/apps/codecov) 来报告您的 GitHub pull 请求！如果您在上一页没有看到您的组织，您应该能够直接导航到它。比如这个项目的直接链接是[https://code cov . io/GH/okta developer/okta-ionic-crypto-Java-SDK-example](https://codecov.io/gh/oktadeveloper/okta-ionic-crypto-java-sdk-example)。

参见[okta-ionic-crypto-Java-SDK-example # 6](https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example/pull/6)获取添加代码覆盖率的 pull 请求。

## 了解更多关于 Spring Boot 测试和 TypeScript 应用的信息

我们希望你喜欢这个测试 Java APIs 和 Ionic/Angular 应用程序的搭便车指南。当您的代码库具有高测试覆盖率，并且您能够通过调整代码和测试来进行重构时，这种感觉非常好。

你可以在 GitHub 上找到本指南使用的代码:[https://GitHub . com/okta developer/okta-ionic-crypto-Java-SDK-example](https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example)。

如果您想了解更多关于测试 Java 和 Spring Boot API 的信息，请访问:

*   [Spring Boot 测试功能](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html)
*   [定制汉堡匹配器](https://www.javacodegeeks.com/2015/11/custom-hamcrest-matchers.html)
*   [用 Mockito 嘲讽的指南](https://dzone.com/articles/a-guide-to-mocking-with-mockito)

对于角度和离子测试，这里有一些我们认为有用的资源:

*   [Angular 5:模拟测试&间谍](https://codecraft.tv/courses/angular/unit-testing/mocks-and-spies/)
*   [Angular 官方测试指南](https://angular.io/guide/testing)
*   [离子单元测试示例](https://github.com/ionic-team/ionic-unit-testing-example)

如果您对 Ionic 和 JHipster 感兴趣，我们最近为 JHipster 及其关联的 [Ionic JHipster Starter](https://github.com/oktadeveloper/ionic-jhipster-starter) 的 [Ionic 模块增加了单元和 e2e 测试支持。这两个项目都有一些非常复杂的 Travis 构建，确保一切都与 JWT/OIDC 认证和](https://github.com/oktadeveloper/generator-jhipster-ionic) [JHipster](https://www.jhipster.tech) 后端一起工作。

说到复杂的构建，JHipster 的[主构建](https://github.com/jhipster/generator-jhipster/blob/master/.travis.yml)和[附加构建](https://github.com/hipster-labs/jhipster-travis-build/blob/master/.travis.yml)测试超过 54 种组合！

如果你正在寻找一个有很多测试的 Spring Boot + Angular(或 React)示例应用程序，那么 JHipster 是不二之选。它甚至还有一个[持续集成子生成器](https://www.jhipster.tech/setting-up-ci/)，支持 Jenkins、Travis、CircleCI 和 GitLab CI/CD。

如果你想了解更多关于 Spring Boot、Angular、React 和 JHipster 的话题，请关注我们。如果你对这篇文章有任何问题，请在下面留言。

**变更日志:**

*   2018 . 6 . 13:更新将 [serve](https://www.npmjs.com/package/serve) 替换为 node-http-server 并修复了 Travis 脚本中的错别字。参见[oktadeveloper/okta-ionic-crypto-Java-SDK-example # 12](https://github.com/oktadeveloper/okta-ionic-crypto-java-sdk-example/pull/12)中的代码变化。你可以在 [okta/okta.github.io#2123](https://github.com/okta/okta.github.io/pull/2123) 看到这篇文章的改动。