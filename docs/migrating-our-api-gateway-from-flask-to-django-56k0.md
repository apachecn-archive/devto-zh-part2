# 将 API 网关从 Flask 迁移到 Django

> 原文：<https://dev.to/gadventurestech/migrating-our-api-gateway-from-flask-to-django-56k0>

<figure>[![](../Images/45f98d92d505bb1606698cee3fb40bb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zh9sBxtO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AT-NaIlxpxpB0uvmZ.)

<figcaption>[菠萝供应公司](https://unsplash.com/@pineapple?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

的《坐在海洋海岸线上的新鲜菠萝》

在 G Adventures，我们在过去的三年里一直在构建一个可公开访问的 API。我们一直在努力推动 G Adventures API 成为探险旅游行业的标准，我们相信我们正在通过提供卓越的文档&支持，以及为我们的客户提供符合标准的 JSON/XML API，由一套服务和工具提供支持来实现这一目标。

当我们第一次着手构建我们的 API 时，我们希望事情简单一些。我们在 G Adventures 有许多拥有数据的系统，我们希望确保 API 不是数据的所有者，而只是专注于交付数据。

因此，我们着眼于保持事物简单的框架。在 Python 生态系统中，最流行的保持事物简单的框架之一是 Flask。

Flask 是一个不会妨碍你的微框架。它确保您可以编写普通 Python，包括额外的电池。这非常符合我们的需求，因为我们的任务是:

*   构建一个可以捕获单个请求的系统，并将其转换为对源系统的潜在多个请求。实际上，API 网关
*   当谈到 HTTP 标准时，确保我们不会重新发明轮子
*   保持精简和快速。速度很重要。从开发加速和数据交付的角度来看

因此，我们开始迭代我们的 API 原型。弗拉斯克真的没有妨碍我们。几天后，我们就有了一个原型，可以在幕后将一个客户端请求代理成多个请求。我们迭代这个原型，直到我们更接近一个合理的产品。在纯 Python 中构建我们的资源定义，我们能够定义公共模式，并将它们存储为简单的字典。我们很快会谈到今天的情况。

最终，我们发布了我们的 API。成功了！我们有许多内部和外部客户在使用 G Adventures 的数据。我们开始通过提供更多的资源、OAuth、有效载荷验证等工具来扩展我们的系统。当我们开始添加这些工具时，我们将 ORM(通过 SQLAlchemy)和 migrations (Alembic)等概念引入到我们的基础设施中。

这些不是外来的概念，提到的工具也很棒，但是我们已经开始偏离我们的核心基础。我们的 API 在范围上变得越来越大，我们开始重新发明轮子，我们简单的字典定义资源的方法起作用了，但是没有。本质上，我们已经达到了一个点，我们可以确定，如果我们继续建立在这个微框架上，我们将建立在我们的技术债务上。这是一个重新思考的机会

我们开始思考如何前进。我们希望保持 API 层简单，并允许其他微服务提供 G Adventures API 的各种功能，但我们希望有一个一致的、结构良好的想法集，我们可以利用和构建。

因此，我们向 Django Rest 框架挥手致意。Django Rest 框架是拥有 Django 模型的系统的完美伴侣，并且您希望向它们公开一个 API。它如此简单，以至于我们大多数向 API 网关公开 API 的内部系统都使用它。然而，我们的情况不同于典型的 Django 项目。

我们正在积极地运行一个 API 服务，它每周收到数百万次点击，我们希望利用 Django Rest 框架，但需要调整它以适应我们对多个 HTTP 后端服务的需求。本质上，我们必须让 Django Rest 框架按照我们构建 API 的方式工作。让我们看看那是什么样子。

**机器运行时迁移**

在 API 上，我们支持 90 种资源，每种资源都有不同的复杂程度。一个后端上的只读资源比使用多个后端的读写资源更容易测试。归根结底，同时迁移 5 个以上的简单资源是自找麻烦。我们的决定是一次迁移几个，并将请求代理到适当的进程(Flask 或 DRF)。这使得我们可以缓慢地迁移，在出现错误的情况下与我们的客户沟通，并在需要时轻松地路由回 Flask。

能够恢复使用 Flask 对我们很有帮助。您可以完全按照文档来迁移资源，但这并不能保证成功。Django Rest 框架可以拒绝请求，而 Flask 框架不会。比如 Flask 会愉快地处理:

```
POST to /bookings POST to /bookings/resource 
```

而 Django Rest 框架将只接受:

```
POST to /bookings 
```

我们的文档总是说使用预订。我们的文档中没有禁止预订/资源，只是没有提到。我们的一个客户使用预订/资源，它通过了，他们继续使用它。当我们将预订迁移到 DRF 时，客户给我们发了消息。弗拉斯克忽略了这个错误，但 DRF 没有。嗯，我们很惊讶预订/资源在我们自己的 API 上是一个有效的请求。我告诉客户删除我们所有的资源。现在想象一下，我们一次迁移了所有 90 个资源——所有使用 <resource>/resource 的集成都是可悲的。</resource>

**定制我们的端点**

使用 Django Rest 框架，通过将端点绑定到视图来服务资源。例如，要在 API 上提供预订服务，我们只需注册它:

```
router.register(bookings, BookingView) 
```

使用 DRF 通用路由器，这将支持以下请求:

```
bookings/\<booking\_id\> 
```

我们的 Flask 支持其他 URL 变体，我们的挑战之一是正确路由所有现有的请求。以下是我们需要提供的服务:

```
\<resource\>/\<id\>/ \<resource\>/\<id\>/\<resource\_2\> \<resource\>/\<id\>/\<variation\_id\> \<resource\>/\<id\>/\<variation\_id\>/\<resource\_2\> 
```

首先我们发现了 DRF 是如何解析 URL 的:[https://github . com/encode/django-rest-framework/blob/1c 53 FD 32125 b 4742 CDB 95246523 f1 CD 0 c 41 c 497 c/rest _ framework/routers . py](https://github.com/encode/django-rest-framework/blob/1c53fd32125b4742cdb95246523f1cd0c41c497c/rest_framework/routers.py#L230)[# L241。](https://github.com/encode/django-rest-framework/blob/master/rest_framework/routers.py#L240)

这意味着我们的预订/ <id>将通过 DRF 的默认 regex:
有效</id>

```
base\_regex=’(?P\<{lookup\_prefix}{looking\_url\_kwarg}\>{lookup\_value})’ 
```

URL 是在 get_urls 中构造的，我们可以很容易的改写这个函数:

```
def get\_urls(self): urls = super().get\_urls() return urls + \<our custom urls\> 
```

我们的自定义网址会照顾:

```
\<resource\>/\<id\>/\<resource\_2\> \<resource\>/\<id\>/\<variation\_id\> \<resource\>/\<id\>/\<variation\_id\>/\<resource\_2\> 
```

假设我们有一个 ID 为 2222 的路线资源，一个 ID 为 3333 的可能变体，以及一个嵌套的路线地图资源。这些都是有效的请求:

```
Variation 1: itineraries/2222/3333 Variation 2: itineraries/2222/itinerary\_maps Variation 3: itineraries/2222/3333/itinerary\_maps 
```

首先我们重写了这个方法[https://github . com/encode/django-rest-framework/blob/1c 53 FD 32125 b 4742 CDB 95246523 f1 CD 0 c 41 c 497 c/rest _ framework/routers . py # L23](https://github.com/encode/django-rest-framework/blob/1c53fd32125b4742cdb95246523f1cd0c41c497c/rest_framework/routers.py#L230)0 添加额外的正则表达式，比如:

```
\<base\> + "/\*(?P\<variation\_id\>[^/]\*)" 
```

注意，在我们的 get_urls 方法中，我们在创建嵌套 URL 之前调用了 super()。超级()。get_urls()调用 self.get_lookup_regex(viewset)，它在构造嵌套 URL(即以<resource_2>结尾的 URL)之前构造我们的 variation _ ids。</resource_2>

对 super()的调用创建了:

```
itineraries/2222 itineraries/2222/3333 
```

在创建嵌套的 URL 时，我们添加了这些 URL，同时也创建了:

```
itineraries/2222/itinerary\_maps itineraries/2222/3333/itinerary\_maps 
```

通过有序地构造我们的正则表达式，我们能够创建这些自定义 URL 并将它们绑定到适当的视图。

**路由我们的后端**

我在本文中提到过几次后端，但从未正式定义过它们。后端是我们称之为向 API 提供数据的内部系统之一。我们的预订系统提供预订，我们的销售团队提供联系数据，我们的运营工具提供旅游数据等等。这意味着我们的 API 必须知道哪个后端用于哪个资源。我们通过定义一组后端和绑定资源来做到这一点(示例):

```
class Backend: config\_variable = 'B1'

def request: \<initializes a request with the system\> def get\_object: \<uses request to get an object\> def update\_object: \<uses request to update an object\> def create\_object: \<uses request to create an object\>

class Resource: object\_backends = [(READ\_WRITE, 'B1'), (READ\_ONLY, 'B2')] 
```

上面声明了从 B1 读写的资源，然后使用 B2。注意顺序，如果字段冲突，B2 实际上会覆盖来自 B2 的数据。现在我们只需要修补 DRF 来使用这个结构，这很容易。这里只是要覆盖的函数:[https://github . com/encode/django-rest-framework/blob/7078 AFA 42 c 1916823227287 f 6a 6 f 60 c 104 ebe 3 CD/rest _ framework/generics . py # L77](https://github.com/encode/django-rest-framework/blob/7078afa42c1916823227287f6a6f60c104ebe3cd/rest_framework/generics.py#L77)。我们对 update_object 和 create_object 做了同样的事情，允许我们为资源选择后端。我们的 Flask 应用程序以同样的方式运行，但是这个功能的代码要复杂得多。

**API 的现在和未来**

我们的 DRF 框架已经为所有资源服务了一年多，我们非常高兴。虽然 Flask 最初为我们提供了很好的服务，但我们最终发现自己在重新发明轮子。使用 Flask，所有的 API 基础都必须从头开始。有了 DRF，我们获得了基本的功能和添加我们自己的简单方法。一个结构化的框架允许我们专注于实际的 API，同时允许我们稍微定制框架。DRF 足够灵活，给了我们无限的可能性。我们目前正在定制 API，将不同的请求路由到不同的预订系统，敬请关注！

*继续关注我们的* [*Medium*](https://tech.gadventures.com/) *和*[*Twitter*](https://twitter.com/gadventurestech)*获取更多科技世界片段据 G！*

*想帮助 G 大冒险改变人们的生活，环游世界？* [*查看我们所有的职位，今天申请*](https://gadventures.com/careers) *。*

* * *