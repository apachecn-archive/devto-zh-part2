# 在代码投入生产之前发现安全问题

> 原文：<https://dev.to/omerlh/dynamic-security-tests-made-easy--14ff>

[![cover image](img/9984c0913dca8a93a88bdba07cf6e20a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ryYXDHIk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.solutotlv.com/wp-content/uploads/2017/09/generic_cover.png) 
您是否在您的配置项中运行安全测试？

您可能想知道——运行安全测试到底意味着什么？它是做什么的？安全测试只是测试你的代码是否有已知的漏洞，以确保黑客无法侵入你的系统。这听起来可能很复杂——但实际上，它非常简单。有许多现有的工具可以用来运行安全测试——在这篇博文中，我将介绍其中的一种。

在 Soluto，我们有几个开源项目，最大的是 [Tweek](https://github.com/soluto/tweek) 。Tweek 是一个功能管理解决方案，它允许你做类似于 A/B 测试或使用功能标志的事情(例如，只向你的部分用户开放一个功能，以查看它的行为)。它是我们基础架构的重要组成部分，因此，确保它的安全非常重要。此外，由于它是一个开源项目，所以任何人都可以向它贡献代码——这可能会引入新的漏洞。请继续阅读，了解我是如何轻松地向 Tweek 添加安全测试的——或者如果你想跳过解释，你可以阅读[的 PR](https://github.com/Soluto/tweek/pull/633) 。

## 什么是 Zap，它如何帮助我们

为了构建我们的安全测试，我使用了一个名为 [Zaproxy](https://github.com/zaproxy/zaproxy) 或简称 Zap 的工具。Zap 是由 [OWASP](https://www.owasp.org/index.php/Main_Page) 开发和维护的开源免费黑客工具。Zap 最酷的部分是它有一个[API](https://github.com/zaproxy/zaproxy/wiki/ApiDetails)——这意味着我可以在 CI 中运行它所有的黑客功能。

[![We’ll need a hoodie for our CI, of course](img/c4db409ce8ec154dd96f369eb05568bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BOoaIEXr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.solutotlv.com/wp-content/uploads/2017/11/Screen-Shot-2017-11-09-at-18.40.32-1024x607.png)
Zap 的基本特征是它充当 web 代理，例如，它检查从客户端到服务器的流量。Zap 可以检查请求和响应，并寻找各种安全问题，比如缺少安全头。因此，我可以使用现有的测试(UI 自动化或集成测试)，通过 Zap 代理它们，然后向 Zap 查询警报——让我们看看如何进行！

## 代理 Tweek 的 API 冒烟测试

Tweek 是一个复杂的系统，由许多微服务和 UI 组成。在这篇博文中，我将重点介绍 Tweek 的 API，但是同样的方法也可以应用于 Tweek 的 UI。Tweek 的 API(和集成测试)是在。NET core (C#)。所有的集成测试都使用 HttpClient 发送请求，我们可以很容易地配置一个代理:

```
var baseUrl = Environment.GetEnvironmentVariable("TWEEK_API_URL") ?? "http://localhost:4003";
var proxyUrl = Environment.GetEnvironmentVariable("PROXY_URL");
var handler = new HttpClientHandler();
if (proxyUrl != null)
{
    handler.Proxy = new WebProxy(proxyUrl, false);
}
output.WriteLine($"TWEEK_API_URL {baseUrl}");
var client = new HttpClient(handler);
client.BaseAddress = new Uri(baseUrl);
return new TweekApi(client); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要做的就是运行测试，并在 Zap 中寻找有趣的发现。请记住。NET 不会将请求代理到 localhost——所以这在本地运行 Tweek 时不起作用。

## 汇集一切

Tweek 是一个容器化的应用程序，使用 [Docker](https://www.docker.com/) 开发和部署。由于 Tweek 包含多个微服务，我使用 [Docker-compose](https://docs.docker.com/compose/) 在本地运行 Tweek。Docker-compose 允许您在一个 yaml 文件中定义您需要的所有容器，然后它会处理其余的事情。这是 docker-compose yaml 的相关部分:

```
api:
 image: soluto/tweek-api
 logging:
   driver: "none"
zap:
  image: owasp/zap2docker-bare
  command: zap.sh -daemon -host 0.0.0.0 -port 8090
smoke-tests:
  build:
    context: ../../
    dockerfile: TweekApiSmokeTestDockerfile
  depends_on: 
    - api
    - zap
  working_dir: /repo/tweek
  environment: 
    - TWEEK_API_URL=http://api/
    - PROXY_URL=http://zap:8090
  command: /bin/bash -c "wget --tries 20 --timeout=15 --read-timeout=20 --waitretry=30 --retry-connrefused http://api/status && /src/services/api/Tweek.ApiService.SmokeTests/test.sh" 
```

Enter fullscreen mode Exit fullscreen mode

我省略了所有不相关的服务和测试。你可以在这里看一下完整的文件[(Tweek 有多个 compose 文件，我引用了相关的一个)。在这个片段中，您可以看到我们有 3 个服务:](https://github.com/Soluto/tweek/blob/master/CI/docker-compose.yml)

*   运行 Tweek 的 API 代码的 API(使用 [Tweek 的官方映像](https://hub.docker.com/r/soluto/tweek-api/)
*   冒烟测试(使用测试容器，这是运行我们的测试代码的 [dockerfile](https://github.com/Soluto/tweek/blob/master/TweekApiSmokeTestDockerfile)
*   Zap(使用 Zap 的[官方裸图](https://hub.docker.com/r/owasp/zap2docker-bare/)——小图，CI 理想)。

您可能还注意到，冒烟测试被配置为针对我们的 Tweek API 运行，并使用 Zap 代理请求。 [test.sh](https://github.com/Soluto/tweek/blob/master/services/api/Tweek.ApiService.SmokeTests/test.sh) 脚本只是运行测试，并查询感兴趣的警报——我们很快将深入研究它。
所以现在我们需要做的就是运行:

```
docker-compose up --build --exit-code-from smoke-tests 
```

Enter fullscreen mode Exit fullscreen mode

看着奇迹发生。Docker-compose 将根据测试和 Zap 结果设置退出代码——因此，如果我们有一个 bug 或安全问题，退出代码将被设置为 1——这意味着我们可以使构建失败！万岁！

## 禁用规则

现在让我们来看看结果。当我第一次通过 Zap 运行测试时，我注意到 Zap 有许多警告。大部分是因为缺少了 [X-Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options) 和 [Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control) 头。我认为那些丢失的头对于 Tweek API 来说不是真正的漏洞，所以我想忽略它们。幸运的是，Zap 的 API 允许禁用规则(你可以在这里找到所有的规则)。让我们来看看 test.sh 脚本文件，看看如何:

```
#!/bin/bash
# Abort script on error
set -e
./wait-for-it.sh api:80 -t 4000
./wait-for-it.sh zap:8090 -t 4000
# Disable X-Content-Type scanner - not relevant for API
curl --fail http://zap:8090/JSON/pscan/action/disableScanners/?zapapiformat=JSON\&formMethod=GET\&ids=10021
#Disable Cache control scanner - not relevant for this API
curl --fail http://zap:8090/JSON/pscan/action/disableScanners/?zapapiformat=JSON\&formMethod=GET\&ids=10049
dotnet test 
```

Enter fullscreen mode Exit fullscreen mode

脚本使用 [`wait-for-it.sh`](https://github.com/vishnubob/wait-for-it) 等待 Zap 和 Tweek 的 API 完成加载。然后，我禁用了我在使用 Zap 的 API 之前讨论过的那两条规则。现在，我可以简单地调用`dotnet test`，它将运行我们的冒烟测试，并通过 Zap 代理它们。现在让我们继续下一部分——查询 Zap 的警报，构建失败。

## 用胶水过滤结果

因此，禁用这些规则很容易，但这还不够。有些情况下，您希望忽略或推迟特定的问题，但不想关闭整个规则。Zap 的最后两项发现就是如此。Zap 报告的请求表明使用了 Base64 和 SHA-1——这些是需要手动审查的情况。当我回顾 Tweek 的案例时，我发现这显然是一个假阳性——所以我想忽略这些发现，但不关闭规则。

对我们来说幸运的是，我们有另一个来自 OWASP 的开源项目可以帮助解决这个问题。Glue 旨在简化将安全工具“粘合”到 CI/CD 管道中的过程。它已经支持超过 [15 种安全工具](https://github.com/OWASP/glue/blob/master/TOOLS.md)，包括 Zap。Glue 还支持各种过滤——比如过滤误报。它还支持各种类型的报告，如向吉拉报告。它是开源的，所以你可以很容易地添加更多的过滤器或报告器。例如，你可以向 [Slack](https://github.com/OWASP/glue/pull/70) 报告每个新发现，打开一个 GitHub 问题，或者以 [TeamCity 格式](https://github.com/OWASP/glue/pull/72)报告。

Glue 中的一个内置过滤器是文件过滤器——它会将所有的发现写入一个 JSON 文件，并且您可以为每个发现设置是应该忽略还是推迟。您可以签出 Tweek 的 [`glue.json`](https://github.com/Soluto/tweek/blob/master/services/api/Tweek.ApiService.SmokeTests/glue.json) 文件。你会注意到我将这两个问题标记为忽略。

那么现在让我们来看看`test.sh`文件的结尾:

```
 ruby /usr/bin/glue/bin/glue
  -t zap
  --zap-host http://zap --zap-port 8090 --zap-passive-mode
  -f text
  --exit-on-warn 0
  http://api
  --finding-file-path /usr/src/wrk/glue.json 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我告诉 Glue，我只想运行 Zap，不需要任何其他工具。我向它提供 Zap 正在监听的主机和端口。然后，我可以指定我想要一个文本报告(目前支持的其他报告器是 JSON 和吉拉),并且我想要将任何发现的退出代码设置为 1(0 是最低级别，3 是最高级别)。下一个参数是我们的目标— Tweek 的 API URL (" [http://api"](http://api%22) )。最后一个参数是我们的`glue.json`文件，带有被忽略的结果。
现在，如果我们使用 docker-compose 运行它，退出代码将是 0——没有发现！这意味着我们可以在每次新提交时运行它——知道 Zap 在监视我们。

## 怎么用？

如您所见，这非常简单——您也可以查看我在 Tweek 的 repo 中创建的 [PR](https://github.com/Soluto/tweek/pull/633) 来添加那些安全测试。此外，还有一个[样本回购](https://github.com/Soluto/webdriverio-zap-proxy)演示这一解决方案——使用 OWASP 的漏洞百出的网络应用程序“果汁店”。这份报告展示了如何给[果汁店](https://github.com/bkimminich/juice-shop)添加安全测试——由于它是一个易受攻击的应用程序，因此有一些发现。您可以克隆这个 repo 并使用它来更好地理解这个解决方案是如何工作的。
我希望您现在了解如何轻松地将安全测试添加到您的 CI 中，但是如果有不清楚的地方，请随时联系我。Twitter 是最好的选择，但是你也可以在这里发表评论——我很乐意帮助你将 Zap 整合到你的 CI 中。

Orignaly 发布在 [Soluto 工程博客](https://blog.solutotlv.com/dynamic-security-testing-made-easy/?utm_source=devto)上。