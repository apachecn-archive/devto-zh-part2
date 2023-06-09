# 添加 Spring Boot Rest 区域设置查询参数

> 原文：<https://dev.to/dougd/adding-spring-boot-rest-locale-query-param-1ijf>

作为我最近参与的一个项目的一部分，我们需要能够改变来自端点的响应的区域设置，而不需要实际改变底层浏览器的区域设置。

由于一些 Spring 的魔力，为了实现这一点，我扩展了[HandlerInterceptorAdapter](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/handler/HandlerInterceptorAdapter.html)抽象类，允许我处理任何传入的请求。

```
@Override
public boolean preHandle(HttpServletRequest request, 
                         HttpServletResponse response, Object handler)
{
    // set request locale here
    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 preHandle override 中，我获取了区域设置查询参数，并将[locale solver](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/LocaleResolver.html)上的区域设置设置为从查询参数中获取的值。

代码看起来有点像这样:

```
Locale localeFromRequest = RequestContextUtils.getLocale(request);

String queryParameterLocale = request.getParameter("locale");

if (queryParameterLocale != null) {
   localeFromRequest = StringUtils.parseLocaleString(queryParameterLocale);
}

// set the new locale
LocaleResolver localeResolver = RequestContextUtils.getLocaleResolver(request);
localeResolver.setLocale(request, response, localeFromRequest); 
```

Enter fullscreen mode Exit fullscreen mode

最后，为了激活新添加的拦截器适配器，您需要将其添加到您的 [WebMvc](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurerAdapter.html) 配置中。请看下面的例子，在我的例子中，我将我的拦截器命名为 RequestLocaleInterceptor。

```
@Configuration
public class WebMvcConfiguration extends WebMvcConfigurerAdapter
{
    @Autowired
    private RequestLocaleInterceptor requestLocaleInterceptor;

    /**
     * {@inheritDoc}
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry)
    {
        registry.addInterceptor(requestLocaleInterceptor).addPathPatterns("/**");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

从那时起，任何 REST 调用都会自动继承设置区域环境上下文的区域查询参数。

比如:GET /rest/orderDetails？locale=fr_FR