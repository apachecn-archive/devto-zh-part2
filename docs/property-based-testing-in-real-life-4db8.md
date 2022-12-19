# 现实生活中基于属性的测试

> 原文：<https://dev.to/quii/property-based-testing-in-real-life-4db8>

[假设您已经阅读了 Go](https://dev.to/quii/property-based-testing-in-go--2h1p) 中的基于属性的测试介绍

你在网上看到的大多数基于属性的测试例子都是虚构的场景，人们可能会对它们不屑一顾，声称它们实际上并不实用。这篇文章将有一个简单的例子应用到我的一个项目中，希望能说明它们的价值。

尽管这些例子在 Go 中，但是大多数主流编程语言都有 [QuickCheck](https://en.wikipedia.org/wiki/QuickCheck) 框架，这里描述的概念仍然适用。

这篇文章将:

*   简要解释领域
*   描述编写一个真实的基于属性的测试
*   总结我的结论

## 学舌鸟服务器

在编写基于属性的测试时，对领域有一个好的了解是很重要的，下面是我们将要测试的领域的概述。

mockingjay-server (MJ)是一个应用程序，它是一个轻量级的 HTTP 服务器，由配置驱动。

```
 - name: My very important integration point
   request:
     uri: /hello
     method: POST
     body: Chris
   response:
     code: 200
     body: '{"message":  "hello,  Chris"}'
     headers:
content-type: application/json 
```

Enter fullscreen mode Exit fullscreen mode

MJ 有两种“模式”:

#### 服务器模式

提供与您定义的请求相匹配的响应。

#### 兼容或 [CDC](http://martinfowler.com/articles/consumerDrivenContracts.html) 模式

采用相同配置，针对给定的 URL 执行每个请求，并检查响应是否与配置中的响应兼容。这被称为*消费者驱动的契约* (CDC)。

### 为什么？

当编写针对 HTTP 服务的集成测试时，您通常会创建一个伪服务器(或 HTTP 周围的某种存根)来编写测试。然后你会让你的测试通过，并感到高兴，对不对？

但是:

*   伪装并不总是与真实行为相符
*   假货一开始可能行为正确，但是如果下游服务改变，假货可能是错误的

在这两种情况下，你的构建将是绿色的，但是你的软件是坏的。

mockingjay 允许您制作一个服务器，并轻松验证它是否等同于您正在测试的对象。然后，您可以将这个配置作为 CDC 分发给下游服务的维护者，这样他们就不会意外地破坏您的用例的服务。[维基对此进行了更深入的探讨](https://github.com/quii/mockingjay-server/wiki/Rationale)。

> HTTP 集成测试和消费者驱动的契约之间有一种内在的耦合。MJ 在单一配置中利用了这一点。

## 学舌鸟的一种属性

要编写一个基于属性的测试，我们需要确定一个属性来抛出大量自动生成的数据，以确保该属性为真。

MJ 的一个属性是:

> MJ 应该永远和自己兼容

我的想法是:

*   **给定**配置*一*
*   **当**你用配置 *A* 启动一个 MJ 服务器
*   **和**您使用配置*和运行 MJ 服务器的 URL 运行 CDC 检查*
*   **然后**CDC 检查应该通过

如果没有，这意味着 CDC 算法或服务器的行为方式存在缺陷。

我有很多基于测试的例子，但如果我花时间写一个基于属性的测试，我真的可以相信 MJ 正在工作。

## 创建发电机

对于除基本类型之外的所有类型，您都需要在测试中为输入类型创建一个`Generate`方法。这将允许`quickcheck`包创建数千个不同的数据点来检查属性。

当读取 YAML 文件时，每个端点都被读入一个名为`FakeEndpoint`的类型

我首先为`FakeEndpoint`做了一个简单的生成器，其他的 HTTP 东西比如标题、表单等等可以在以后添加。

```
func (r FakeEndpoint) Generate(rand *rand.Rand, size int) reflect.Value {
    randomMethod := httpMethods[rand.Intn(len(httpMethods))]

    req := Request{
        Method: randomMethod,
        URI:    "/" + randomURL(rand.Intn(maxURLLen)),
    }

    res := response{
        Code: rand.Intn(599-100) + 100,
    }

    return reflect.ValueOf(FakeEndpoint{
        Name:     "Generated",
        Request:  req,
        Response: res,
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试属性

*   写一个函数作为我们的断言。它将从`quick`包中随机生成`FakeEndpoint`并检查我们的属性是否为真。
*   使用随机端点启动 MJ 服务器
*   采用相同的配置，对服务器运行 CDC
*   如果有任何错误，则检查失败

```
func TestItIsAlwaysCompatibleWithItself(t *testing.T) {

    compatabilityChecker := NewCompatabilityChecker(noopLogger, httpTimeout)

    assertion := func(endpoint FakeEndpoint) bool {

        // Start an MJ server with the random configuration
        mjSvr := NewServer([]FakeEndpoint{endpoint}, false, ioutil.Discard)
        svr := httptest.NewServer(http.HandlerFunc(mjSvr.ServeHTTP))
        defer svr.Close()

        // Run CDC against "itself". An MJ server should always be compatible with itself.
        errors := compatabilityChecker.check(&endpoint, svr.URL)

        if len(errors) > 0 {
            t.Log("It wasn't compatible with itself")
            for _, err := range errors {
                t.Log(err)
            }
        }

        return len(errors) == 0
    }

    config := quick.Config{
        MaxCount:1000,
    }

    if err := quick.Check(assertion, &config); err != nil {
        t.Error(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我进行测试时，我惊喜地发现疾控中心的检查*没有通过*。

`Couldn't reach real server: Post http://127.0.0.1:41006/snipped-really-long-random-url: 303 response missing Location header`

这意味着 CDC 试图发布到配置的 URL，而 Go 的 HTTP 客户端返回了一个错误。

这很令人兴奋，我还没有看到 MJ 配置任何 3xx 响应代码，所以这表明我的代码有些幼稚。

HTTP 303 是“See other ”,它依赖于位置头来重定向到另一个资源。

### 调查 Go 的 HTTP 客户端

在为此写任何代码之前，我想看看 [`http.Client`](https://golang.org/src/net/http/client.go?s=6486:6538#L171) 的 Go 源代码，看看它到底是如何工作的。

通过跟随一些函数调用，我们可以看到它是一个`POST`还是一个`PUT`，状态是一个 HTTP `Found 302`还是`See Other 303`，然后它将期待一个可以用`req.URL.Parse`解析的位置头。

我必须在配置中添加一些额外的验证，这样这些规则才能被遵守*或者*使 Go 的 HTTP 客户端不跟随重定向。

## 结论

编写基于属性的测试的成本很低(这花费了我大约 10 分钟的时间),并且可以帮助你对你的代码很有信心(或者没有！).

最棒的是，即使在一个非常简单的生成器实现上，测试也发现了一些错误。

这种测试方式可以:

*   提高对问题领域的了解
*   找到你不会想到的错误

这些事情证明了基于属性的测试可以提高你的软件质量。所以，找出如何用你最喜欢的编程语言做这种测试，并尝试一下，你可能会学到一些东西。

### 脚注

现实世界中这种测试方式的其他例子:

[快速检查 Riak](https://skillsmatter.com/skillscasts/4505-quickchecking-riak)

> 在这个演讲中，约翰·休斯向我们展示了 QuickCheck 如何帮助我们模拟 Riak 的行为，提高理解和揭示偶尔的错误。

[约翰·休斯——测试困难并保持理智](https://www.youtube.com/watch?v=zi0rHwfiX1Q)

> 用 3k 行规格说明创建 20 行快速检查来测试来自 6 个不同供应商的 100 万行代码。这个视频里的一些真实的战争故事。