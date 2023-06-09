# 春云循序渐进(第一部分)

> 原文：<https://dev.to/piczmar_0/spring-cloud-step-by-step-part-1-1670>

这篇文章是 Spring Cloud 系列文章的第一篇 Spring Cloud 是一套用于构建“云原生”应用程序的 Spring 库。最初发表于 [Cloudly。技术](https://cloudly.tech/blog/spring-cloud-1/)

什么是“云原生”应用？它意味着应用程序有 [12 个因子](https://12factor.net/)。
简而言之:这些应用易于部署在可用的云提供商上，易于扩展/缩减、监控和发布(通过 CI/CD)。

我将向您展示如何从一个简单的 Spring Web 项目开始，在最后创建一个 Spring 云应用程序。

我们将要构建的示例应用程序是一个用于租赁商品(例如视频)的 rest API。它将根据租赁计划和期限返回价格。

该项目可从 github 获得，每个步骤都是一个独立的分支。

在本帖中，我们将创建 [spring-cloud-1](https://github.com/piczmar/spring-cloud-rental/tree/spring-cloud-1) 分支。

我们将首先从使用 Spring Initializr 在 [http://start.spring.io](http://start.spring.io) 生成的标准 Spring web 应用程序开始

然后，我们将继续向它添加云功能，例如:

*   使用[云配置](https://cloud.spring.io/spring-cloud-config/)进行集中配置
*   使用[云发现](https://spring.io/guides/gs/service-registration-and-discovery/)进行服务发现
*   使用[云追踪](https://cloud.spring.io/spring-cloud-sleuth/)进行监控
*   具有新[云网关](http://cloud.spring.io/spring-cloud-gateway/)或旧[云网关](https://spring.io/guides/gs/routing-and-filtering/)的 API 网关

让我们生成一个基本的 Spring web 项目。

[![Generate project](img/1276c568efa5ca90e912d86e335b729d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I2iJk7sL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zso5r075bhrp8c2ids2d.png)

然后，我们将添加休息控制器，在开始时，它只会回应零价格。

```
package com.example.cloud.pricingservice.v1;

import com.example.cloud.pricingservice.v1.dto.PriceRequest;
import com.example.cloud.pricingservice.v1.dto.PriceResponse;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.math.BigDecimal;

@RestController
@RequestMapping(path = "/v1")
public class PriceController {

    @RequestMapping(path = "/prices", method = RequestMethod.POST)
    public ResponseEntity<PriceResponse> getPriceAmount(@RequestBody PriceRequest priceRequest) {

        return new ResponseEntity<>(new PriceResponse(BigDecimal.ZERO), HttpStatus.OK);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

从
开始项目

```
mvn spring-boot:run 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用 curl
来测试它

```
curl -X POST -H 'Content-Type:application/json' -d '{"plan":"vip", "start": "2011-12-03T10:15:30+01:00","end" : "2011-12-05T12:15:30+01:00"}' http://localhost:8080/v1/prices 
```

Enter fullscreen mode Exit fullscreen mode

它会用
来回应

```
{"price":0} 
```

Enter fullscreen mode Exit fullscreen mode

假设我们在 yaml 文件中有一个静态的每日租金配置，比如:

```
plans: 
   BASIC 10
   STANDARD 20
   VIP 50 
```

Enter fullscreen mode Exit fullscreen mode

我们将用 application.yaml 替换 application.properties，因为 Spring 支持这两种格式，并在那里添加计划:

```
server:
  port=8080

pricing:
  plan:
    basic: 10
    standard: 20
    vip: 50 
```

Enter fullscreen mode Exit fullscreen mode

就我个人而言，我觉得这种格式比属性更具可读性，但这是个人喜好的问题。如果你喜欢，你可以使用属性。

为了在我们的应用程序中将这些属性作为地图，我们需要编写一些代码:

```
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.stereotype.Component;

import java.math.BigDecimal;
import java.util.HashMap;

@Component
@EnableConfigurationProperties
@ConfigurationProperties(prefix = "pricing")
public class PlanConfig {

    private HashMap<String, BigDecimal> plan;

    public HashMap<String, BigDecimal> getPlan() {
        return plan;
    }

    public void setPlan(HashMap<String, BigDecimal> plan) {
        this.plan = plan;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个 spring 管理的 bean，我们可以将它注入到服务中，并基于它计算价格。
让我们创建一个服务类，如下所示:

```
package com.example.cloud.pricingservice.services;

import com.example.cloud.pricingservice.config.PlanConfig;
import org.springframework.stereotype.Component;
import org.springframework.util.Assert;

import java.math.BigDecimal;
import java.time.Duration;
import java.time.ZonedDateTime;
import java.time.temporal.ChronoUnit;

@Component
public class PriceCalculator {

    private final PlanConfig planConfig;

    public PriceCalculator(PlanConfig planConfig) {
        this.planConfig = planConfig;
    }

    /**
     * Calculates price according to plans. The minimal unit of price calculation is one day.
     * If the rent time overlaps next day the whole price for another day is calculated.
     *
     * @return calculated price
     */
    public BigDecimal calculatePrice(ZonedDateTime rentStartDate, ZonedDateTime rentEndDate, String planName) {
        Assert.notNull("Plan name is required", planName);

        long exactDaysCount = ChronoUnit.DAYS.between(rentStartDate, rentEndDate);
        long daysCount = exactDaysCount + (isOverlappingNextDay(exactDaysCount, rentStartDate, rentEndDate) ? 1 : 0);

        BigDecimal pricePerDay = planConfig.getPlan().getOrDefault(planName.toLowerCase(), BigDecimal.ZERO);
        return pricePerDay.multiply(new BigDecimal(daysCount));
    }

    private boolean isOverlappingNextDay(long exactDaysCount, ZonedDateTime rentStartDate, ZonedDateTime rentEndDate) {
        ZonedDateTime exactEndTime = rentStartDate.plus(exactDaysCount, ChronoUnit.DAYS);
        Duration overlappedDayDuration = Duration.between(exactEndTime, rentEndDate);
        return overlappedDayDuration.getSeconds() > 0;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们更新我们的控制器，并在其中使用`calculatePrice`方法。

```
@RestController
@RequestMapping(path = "/v1")
public class PriceController {
    private final PriceCalculator priceCalculator;

    public PriceController(PriceCalculator priceCalculator) {
        this.priceCalculator = priceCalculator;
    }

    @RequestMapping(path = "/prices", method = RequestMethod.POST)
    public ResponseEntity<PriceResponse> getPriceAmount(@RequestBody PriceRequest priceRequest) {

        return new ResponseEntity<>(
                new PriceResponse(priceCalculator.calculatePrice(priceRequest.getStart(), priceRequest.getEnd(), priceRequest.getPlan())),
                HttpStatus.OK);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在构建并再次启动应用程序，并执行相同的 curl 命令，结果应该会有所不同，这取决于租赁将持续多少天。
例如:

```
curl -X POST -H 'Content-Type:application/json' -d '{"plan":"basic", "start": "2011-12-03T10:15:30+01:00","end" : "2011-12-05T12:15:30+01:00"}' http://localhost:8080/v1/prices 
```

Enter fullscreen mode Exit fullscreen mode

返回

```
{"price":30} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个用 Spring Web 实现的 REST API 示例，我们可以在下一篇文章的[中对其进行处理，将其转换成 Spring Cloud 应用程序。](https://dev.to/piczmar_0/spring-cloud-step-by-step-part-2-12o2)