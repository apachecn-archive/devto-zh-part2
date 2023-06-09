# 向科特林& JAX-RS(新泽西)项目添加测试

> 原文：<https://dev.to/baens/adding-tests-to-kotlin--jax-rs-jersey-project-1ail>

*这篇文章最初是在[blog.baens.net](https://blog.baens.net/posts/add-tests-to-jaxrs-setup/)T3 发表在我的博客上的*

我有一篇关于与科特林一起建立 JAX-RS 的博文，但我忽略了如何测试。所以，让我们回去解决这个问题。为此，我将使用 [JUnit 5](https://junit.org/junit5/) 作为测试运行程序，使用 [AssertJ](http://joel-costigliola.github.io/assertj/) 作为断言库，使用 [Mockito](http://site.mockito.org/) 作为嘲讽框架。

让我们快速回顾一下在我创建 JAX-RS 服务时，我发现哪些测试是有价值的。实际上，只有两个:一个测试 HTTP 接口(所以，“集成测试”)，另一个单独测试类和方法(所以，“单元测试”)。我发现更有价值的是告诉系统的哪些部分在测试中得到了锻炼，而不是试图识别它们处于什么类型的通用测试水平。只是一个奇怪的怪癖，你会看到我有很多这样的怪癖。

# 在 gradle 中设置依赖关系

首先，我们需要确保我们使用的是 gradle 4.6 或更高版本。我个人使用包装版本，但只是验证你有它。要获得特定的包装版本，您可以运行`gradle wrapper --gradle-version 4.6`,然后一切都会好的。

对于实际的代码依赖，下面是您在`build.gradle`文件中需要的内容:

```
def junitVersion = "5.1.0"

dependencies {
    // junit dependencies
    testCompile "org.junit.jupiter:junit-jupiter-api:${junitVersion}"
    testRuntime "org.junit.vintage:junit-vintage-engine:${junitVersion}"
    testRuntime "org.junit.jupiter:junit-jupiter-engine:${junitVersion}"

    // jersey test dependencies for HTTP testing
    testCompile "org.glassfish.jersey.test-framework.providers:jersey-test-framework-provider-grizzly2:${jerseyVersion}"

    // assertJ
    testCompile 'org.assertj:assertj-core:3.10.0'

    // mockito
    testCompile 'org.mockito:mockito-core:2.18.3'
    testCompile 'org.mockito:mockito-junit-jupiter:2.18.3'
    testCompile 'com.nhaarman:mockito-kotlin:1.5.0'

} 
```

Enter fullscreen mode Exit fullscreen mode

有点乱，不是吗？这里有一点要注意。任何一个`org.junit`都是您为 JUnit 5 包含的核心 JUnit 库。我们包含了`junit-vintage-engine`，因为对于我们的测试来说，HTTP 测试仍然基于来自`jersey-test-framework-provider`的更老的 JUnit 4 测试。我们稍后将对此进行更深入的探讨。

我将 [AssertJ](http://joel-costigliola.github.io/assertj/) 作为断言库，因为我更喜欢这种语法，而不是现成的 JUnit 5 风格。

[Mockito](http://site.mockito.org/) 是一个模仿框架，还有一些来自`mockito-kotlin`的好东西，可以将`any()`选项变成一个实际的实例，而不是一个`null`对象。而`mockito-junit-jupiter`允许我们用 JUnit 5 运行 Mockito。

# 设置结构

首先，让我们做一点工作来设置反转控制系统，我用的是 JAX-RS 版本(新泽西)，正好是 H2。我们将创建一个可以注入绑定的地方，并确定这些绑定是测试绑定，还是生产绑定。这给了我们一个控制层来控制使用哪个系统，并使测试变得更容易。

H2 的基本绑定实现如下所示:

```
class Bindings : AbstractBinder() {
    override fun configure() {
        // bind(<implementation class>).to(<interface>)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们将所有绑定放在那个`configure`方法中的地方。稍后我们会深入探讨这一点。

现在，让我们实际得到一些有用的东西。让我们创建一个接口，它将返回一个数据列表。所以，大概是这样的:

```
data class HelloJson(val prop1: Int, val prop2: String)

interface DataService {
    fun all() : List<HelloJson>
} 
```

Enter fullscreen mode Exit fullscreen mode

没什么特别的，只是一个返回数据对象列表的方法。

# 关于如何让 IntelliJ 工作的说明

因此，IntelliJ 不喜欢测试文件旁边的源代码。老实说，我发现在我用过的所有语言中，这是一个更好的项目结构。因此，为了使这个工作(虽然很糟糕)你需要在`build.gradle`文件中添加一些特殊的东西:

```
apply plugin: 'idea'

// This is here to fix the intellij's issues with
// the complicated source mappings. Use the 'idea' job
// to generate a intellij project
// Ticket to make this just work out of the box: https://youtrack.jetbrains.com/issue/IDEA-188436
idea {
    module {
        scopes.COMPILE.plus += [ configurations.testCompile ]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这仅适用于 IntelliJ。我将很快在这里尝试测试 Eclipse 并报告结果，但老实说，我已经很多年没碰它了。

# HTTP 测试

我在创建端点时编写的第一个测试之一是，我可以从端点返回一个 200，并返回 JSON 作为内容类型。这些测试将是我的快乐之路测试，所以它应该总能通过。随着对这个端点需求的增长，我将返回并添加一些可能需要的东西，以使这个测试默认通过。这些测试可能是这样的:

```
class `HTTP HelloWorldResource should` : JerseyTest(Application()) {
    @Test
    fun `returns 200`() {
        val statusCode = target("helloWorld").request().get().status
        assertThat(statusCode).isEqualTo(200)
    }

    @Test
    fun `returns JSON type`() {
        val mediaType = target("helloWorld").request().get().mediaType
        assertThat(mediaType).isEqualTo(MediaType.APPLICATION_JSON_TYPE)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

***注释*** :那个`@Test`注释来自 JUnit 4 namepsace，导入看起来是这样的:`import org.junit.Test`。如果您没有这个名称空间，而是使用更新的 JUnit 5 名称空间，测试将不会运行。这是因为`JerseyTest`基类假设某些事情运行 JUnit 5 不再做了(想想静态类设置过程之类的)。如果您将这些类型的测试放在同一个文件中，您可以非常简洁地将 JUnit 4 导入重命名为:`import org.junit.Test as TestV4`以避免混淆。

如你所见，这个测试并没有那么糟糕。这是相当直截了当的，围绕测试的样板文件也不错。让我们来看一个更复杂的绑定例子。

# 设置空绑定作为您的第一个实现

当我开始向我的应用程序添加层时，我通常从我的“上”层向下钻至“下”层。正因为如此，我通常没有一个可以用来测试或做其他事情的实现。我有点喜欢这种方法，因为我可以测试 API，看看我是否真的需要这种抽象，以及我期望什么样的行为。考虑到这一点，通常当我创建一个接口时，我总是会在它旁边放一个空对象。这将使从一些默认行为开始变得更容易，并使以后测试系统变得更容易。

例如，上面定义的`DataService`的空对象可能是这样的:

```
class NullDataService : DataService {
    override fun all(): List<HelloJson> = emptyList()
} 
```

Enter fullscreen mode Exit fullscreen mode

# 方法水平测试

现在我们有了接口，并且它是空对象，假设我们需要向我们的`helloWorld`方法添加功能，以便它从这个服务返回数据。使用 TDD 方法，第一个测试可能只是检查方法返回相同数量的项目。下一个测试可能会确保我们确实有一个项目包含在返回列表中。这些测试相当简单，所以让我们看看它的代码:

```
@ExtendWith(MockitoExtension::class)
class `HelloWorldResource Should` {

    fun createHelloJson(prop1: Int = 1, prop2: String = "test") = HelloJson(prop1, prop2)

    @Mock
    lateinit var mockDataService: DataService

    @InjectMocks
    lateinit var helloWorldResource: HelloWorldResource

    @Test
    fun `returns same number of elements as DataService`() {
        whenever(mockDataService.all()).thenReturn(listOf(createHelloJson(), createHelloJson()))

        assertThat(helloWorldResource.helloWorld()).hasSize(2)
    }

    @Test
    fun `contains item from returned list`() {
        val expectedObject = createHelloJson(prop1 = 2)

        whenever(mockDataService.all()).thenReturn(listOf(expectedObject))

        assertThat(helloWorldResource.helloWorld()).contains(expectedObject)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么需要指出的。我使用`MockitoExtension`来减少测试的设置部分。这允许我们用`@Mock`和`@InjectMocks`注释创建属性。这使得测试类中的重用非常容易。`lateinit`部分是因为 Kotlin 真的希望类显示它们何时被初始化。这条语句向编译器表明，我们稍后将在后台执行它，不用担心。

我还添加了一个助手方法，使创建测试数据更加容易。正如你在顶部看到的，我们有一个创建数据对象的`createHelloJson`函数。然后，如果我们想要覆盖不同的属性，我们可以很容易地(如第二个测试所示)添加我们想要覆盖的属性。这允许对象具有相同的缺省值，并且如果对象增长，可以很容易地添加更多的字段，而不必改变 30 个测试。

# 在我们添加了数据服务构造函数依赖之后，让 HTTP 测试工作起来

现在，如果您继续下去，您会注意到上面的两个测试工作得很好，但是如果您现在运行 HTTP 测试，事情就不一样了。这是因为我们现在要求在运行时从 H2 注入`DataService`。为了解决这个问题，我们需要确保我们的绑定是正确的，所以下面是测试绑定和注入的样子

```
// in our Application.kt file
class TestBindings : AbstractBinder() {
    override fun configure() {
        bind(NullDataService()).to(DataService::class.java)
    }
}

// in our HelloWorldResource.kt file
class HelloWorldResource
@Inject constructor(private val dataService: DataService)

// in our HelloWorldResource.tests.kt file
class `HTTP HelloWorldResource should` : JerseyTest(Application(TestBindings())) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要将`@Inject`属性添加到资源的构造函数中。这将向 Jersey 发出信号，这个构造函数将需要外部依赖，并使用 H2 来找到它们。我们的依赖关系是在我们的`TestBindings`类中定义的，默认为`NullDataService`。然后，我们将测试绑定添加到我们的`JerseyTest`创建中，使这一切工作。

# 包装完毕

现在，如果这些都没有意义，而你想看到它们被组合在一起，那么就去 github 仓库吧，在那里你应该可以看到完整的工作过程。我已经为该数据服务创建了一个静态列表实现，让人们看看实际的实现可能是什么样子。

# 奖励:如何在泽西记录异常

如果你在 github 上查看代码，你会注意到在`Application.kt`文件中的另一个类。这个类挂钩到 Jersey 的事件系统，所以如果一个请求在异常中出错，我们可以观察到它。请注意，这只是为了观察是否发生了异常，而不是对请求做任何事情。如果我们想处理这个问题，我们可以创建一个请求映射器，但是这超出了这篇博文的范围。