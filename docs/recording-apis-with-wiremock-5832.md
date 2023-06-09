# 用 WireMock 记录 API

> 原文：<https://dev.to/sirech/recording-apis-with-wiremock-5832>

[![Recording the API](img/5877bbc11f8eac5a56c9566e1ade869c.png "Recording the API")T2】](///static/f848aea11cf8e83b7deb3b37a8b8ed63/01e7c/cassette.png)

当应用程序依赖于第三方 API 时，您似乎总是会面临类似的挑战。即使在开发过程中，你也反对集成吗？那么测试呢，如果 API 需要在那里，它们不会变得不可靠吗？

与此同时，如果您决定使用存根，您对应用程序的工作有多大把握？我不记得有多少次我进行了完全绿色的测试，这些测试是基于不测试任何东西的模拟，因为 API 已经以某种方式改变了。

最近我一直在思考这个问题，并试图通过 API 录音来解决这个问题。我从 Ruby world 知道这个想法，具体是 [VCR](https://github.com/vcr/vcr) 。然而这一次，我想在一个 *Java* 项目中使用它，所以我查看了 [WireMock](http://wiremock.org/) 。

## 为什么？

遵循[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)，我们希望尽可能在最低的级别进行大多数测试。单元测试不应该处理网络请求。另一方面，如果我们使用模拟，它们应该接近源代码，最好是基于实际的请求。

如果我们已经有了这些模拟，为什么不把它们用于我们的本地开发呢？我想让我的应用程序启动并快速显示一些东西，同样不需要连接到外部。

而且，我想要自动化。手动编辑`.json`文件会导致错误和过时的数据。我想避免摩擦。

多亏了 *WireMock* ，这个过程非常方便，对 app 几乎透明。需要做一些准备工作，但这在 T2 很容易做到。

## 嘲弄我！

我在 Github 中有这个[库，里面有所有的细节。可以用一张图来概括:](https://github.com/sirech/example-wiremock-recorder)

[![Recording the API](img/a812bc9b0c5598bc41ff097f9ba5a712.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dvuaCwss--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sirech/example-wiremock-recorder/raw/mastimg/diagram.png)

对于示例 API，我采用了这个非常有用的示例 API 。在这个设置中有四个部分需要考虑。

### 生产

`production`模式的 app 直接上 API，就这样。请求是这样的:

```
public List<Todo> todos() {
    ResponseEntity<List<Todo>> response = template.exchange(
            "/todos",
            HttpMethod.GET,
            null,
            new ParameterizedTypeReference<List<Todo>>() {});
    return response.getBody();
} 
```

### 发展

对于开发服务器，我们依赖于存在的某些模拟，我们将通过在启动时启动一个 *WireMock* 服务器来透明地提供服务。在 [Spring Boot](https://spring.io/projects/spring-boot) 项目中，我们使用这个初始化器:

```
@Component
public class MockServerInitializer {
    @Value("${mock.active}")
    boolean active;

    @Value("${mock.port}")
    int port;

    @EventListener
    public void onApplicationEvent(ContextRefreshedEvent event) {
        if(active) {
            WireMockServer server = new WireMockServer(port);
            server.start();
        }
    }
} 
```

对于任何非开发环境，将`mock.active`设置为`false`是很重要的。除此之外，配置指向本地服务器，而不是真正的 API:

```
serverUrl: http://localhost:${mock.port}
mock:
  active: true
  port: 8080 
```

### 单元测试

需要使用这个模拟数据的单元测试将使用开发服务器使用的来自 *WireMock* 的相同模拟。激活模拟就像从`RecordedTest`类继承一样简单，看起来像这样:

```
public abstract class RecordedTest {
    @Value("${record.port}")
    int port;

    WireMockServer server;

    @Before
    public void useMocks() throws Exception {
        server = new WireMockServer(port);
        server.start();
    }

    @After
    public void stopUsingMocks() throws Exception {
        server.stop();
    }
} 
```

继承和`@Before`和`@After`注释也许不是最佳实践，但是它使测试变得非常简洁。定义额外的行为很容易，只需定义额外的 *WireMock* 规则。这样，您可以测试更具体的场景，比如超时，同时保持大部分常规代码非常干净。

### 集成测试

这是一切汇集的地方。我们的集成测试确保到真实 API 的连接正常工作。在这个过程中，交互会被记录下来，这样我们就可以将它存储为应用程序其余部分使用的模拟。要使一个测试成为`RecordingTest`，您需要继承类:

```
public abstract class RecordingTest {
    @Value("${record.port}")
    int port;

    abstract String recordingServerUrl();

    @Value("${record.persist}")
    private boolean persistRecordings;

    @Value("${record.extractBody}")
    private int extractBody;

    private WireMockServer server;

    @Before
    public void setUp() throws Exception {
        server = new WireMockServer(port);
        server.start();
        server.startRecording(config());
    }

    @After
    public void tearDown() throws Exception {
        server.stopRecording();
        server.stop();
    }

    private RecordSpec config() {
        return recordSpec()
                .forTarget(recordingServerUrl())
                .makeStubsPersistent(persistRecordings)
                .extractTextBodiesOver(extractBody)
                .build();
    }
} 
```

这个类将所有请求代理给 real API，并在配置时将它们存储在默认文件夹中。我们想要集成的服务器的 URL 在抽象方法`recordingServerUrl`中定义。通过这种方式，每个测试可以定义不同的端点，并且我们不局限于单个外部端点。

为什么不是每次？我们不希望每次测试运行时都创建新文件。相反，这是一个有意识的决定，由这个脚本目标触发:

```
goal_refresh-recordings() {
  RECORD_PERSIST=true RECORD_EXTRACTBODY=0 ./gradlew clean integration
} 
```

之后，新的录像就出现了，可以使用了。这可以手动完成或集成到管道中，以确保它经常发生。

*注意:*如果*第三方 API* 通过`mTLS`得到保护，您仍然可以通过让 *WireMock* 知道您的代码用来连接它的密钥库，为 *WireMock* 服务器创建一个定制配置:
来使这个设置工作

```
server = new WireMockServer(options()
        .trustStorePath(System.getProperty("javax.net.ssl.keyStore"))
        .trustStorePassword("changeit")
        .port(port)); 
```

## 总结

这个设置有点神奇，但结果使用起来非常简单。这样，您可以在不损害开发体验的情况下实现很大的测试覆盖率，同时保持核心单元测试的精简和快速。如果你以前和不同步的模仿者打过仗，你会看到它的优势！

*编辑 05/08/2019:* 增加了对多个外部 API 的支持