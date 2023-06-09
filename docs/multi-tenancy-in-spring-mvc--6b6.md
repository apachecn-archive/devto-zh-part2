# Spring MVC 中的多租户

> 原文：<https://dev.to/brightdevs/multi-tenancy-in-spring-mvc--6b6>

我们的一个客户的目标是用基于云的 SaaS 模式解决方案取代旧的、通常基于 DOS 的销售点系统。[在 Bright Inventions](//%7B%7B%20site.url%20%7D%7D) 我们开发了所有必需的组件，包括基于 AWS 的后端处理来自多个客户的请求。使用 SaaS 销售点的每个企业都可以被视为多租户环境中的一个租户。在开发多租户应用程序时涉及到许多方面，其中[数据隔离和分区是讨论最多的话题](http://blog.memsql.com/database-multi-tenancy-in-the-cloud-and-beyond/)。然而，今天我想集中讨论计算和资源隔离方面。

[![Multiple consumers](img/fa4d9b4c4d7f908f12744e24dc71ece9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h993OPY4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gwg9bh5wb4i70rghwzlh.jpg)

## 受控资源使用情况

在所讨论的情况下，每个租户都有多个基于 iOS 的 API 客户端。确切的数字从一个到几十个不等。每个 iOS 应用程序将在整个销售日持续打开，并针对后端 API 执行频繁的请求。在 iOS 应用程序中，有一段代码会定期频繁地轮询服务器上的数据变化。不幸的是，一个错误通过了代码审查，导致应用程序每秒钟向服务器请求 50 次更改，而不是半分钟一次。该错误导致单个 API 客户端发出的 API 请求激增，吞吐量比预期大 10 到 100 倍。更糟糕的是，bug 增加轮询频率的速度超过了后端扩展策略。很快，所有的请求处理线程都忙于处理只由一小部分 API 客户机发出的请求。

在多租户应用程序中，需要特别注意防止租户“A”影响租户“B”的操作，即使是间接影响。我们未能满足 CPU/线程池级别的要求，这导致了支持热线的火爆。

## 反向代理请求速率限制

想到的第一个解决方案是应用每个 API 客户机请求速率限制。事实上，这种解决方案非常普遍，在许多服务器中都可以作为配置选项使用。例如在 NGINX 中你可以做 :

```
limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;

server {
    location /login/ {
        limit_req zone=mylimit burst=20;

        proxy_pass http://my_upstream;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面只允许来自同一个 IP 地址的每秒 10 个请求。任何以较高速率进入的请求都将排队到指定的容量(`burst=20`)。任何超过限制的请求都会被拒绝，并显示 503 状态代码。

nginx 方法经过了实战检验，非常容易应用。最好不要使用 IP 地址，而是通过租户标识符对请求进行分组。但是，要准确地确定哪个租户发出请求可能并不容易，除非请求头中的信息很容易获得。就此而言，最好考虑使用自定义 HTTP 头发送 API 客户端标识。例如，如果 API 客户端提供了`X-Tenant-Id: tenant.1`，您可以将其用作`limit_req_zone $http_x_tenant_id zone=mylimit:10m rate=10r/s;`。使用 JWT 时，您通常可以通过解析`Authorization`头值来确定*是谁*发出请求。

## 春季 MVC 请求速率限制

在反向代理级别应用请求速率限制通常是不可行的。在这种情况下，我们可以在 Spring MVC 应用程序中应用限制。首先，你可以尝试起诉 Servlet 过滤器。[有几种解决方案](https://stackoverflow.com/questions/10127472/servlet-filter-very-simple-rate-limiting-filter-allowing-bursts)可用，包括作为码头项目一部分的[剂量过滤器。](https://www.eclipse.org/jetty/javadoc/9.4.7.v20170914/org/eclipse/jetty/servlets/DoSFilter.html)

使用现成的 Servlet 过滤器通常就足够了，尤其是当可用的定制选项满足我们的需求时。然而，对于我们的客户，我们希望限制取决于客户的*大小*。换句话说，你买的服务越多，你能得到的资源就越多。此外，我想有一个在**一个控制器动作**级别的细粒度控制。令我惊讶的是，使用[异步处理器](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/AsyncHandlerInterceptor.html)并不容易实现这样的行为。幸运的是，我确实找到了一种方法，通过混合使用可扩展性点和 hacks 来获得想要的结果。

第一步是定制 Spring MVC 使用的 [`RequestMappingHandlerAdapter`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/RequestMappingHandlerAdapter.html) ，将`@RequestMapping`注释转换成 handler 类。Kotlin 中的以下配置类正好实现了这一点:

```
@Configuration
class WebMvcConfiguration : DelegatingWebMvcConfiguration() {

    @Autowired(required = false)
    private val mvcProperties: WebMvcProperties? = null

    @Inject lateinit var reactiveRequestCommandFactory: ReactiveRequestCommandFactory
    @Inject lateinit var reactiveRequestsProperties: ReactiveRequestsConfiguration.RequestsProperties

    @Bean
    override fun requestMappingHandlerAdapter(): RequestMappingHandlerAdapter {
        //copy pasted from WebMvcConfigurationSupport
        val argumentResolvers = ArrayList<HandlerMethodArgumentResolver>()
        addArgumentResolvers(argumentResolvers)

        val returnValueHandlers = ArrayList<HandlerMethodReturnValueHandler>()
        addReturnValueHandlers(returnValueHandlers)

        val adapter = RateLimitingRequestMappingHandlerAdapter(reactiveRequestCommandFactory, reactiveRequestsProperties)

        adapter.setContentNegotiationManager(mvcContentNegotiationManager())
        adapter.messageConverters = messageConverters
        adapter.webBindingInitializer = configurableWebBindingInitializer
        adapter.customArgumentResolvers = argumentResolvers

        adapter.customReturnValueHandlers = returnValueHandlers

        val requestBodyAdvices = ArrayList<RequestBodyAdvice>()
        requestBodyAdvices.add(JsonViewRequestBodyAdvice())
        adapter.setRequestBodyAdvice(requestBodyAdvices)

        val responseBodyAdvices = ArrayList<ResponseBodyAdvice<*>>()
        responseBodyAdvices.add(JsonViewResponseBodyAdvice())
        adapter.setResponseBodyAdvice(responseBodyAdvices)

        configureAsync(adapter)

        adapter.setIgnoreDefaultModelOnRedirect(mvcProperties?.isIgnoreDefaultModelOnRedirect != false)
        return adapter
    }

    private fun configureAsync(adapter: RequestMappingHandlerAdapter) {
        //expose field publicly
        val configurer = object : AsyncSupportConfigurer() {
            public override fun getTaskExecutor() = super.getTaskExecutor()
            public override fun getTimeout() = super.getTimeout()
            public override fun getCallableInterceptors() = super.getCallableInterceptors()
            public override fun getDeferredResultInterceptors() = super.getDeferredResultInterceptors()
        }

        configureAsyncSupport(configurer)

        if (configurer.taskExecutor != null) {
            adapter.setTaskExecutor(configurer.taskExecutor)
        }

        if (configurer.timeout != null) {
            adapter.setAsyncRequestTimeout(configurer.timeout!!)
        }

        adapter.setCallableInterceptors(configurer.callableInterceptors)
        adapter.setDeferredResultInterceptors(configurer.deferredResultInterceptors)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们正在注入`reactiveRequestCommandFactory`和`reactiveRequestsProperties`，并将它们传递到我们的核心`RateLimitingRequestMappingHandlerAdapter`。所有其他代码大多是从`DelegatingWebMvcConfiguration`基类复制粘贴而来的。

```
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION)
annotation class RequestCommand(
    val enabled: Boolean = true,
    val timeoutInMillis: Int = 60_000
)

class RateLimitingRequestMappingHandlerAdapter(private val reactiveRequestCommandFactory: ReactiveRequestCommandFactory,
                                               private val reactiveRequestProperties: ReactiveRequestsConfiguration.RequestsProperties) : RequestMappingHandlerAdapter() {
    private val handlerMethodConfigurationsCache = ConcurrentHashMap<HandlerMethod, RequestCommandConfiguration>()

    override fun createInvocableHandlerMethod(handlerMethod: HandlerMethod): ServletInvocableHandlerMethod? {
        val configuration = requestCommandConfigurationFor(handlerMethod)

        return when {
            configuration.enabled && reactiveRequestProperties.enabled -> CommandInvocableHandlerMethod(handlerMethod, reactiveRequestCommandFactory, configuration)
            else -> super.createInvocableHandlerMethod(handlerMethod)
        }
    }

    private fun requestCommandConfigurationFor(handlerMethod: HandlerMethod): RequestCommandConfiguration {
        return handlerMethodConfigurationsCache.getOrPut(handlerMethod) {
            val method = handlerMethod.getMethodAnnotation(RequestCommand::class.java)
            val methodOrController = method ?: AnnotatedElementUtils.findMergedAnnotation(handlerMethod.beanType, RequestCommand::class.java)
            methodOrController?.let { RequestCommandConfiguration(it) } ?: RequestCommandConfiguration.Default
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`createInvocableHandlerMethod`中，我们得到了由 Spring MVC 决定的`handlerMethod`的配置。`handlerMethod`表示控制器动作。然后，我们决定我们是否应该使用速率限制处理程序或回退到默认的处理程序。如果我们需要应用速率限制，我们将调用切换为使用自定义的`CommandInvocableHandlerMethod` :

```
class CommandInvocableHandlerMethod(private val handlerMethod: HandlerMethod,
                                    private val requestCommandFactory: RequestCommandFactory,
                                    private val configuration: RequestCommandConfiguration) : ServletInvocableHandlerMethod(handlerMethod) {
    private lateinit var returnValueHandlers: HandlerMethodReturnValueHandlerComposite

    override fun invokeForRequest(request: NativeWebRequest?, mavContainer: ModelAndViewContainer?, vararg providedArgs: Any?): Any {
        // same as super.invokeForRequest(request, mavContainer, *providedArgs)
        // but with request passed to do invoke
        val args = this.getMethodArgumentValuesCallable.invoke(request, mavContainer, providedArgs)
        val result = doInvokeWithRequest(request, args)
        return result
    }

    private fun doInvokeWithRequest(request: NativeWebRequest?, args: Array<out Any?>?): Any {
        val nativeRequest = request?.getNativeRequest(HttpServletRequest::class.java)

        // If the response has already set error status code tomcat will not wait for async result
        return if (nativeRequest != null && nativeRequest.dispatcherType == DispatcherType.REQUEST) {
            val callSuper = Callable {
                super.doInvoke(*(args ?: emptyArray()))
            }

            val job = callSuper

            val context = RequestCommandContext(configuration, handlerMethod, SecurityContextHolder.getContext(), job)

            val result = requestCommandFactory.createSingle(context)

            MonoDeferredResult(result)
        } else {
            super.doInvoke(*(args ?: emptyArray()))
        }
    }

    override fun setHandlerMethodReturnValueHandlers(returnValueHandlers: HandlerMethodReturnValueHandlerComposite?) {
        this.returnValueHandlers = returnValueHandlers!!
        super.setHandlerMethodReturnValueHandlers(returnValueHandlers)
    }

    override fun wrapConcurrentResult(result: Any?): ServletInvocableHandlerMethod {
        return ConcurrentResultHandlerMethod(result, ConcurrentResultMethodParameter(result))
    }

... 
```

Enter fullscreen mode Exit fullscreen mode

上述代码使用*私有* [`getMethodArgumentValues`](https://github.com/spring-projects/spring-framework/blob/cc74a28/spring-web/src/main/java/org/springframework/web/method/support/InvocableHandlerMethod.java#L147) API 来实现所需的 behavior‼。`doInvokeWithRequest`检查是否应该执行异步分派，如果应该，则创建一个 [`Mono`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html) 来表示控制器动作方法调用的结果。`RequestCommandContext`存储关于目标控制器动作方法和当前安全上下文的信息。在不同的线程上调用控制器操作时，需要保留安全上下文。 [`ConcurrentResultHandlerMethod`](https://gist.github.com/miensol/cca73d158ce8e7664ed653a30fc8dc70) 扩展了`ServletInvocableHandlerMethod`以增加对常规同步控制器动作使用`Mono`的支持。限速的核心逻辑委托给`ReactiveRequestCommandFactory` :

```
interface ReactiveRequestCommandFactory {
    fun createSingle(context: RequestCommandContext): Mono<Optional<Any>>
} 
```

Enter fullscreen mode Exit fullscreen mode

工厂负责将请求上下文转换成异步结果。Spring MVC 5 [已经内置了对反应器](https://docs.spring.io/spring-framework/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html)的支持，因此我们决定使用这个[反应流规范](http://www.reactive-streams.org/)的实现。`ReactiveRequestCommandFactory`看起来如下:

```
@Component
class ReactorRequestCommandFactory(
    threadPoolPropertiesCalculator: ThreadPoolPropertiesCalculator,
    @param:Named("reactiveRequestsScheduler")
    private val reactiveRequestsScheduler: Schedule
) : ReactiveRequestCommandFactory {
    private val threadPoolPropertiesCalculator = HystrixConfigurationAwarePropertiesCalculator(threadPoolPropertiesCalculator)
    private val tenants = ConcurrentHashMap<String, TenantTaskCoordinator>()

    override fun createSingle(context: RequestCommandContext): Mono<Optional<Any>> {
        val properties = threadPoolPropertiesCalculator.newThreadPoolProperties(context)

        val taskCoordinator = tenants.computeIfAbsent(properties.threadPoolName) {
            TenantTaskCoordinator(reactiveRequestsScheduler,
                maximumConcurrency = properties.maximumThreads,
                maximumQueueSize = properties.maximumQueueSize,
                name = properties.threadPoolName
            )
        }

        val optionalCallable = OptionalCallable(context.job)
        val configureRequestAttributes = SpringServletRequestAttributesCallable(optionalCallable)
        val configureLocale = SpringLocaleContextCallable(configureRequestAttributes)
        val securityCallable = DelegatingSecurityContextCallable(configureLocale, context.securityContext)

        return taskCoordinator.execute(securityCallable)
            .timeout(Duration.ofMillis(context.configuration.timeoutInMillis.toLong()))
    }
}

class OptionalCallable(private val inner: RequestHandlerJob) : Callable<Optional<Any>> {
    override fun call(): Optional<Any> = Optional.ofNullable(inner.call())
} 
```

Enter fullscreen mode Exit fullscreen mode

`ThreadPoolPropertiesCalculator`计算特定租户或租户组的并发线程和请求队列的大小。然后，对于每个租户组，特别是单个租户，我们创建一个`TenantTaskCoordinator`,负责计算并发处理请求的限制并加以实施。再往下，我们用安全委托、区域设置和请求属性设置来修饰代表实际请求处理的`Callable`。最后，我们要求`TenantTaskCoordinator`使用配置的超时来执行修饰的作业。

拼图的最后一块，即`TenantTaskCoordinator`需要[一个单独的博客帖子](https://dev.to%20post_url%202018-01-04-multi-tenancy-task-scheduler%20)，敬请关注。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)