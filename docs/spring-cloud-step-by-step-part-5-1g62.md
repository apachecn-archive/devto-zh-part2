# 春云步步为营(第五部分)

> 原文：<https://dev.to/piczmar_0/spring-cloud-step-by-step-part-5-1g62>

这是之前博文的延续:
这是之前博文的延续:

*   [春云步步为营(上)](https://dev.to/piczmar_0/spring-cloud-step-by-step-part-1-1670)
*   [春云步步为营(下)](https://dev.to/piczmar_0/spring-cloud-step-by-step-part-2-12o2)
*   [春云步步为营(第三部)](https://dev.to/piczmar_0/spring-cloud-step-by-step-part-3-4jeg)
*   [春云步步为营(第四部)](https://dev.to/piczmar_0/spring-cloud-step-by-step-part-4-l00)

在[之前的博客文章](https://dev.to/piczmar_0/spring-cloud-step-by-step-part-4-l00)中，我展示了如何使用`EurekaClient`调用其他服务，但是有一种更简单的方法。

`RestTemplate`足够智能，可以从 Eureka 服务器本身解析服务 URI。

[Ribbon](https://cloud.spring.io/spring-cloud-netflix/multi/multi_spring-cloud-ribbon.html) 是一个客户端负载均衡器，`SpringTemplate`通过简单地添加一个注释:`@LoadBalanced`来支持它。

让我们创建租赁服务控制器的版本 2，它看起来是这样的:

```
@RestController
@RequestMapping(path = "/v2")
public class RentalControllerV2 {
    protected String serviceUrl = "http://PRICING-SERVICE";

    private final RestTemplate restTemplate;

    public RentalControllerV2(@Qualifier("ribbon-template") RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    @RequestMapping(path = "/rentals/{id}/charges", method = RequestMethod.GET)
    public ResponseEntity getPossibleCharges(@PathVariable("id") String id) {

        ZonedDateTime startTime = ZonedDateTime.now();
        ZonedDateTime endTime = startTime.plus(2, ChronoUnit.DAYS);
        HttpEntity<PriceRequest> request = new HttpEntity<>(
                new PriceRequest("vip", startTime, endTime)
        );
        PriceResponse response = restTemplate.postForObject(serviceUrl + "/v1/prices", request, PriceResponse.class);
        return new ResponseEntity<>(new RentalChargesResponse(response.getPrice()), HttpStatus.OK);
    }
} 
```

要启用功能区，我们必须在`bootstrap.yaml`或`application.yaml`
中添加该属性

```
ribbon:
  http:
    client:
      enabled: true 
```

在上面的控制器中，我将`RestTemplate`作为名为`ribbon-template`的 bean 注入。
这是因为我想在应用程序中保留两个`RestTemplate`bean:一个我们在带有本地 EurekaClient API 的版本 1 控制器中使用，另一个在负载平衡的版本 2 控制器中使用`RestTemplate`。

这是我的 beans 配置在主类中的样子:

```
@Bean
public RestTemplate restTemplate() {
    return new RestTemplate();
}

@Bean("ribbon-template")
@LoadBalanced
public RestTemplate restTemplateWithRibbon() {
    return new RestTemplate();
} 
```

仅此而已。启动应用程序并调用端点:

```
curl localhost:8080/v2/rentals/1/charges 
```

您将在日志中看到 Spring 是如何从服务名:
解析租赁服务 URL 的

```
2018-06-29 01:11:58.027  INFO 18934 --- [nio-8080-exec-1] c.netflix.config.ChainedDynamicProperty  : Flipping property: PRICING-SERVICE.ribbon.ActiveConnectionsLimit to use NEXT property: niws.loadbalancer.availabilityFilteringRule.activeConnectionsLimit = 2147483647
2018-06-29 01:11:58.048  INFO 18934 --- [nio-8080-exec-1] c.n.u.concurrent.ShutdownEnabledTimer    : Shutdown hook installed for: NFLoadBalancer-PingTimer-PRICING-SERVICE
2018-06-29 01:11:58.067  INFO 18934 --- [nio-8080-exec-1] c.netflix.loadbalancer.BaseLoadBalancer  : Client: PRICING-SERVICE instantiated a LoadBalancer: DynamicServerListLoadBalancer:{NFLoadBalancer:name=PRICING-SERVICE,current list of Servers=[],Load balancer stats=Zone stats: {},Server stats: []}ServerList:null
2018-06-29 01:11:58.072  INFO 18934 --- [nio-8080-exec-1] c.n.l.DynamicServerListLoadBalancer      : Using serverListUpdater PollingServerListUpdater
2018-06-29 01:11:58.089  INFO 18934 --- [nio-8080-exec-1] c.netflix.config.ChainedDynamicProperty  : Flipping property: PRICING-SERVICE.ribbon.ActiveConnectionsLimit to use NEXT property: niws.loadbalancer.availabilityFilteringRule.activeConnectionsLimit = 2147483647
2018-06-29 01:11:58.090  INFO 18934 --- [nio-8080-exec-1] c.n.l.DynamicServerListLoadBalancer      : DynamicServerListLoadBalancer for client PRICING-SERVICE initialized: DynamicServerListLoadBalancer:{NFLoadBalancer:name=PRICING-SERVICE,current list of Servers=[192.168.43.107:46579],Load balancer stats=Zone stats: {defaultzone=[Zone:defaultzone;    Instance count:1;   Active connections count: 0;    Circuit breaker tripped count: 0;   Active connections per server: 0.0;]
},Server stats: [[Server:192.168.43.107:46579;  Zone:defaultZone;   Total Requests:0;   Successive connection failure:0;    Total blackout seconds:0;   Last connection made:Thu Jan 01 01:00:00 CET 1970;  First connection made: Thu Jan 01 01:00:00 CET 1970;    Active Connections:0;   total failure count in last (1000) msecs:0; average resp time:0.0;  90 percentile resp time:0.0;    95 percentile resp time:0.0;    min resp time:0.0;  max resp time:0.0;  stddev resp time:0.0]
]}ServerList:org.springframework.cloud.netflix.ribbon.eureka.DomainExtractingServerList@fcc5a3d
2018-06-29 01:11:58.131  INFO 18934 --- [nio-8080-exec-1] com.netflix.http4.ConnectionPoolCleaner  : Initializing ConnectionPoolCleaner for NFHttpClient:PRICING-SERVICE
2018-06-29 01:11:59.075  INFO 18934 --- [erListUpdater-0] c.netflix.config.ChainedDynamicProperty  : Flipping property: PRICING-SERVICE.ribbon.ActiveConnectionsLimit to use NEXT property: niws.loadbalancer.availabilityFilteringRule.activeConnectionsLimit = 2147483647 
```

像往常一样，这个例子的源代码在 Github [spring-cloud-5](https://github.com/piczmar/spring-cloud-rental/tree/spring-cloud-5) 分支上。