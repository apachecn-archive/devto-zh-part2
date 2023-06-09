# 在 Java 8 中使用 ZonedDateTime 将时区添加到 LocalDateTime

> 原文：<https://dev.to/codebyamir/add-a-timezone-to-localdatetime-with-zoneddatetime-in-java-8-608>

*最初发表在我的博客上[www.codebyamir.com](http://www.codebyamir.com)T3】*

# 概述

Java 8 中引入的`LocalDateTime`类存储日期和时间，但不存储时区。因此，在开发需要了解不同时区的应用程序时，我们还需要一些别的东西。

幸运的是，我们可以使用`ZonedDateTime`类来表示带有时区信息的日期和时间。该类包含静态方法，允许我们轻松地执行日期转换和计算。

* * *

# 代码示例

对象是不可变的，所以静态方法都返回一个新的实例。

## 获取当地时区的当前日期和时间

### ZonedDateTime.now()

```
ZonedDateTime now = ZonedDateTime.now();
// 2018-05-02T15:45:20.981-04:00[America/New_York] 
```

Enter fullscreen mode Exit fullscreen mode

## 获取不同时区的当前日期和时间

### zoneddatetime . now(zoneId)

```
ZonedDateTime nowInParis = ZonedDateTime.now(ZoneId.of("Europe/Paris"));
// 2018-05-02T21:45:20.982+02:00[Europe/Paris] 
```

Enter fullscreen mode Exit fullscreen mode

## 时区 Id 值

时区 id 值的列表在这里有很好的记录[。](https://garygregory.wordpress.com/2013/06/18/what-are-the-java-timezone-ids/)

我们也可以使用一种方法来检索它们:

```
Set<String> zoneIds = ZoneId.getAvailableZoneIds(); 
```

Enter fullscreen mode Exit fullscreen mode

始终使用地区/城市格式的时区值(America/New_York ),并避免使用短缩写(EST ),因为它们不明确且不标准。

## 从字符串创建 ZonedDateTime

### ZonedDateTime.parse(日期字符串)

```
ZonedDateTime departureTime = ZonedDateTime.parse("2018-07-01T10:00:00Z[America/New_York]");
ZonedDateTime arrivalTime = ZonedDateTime.parse("2018-07-01T22:00:00Z[Europe/London]"); 
```

Enter fullscreen mode Exit fullscreen mode

## 将本地日期时间转换为区域日期时间

我们可以用几种不同的方法将 LocalDateTime 转换为 ZonedDateTime。

```
LocalDateTime ldt = LocalDateTime.parse("2018-07-01T08:00");
ZoneId zoneId = ZoneId.of("Europe/Paris"); 
```

Enter fullscreen mode Exit fullscreen mode

### ZonedDateTime.of(ldt，zoneId)

```
ZonedDateTime zdt = ZonedDateTime.of(ldt, zoneId); 
// 2018-07-01T08:00+02:00[Europe/Paris] 
```

Enter fullscreen mode Exit fullscreen mode

### atZone(zoneId)

```
ZonedDateTime zdt = ldt.atZone(zoneId); 
// 2018-07-01T08:00+02:00[Europe/Paris] 
```

Enter fullscreen mode Exit fullscreen mode

注意，这不会将任何时区转换应用到我们的`LocalDateTime`。它只是将时区添加到存储在源对象中的日期和时间中。

## 比较 ZonedDateTime 对象

### isefore(ZD)

```
boolean departureBeforeArrival = departureTime.isBefore(arrivalTime); 
```

Enter fullscreen mode Exit fullscreen mode

### isAfter(zdt)

```
boolean arrivalAfterDeparture = arrivalTime.isAfter(departureTime); 
```

Enter fullscreen mode Exit fullscreen mode

### isEqual(zdt)

这将比较实际的日期和时间，而不是对象引用。

```
LocalDateTime ldt = LocalDateTime.parse("2018-07-01T08:00");

ZonedDateTime zdtParis = ZonedDateTime.of(ldt, ZoneId.of("Europe/Paris"));
// 2018-07-01T08:00+02:00[Europe/Paris]

ZonedDateTime zdtNewYork = ZonedDateTime.of(ldt, ZoneId.of("America/New_York"));
// 2018-07-01T08:00-04:00[America/New_York]

boolean equal = zdtParis.isEqual(zdtNewYork); // false 
```

Enter fullscreen mode Exit fullscreen mode

## 查找 ZonedDateTime 对象之间的时差

`java.time.temporal`中的`ChronoUnit`允许我们计算`ZonedDateTime`物体之间的时间差:

```
long flightTimeInHours = ChronoUnit.HOURS.between(departureTime, arrivalTime); // 7
long flightTimeInMinutes = ChronoUnit.MINUTES.between(departureTime, arrivalTime); // 420 
```

Enter fullscreen mode Exit fullscreen mode

## 从现有的 ZonedDateTime 对象创建具有新时区的 ZonedDateTime

给定时区 A 中的一个`ZonedDateTime`对象，我们可以在时区 B 中创建另一个代表相同时间点的`ZonedDateTime`对象。

### withzonesame instant(zoneid)

```
ZonedDateTime nowInLocalTimeZone = ZonedDateTime.now();
// 2018-05-02T20:10:27.896-04:00[America/New_York]

ZonedDateTime nowInParis = nowInLocalTimeZone.withZoneSameInstant(ZoneId.of("Europe/Paris"));
// 2018-05-03T02:10:27.896+02:00[Europe/Paris] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 单元测试注意事项

假设我们需要一种方法，根据某种业务逻辑来确定是否可以向航空公司的乘客发放登机牌。

当离出发或返回航班的出发时间不到 24 小时时，它应该返回 true。

我们可以这样写:

```
package com.example.demo;

import java.time.ZonedDateTime;
import java.time.temporal.ChronoUnit;

public class BoardingPassService {
  private static final int MIN_HOUR_DIFF_BOARDING_PASS = -24;

  public boolean canIssueBoardingPass(Ticket ticket) {
    if (ticket == null || ticket.getOutboundFlight() == null || ticket.getReturnFlight() == null) {
      return false;
    }

    ZonedDateTime now = ZonedDateTime.now();    
    ZonedDateTime outboundDepartureTime = ticket.getOutboundFlight().getDepartureTime();
    ZonedDateTime returnDepartureTime = ticket.getReturnFlight().getDepartureTime();

    long diffInHours = ChronoUnit.HOURS.between(outboundDepartureTime, now);

    boolean outboundDepartureInAllowableRange = (diffInHours >= MIN_HOUR_DIFF_BOARDING_PASS && diffInHours <= 0);

    diffInHours = ChronoUnit.HOURS.between(returnDepartureTime, now);

    boolean returnDepartureInAllowableRange = (diffInHours >= MIN_HOUR_DIFF_BOARDING_PASS && diffInHours <= 0);

    if (outboundDepartureInAllowableRange || returnDepartureInAllowableRange) {
      return true;
    }

    return false;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这工作得很好，但是由于`ZonedDateTime.now()`的原因很难进行单元测试。我们不能模仿它，因为它是一个静态方法。

## java.time.Clock

`java.time`中的 now()方法都接受一个`Clock`参数，我们可以用它来重构我们的代码，使其可测试。

下面是该类的可测试版本:

```
package com.example.demo;

import java.time.Clock;
import java.time.ZonedDateTime;
import java.time.temporal.ChronoUnit;

public class BoardingPassService {

  private final Clock clock; 
  private static final int MIN_HOUR_DIFF_BOARDING_PASS = -24;

  public BoardingPassService(Clock clock) {
    this.clock = clock;
  }

  public boolean canIssueBoardingPass(Ticket ticket) {
    if (ticket == null || ticket.getOutboundFlight() == null || ticket.getReturnFlight() == null) {
      return false;
    }

    ZonedDateTime now = ZonedDateTime.now(clock);   
    ZonedDateTime outboundDepartureTime = ticket.getOutboundFlight().getDepartureTime();
    ZonedDateTime returnDepartureTime = ticket.getReturnFlight().getDepartureTime();

    long diffInHours = ChronoUnit.HOURS.between(outboundDepartureTime, now);

    boolean outboundDepartureInAllowableRange = (diffInHours >= MIN_HOUR_DIFF_BOARDING_PASS && diffInHours <= 0);

    diffInHours = ChronoUnit.HOURS.between(returnDepartureTime, now);

    boolean returnDepartureInAllowableRange = (diffInHours >= MIN_HOUR_DIFF_BOARDING_PASS && diffInHours <= 0);

    if (outboundDepartureInAllowableRange || returnDepartureInAllowableRange) {
      return true;
    }

    return false;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在第 12 行，我们添加了一个接受`Clock`对象的单参数构造函数。然后我们可以使用第 21 行上的时钟。

在调用代码中，我们将把`Clock.systemDefaultZone()`传递给构造函数来使用当前的系统时间。

在我们的测试中，我们将使用`Clock.fixed()`创建一个固定的时钟并传递它。

## 单元测试

```
package com.example.demo;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.time.Clock;
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.ZonedDateTime;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class BoardingPassServiceTest {
  private Ticket ticket;
  private BoardingPassService boardingPassService;

  private final LocalDateTime REFERENCE_DATE_TIME = LocalDateTime.of(2018, 7, 1, 10, 0); // 2018-07-01 at 10:00am
  private final ZoneId defaultZone = ZoneId.systemDefault();
  private final Clock FIXED_CLOCK = Clock.fixed(REFERENCE_DATE_TIME.atZone(defaultZone).toInstant(), defaultZone);

  @BeforeEach 
  public void setUp() {
    ticket = new Ticket();
    boardingPassService = new BoardingPassService(FIXED_CLOCK);
  }

  @Test
  public void testCanIssueBoardingPass_Null() {
    assertFalse(boardingPassService.canIssueBoardingPass(null));
  }

  @Test
  public void testCanIssueBoardingPass_OutboundFlightWithinTimeRange() {
    Flight outboundFlight = new Flight();
    outboundFlight.setDepartureTime(ZonedDateTime.parse("2018-07-02T07:30:00Z[America/Chicago]"));

    Flight returnFlight = new Flight();
    returnFlight.setDepartureTime(ZonedDateTime.parse("2018-07-09T11:30:00Z[America/New_York]"));

    ticket.setOutboundFlight(outboundFlight);
    ticket.setReturnFlight(returnFlight);

    assertTrue(boardingPassService.canIssueBoardingPass(ticket));
  }

  @Test
  public void testCanIssueBoardingPass_ReturnFlightWithinTimeRange() {
    Flight outboundFlight = new Flight();
    outboundFlight.setDepartureTime(ZonedDateTime.parse("2018-06-20T07:30:00Z[America/Chicago]"));

    Flight returnFlight = new Flight();
    returnFlight.setDepartureTime(ZonedDateTime.parse("2018-07-02T09:30:00Z[America/New_York]"));

    ticket.setOutboundFlight(outboundFlight);
    ticket.setReturnFlight(returnFlight);

    assertTrue(boardingPassService.canIssueBoardingPass(ticket));
  }

  @Test
  public void testCanIssueBoardingPass_NoFlightWithinTimeRange() {
    Flight outboundFlight = new Flight();
    outboundFlight.setDepartureTime(ZonedDateTime.parse("2018-09-01T07:30:00Z[America/Chicago]"));

    Flight returnFlight = new Flight();
    returnFlight.setDepartureTime(ZonedDateTime.parse("2018-09-08T11:30:00Z[America/New_York]"));

    ticket.setOutboundFlight(outboundFlight);
    ticket.setReturnFlight(returnFlight);

    assertFalse(boardingPassService.canIssueBoardingPass(ticket));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode