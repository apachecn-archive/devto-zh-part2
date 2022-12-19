# 开源 apiron:用于声明性 RESTful API 交互的 Python 包

> 原文：<https://dev.to/ithaka/open-sourcing-apiron-a-python-package-for-declarative-restful-api-interaction-5eo5>

这篇文章最初出现在 Build Smarter 上的[。](https://medium.com/build-smarter/open-sourcing-apiron-1f2010393675)

* * *

在 ITHAKA，我们的 web 团队编写应用程序，每个应用程序都与大量服务交互——有时多达 10 个。每个服务都提供了多个端点，每个端点都有自己的一组路径变量和查询参数。

从多个服务收集数据已经成为 web 应用程序开发人员的一项普遍任务。复杂性可能会快速增长:用多个参数集调用一个 API 端点，调用多个 API 端点，在多个 API 中调用多个端点。虽然业务逻辑可能会变得复杂，但是与这些 API 交互的代码却不会。

我们不久前为低级 HTTP 交互创建了一个模块，并在我们的代码库中使用它。但是，在很长一段时间内，每个服务调用的实际细节——服务名、端点路径、查询参数——分散在代码中。这不可避免地导致了重复和一两个错误，当我们在一个地方做了更新，而忘记了另一个地方。

为了减少由此带来的痛苦，我们最终对这些分散的配置进行了评估，并将它们集中在一个注册模块中。这个模块本质上包含了我们与之交互的所有服务的巨大字典:

```
service_endpoints = {
    'CONTENT_SERVICE': {
        'SERVICE': 'content-service',
        'METADATA_ENDPOINT': '/content/{id}',
        'CITATION_ENDPOINT': '/citation/{citation_type}/{id}',
    },
    'SEARCH_SERVICE': {
        'SERVICE': 'search',
        'SEARCH_ENDPOINT': '/search',
        'EXCERPTS_ENDPOINT': '/excerpt?contentId={content_id}',
    },
    ...
} 
```

每个服务都有一个包含用于发现主机的服务名称的`'SERVICE'`键，以及一些描述端点及其参数的`'*_ENDPOINT'`键。调用这些服务看起来像这样:

```
from http import make_get_request_with_timeout
from services.registry import service_endpoints

CONTENT_SERVICE = service_endpoints.get('CONTENT_SERVICE', {})
METADATA_ENDPOINT = CONTENT_SERVICE.get('METADATA_ENDPOINT', '')

# determine content_id... 
metadata = make_get_request_with_timeout(
    service_name=CONTENT_SERVICE.get('SERVICE'),
    endpoint=METADATA_ENDPOINT.format(id=content_id),
    headers={'Accept': 'application/json'},
    request_timeout=5,
) 
```

如您所见，这些端点有多种形状。这解决了跨代码库的重复问题，但我们仍然面临着这种方法的几个问题:

1.  字符串作为端点描述符不会产生结构化数据。这很难自省或验证。
2.  即使是完全格式化的字符串，有时调用也需要排除所有参数，或者添加一个新参数。这不得不在事后临时进行。
3.  我们的 HTTP 模块仍然有一个方法清单，每个方法都有稍微不同的行为和不清楚的名字，比如`make_get_request_fast`(*有多快？).这些方法中有许多用不同的默认参数调用相同的底层方法，有时堆栈会变得很深。选择正确的通话方式很难。*

 *为了解决行为的高度可变性和缺乏结构化数据的问题，我们构建了一个新的 HTTP 交互范例，为配置服务提供了一个声明性接口。我们想从中得到一些东西:

1.  代码描述了服务交互的外观，而不是如何进行底层 HTTP 调用的细节。
2.  端点描述符是结构化的，并且支持自省。
3.  默认行为可以在服务配置中声明，但也可以在调用时轻松地动态覆盖。

带着这些愿望，[我们想出了`apiron`](https://github.com/ithaka/apiron) 。对于`apiron`,上面的相同定义看起来更像这样:

```
from services import IthakaDiscoverableService
from apiron.endpoint import Endpoint

class ContentService(IthakaDiscoverableService):
    service_name = 'content-service'

    metadata = Endpoint(path='/content/{id}')
    citation = Endpoint(path='/citation/{citation_type}/{id}') 
```

调用服务的代码看起来更像这样:

```
from apiron.client import ServiceCaller, Timeout
from services import ContentService

CONTENT_SERVICE = ContentService()

# determine content_id... 
metadata = ServiceCaller.call(
    service=CONTENT_SERVICE,
    endpoint=CONTENT_SERVICE.metadata,
    path_kwargs={'content_id': content_id},
    headers={'Accept': 'application/json'},
    timeout_spec=Timeout(read_timeout=5),
) 
```

我们现在可以定义`ContentService`看起来像什么，并且当我们需要理解它的形状时，可以很容易地引用这个类。服务发现现在是一个插件系统。可以对端点进行自省，并对其参数进行验证和实施。

有了`apiron`,我们已经能够快速轻松地替换我们现有的许多服务呼叫。代码变得更加清晰，随着认知负荷的消除，我们可以开始关注其他收益，如流响应和数据压缩。我们过得很好，我们也想让你过得更好。

你可以用`pip`(或者你喜欢的包管理器)从 PyPI 安装`apiron`:

```
$ pip install apiron 
```

包中还有一些其他有用的工具，所以今天就试试吧！

## ![](../Images/375dfcc32199b4dedf2b526645c27ff7.png) [伊萨卡](https://github.com/ithaka) / [阿皮龙](https://github.com/ithaka/apiron)

### 🍳apiron 是一个 Python 包，它帮助您为 RESTful APIs 编写一个美味的客户机。只是不要用肥皂洗。

<article class="markdown-body entry-content p-5" itemprop="text">

# 停机坪

[![Documentation Status](../Images/2b820c93deb990f549dcf38075f68186.png)](https://apiron.readthedocs.io/en/latest/?badge=latest)[![PyPI version](../Images/1c03b04ced91a5b97d95fa46fd4379d7.png)](https://badge.fury.io/py/apiron)[![Build Status](../Images/56473bc1a6bc0f76f4147c6d391d7205.png)](https://travis-ci.org/ithaka/apiron)

`apiron`帮助您为 RESTful APIs 设计一个美味的客户端。只是不要用肥皂洗。

[![Pie in a cast iron skillet](../Images/1589d3e3e00fba74e2eef1c36e2c26ed.png)T2】](https://github.com/ithaka/apiron/raw/master/docs/_static/cast-iron-skillet.png)

对于 web 应用程序开发人员来说，从多个服务收集数据已经成为一项无处不在的任务。调用具有多个参数集的 API 端点，调用多个 API 端点，调用多个 API 中的多个端点，这种复杂性会迅速增加。虽然业务逻辑可能会变得复杂，但是与这些 API 交互的代码却不会。

`apiron`为服务和端点提供声明性的结构化配置，并提供统一的接口与它们进行交互。

## 定义服务

服务定义需要一个域和一个或多个与之交互的端点:

```
from apiron import JsonEndpoint, Service

class GitHub(Service):
    domain = 'https://api.github.com'
    user = JsonEndpoint(path='/users/{username}')
    repo = JsonEndpoint(path='/repos/{org}/{repo}')
```

## 与服务交互

一旦您的服务…

</article>

[View on GitHub](https://github.com/ithaka/apiron)*