# 忽略应用洞察中的 Umbraco ping.aspx

> 原文：<https://dev.to/swimburger/ignoring-umbraco-ping-aspx-from-application-insights-1i56>

应用程序性能监视器为您提供了大量数据，但其中一些数据可能并不相关。具体来说，在 Umbraco 的`\umbraco\ping.aspx`有一个页面被频繁调用，以保持网站的活力。这对于防止站点“死亡”(？)，但是这个请求的数据并不相关，可能会扭曲您的统计数据。使用**Application Insights ITelemetryProcessor**，我们可以阻止 ping 请求发送到 Azure Application Insights。

## 使用 ITelemetryProcessor 过滤应用洞察指标

Application Insight (AI) SDK 包含一个接口`ITelemetryProcessor`，在将遥测数据发送到 AI 服务之前，将调用该接口获取所有遥测数据。这使得开发人员能够截取遥测数据，并对其应用自定义逻辑。也许需要添加额外的数据，也许你必须删除一些机密或敏感的数据，如 PII 或 PHI。在我们的例子中，我们希望某些遥测数据根本不被发送。

更具体地说，每当 HTTP 请求进入请求`/umbraco/ping.aspx`时，产生的遥测应该被阻塞。让我们创建我们的`ITelemetryProcessor`来满足这个需求。

```
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System;

namespace YourApp.ApplicationInsights
{
    public class PingFilter : ITelemetryProcessor
    {
        //next processor in the pipeline
        private readonly ITelemetryProcessor next;

        public PingFilter(ITelemetryProcessor next)
        {
            //store next processor in the pipeline
            this.next = next;
        }

        //our logic goes here
        public void Process(ITelemetry telemetry)
        {
            //check to see if telemetry is for a Request
            var request = telemetry as RequestTelemetry;
            //ignore if RequestTelemetry is a ping request
            if (request?.Url?.AbsolutePath.Equals("/umbraco/ping.aspx", StringComparison.OrdinalIgnoreCase) == true)
            {
                //stop pipeline
                return;
            }

            //invoke next processor
            next.Process(telemetry);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来解释这个小片段:

1.  构造函数接收下一个应该在我们完成处理后调用的`ITelemetryProcessor`。该处理器将遵循相同的准则，并调用下一个处理器。
2.  Process 是我们必须实现的方法，因为我们继承自`ITelemetryProcessor`。这是我们可以添加自己的逻辑的地方。
    1.  首先，我们检查遥测是否属于类型`RequestTelemetry` **因为我们想忽略 ping 请求。如果不是请求，我们就把它传递给下一个处理器。
    2.  现在我们知道遥测是关于请求的，我们可以检查`AbsolutePath`属性，并检查请求是否是针对`/umbraco/ping.aspx`的。如果是 ping 请求，我们可以通过不返回任何内容并且不调用下一个处理器来停止管道。

我们的`PingFilter`已经准备好了，但是 Application Insights 还不知道它的存在。为了告诉 AI 使用过滤器，我们必须添加对`ApplicationInsights.config`的引用。这个配置文件是在您安装 AI 时自动创建的。在`TelemetryProcessors`节点的开头添加对`PingFilter`的引用。

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  ...
  <TelemetryProcessors>
    <Add Type="YourApp.ApplicationInsights.PingFilter, YourApp"/>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryProcessor, Microsoft.AI.PerfCounterCollector"/>
    ...
  </TelemetryProcessors>
</ApplicationInsights> 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都连接好了，您可以在`PingFilter`中放置一个断点并查看它的运行情况。

仅此而已，干杯！