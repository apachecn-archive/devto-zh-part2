# 使用 Azure 的无服务器天气监控

> 原文：<https://dev.to/lukaszkuczynski/serverless-monitoring-of-weather-with-azure-4d89>

## 为什么是项目，为什么是平台

这个名为“通知者”的项目一直留在我的脑海里。它甚至有 Python *化身*，你可以在这些回购上查看[。问题是它总是非常**通用**，所以我被自己设定的配置需求淹没了。Azure 的平台是这方面的意外，我在我的工作场所可以使用它，所以即使有一些 AWS 背景，我也决定尝试一下。](https://github.com/lukaszkuczynski/scout)

## 想法、积木

所以主要思想是:当事情发生变化时得到通知。因此，例如，当天气变化时(使用 API 检查)，我希望通过某种通知机制得到变化通知，比如:Slack。使用 Azure Function 应用程序，鼓励你按照“微服务”的思维方式将职责分解到不同的功能中。以下构建模块将支持使用功能应用程序:

*   每小时读取一次数据
*   将读数写入数据库(CosmosDB 是默认选择)
*   触发队列中的消息
*   将读数与之前的值进行比较
*   如果当前值不同于先前值，将其写入*变更*集合
*   通知我变更

### 如何

允许设计者使用我将在这里广泛使用的以下特性来构建一个管道:绑定、触发器和输出。

### 触发

触发器是可以开始执行一个函数的东西。它可以是:

*   时间(cron 表达式)
*   队列中的新消息
*   新数据库记录/文档
*   创建了更多的新文件，点击这里。

### 装订

当一个函数被执行时，你不需要担心如何从 Azure infrastracture 获得正确的数据，因为平台能够**将正确的值作为参数注入**到函数中。使用 *Azure portal* 可以很容易地设计它，但是在你感觉更加专业之后，你可以处理 json 函数来创建和修改它的绑定。

### 输出

函数产生数学意义上的结果。这可以在 Azure cloud 中完成，但是我们可以选择 0 个或更多的动作作为函数执行的结果。例如，我们可以说，如果一条消息进入一个队列，那么应该在 CosmosDB 集合中创建一个文档。

## 管道

这些构件使得非常简单的通知机制成为可能。我只牺牲了几个小时来制作它，所以结果可能不是很大，但我可以品尝到平台的一些可能性。

### 取数据:定时器- >队列

首先，我需要数据。它来自 openweathermap portal 提供的 api，任何人都可以免费用于开发目的。基本上，我为我的城市(波兰弗罗茨瓦夫)获取 json 格式的数据，然后将它放入一个队列中。这个例子的数据非常简单:它只是对天气的简短描述:下雨、多云、晴朗。当它被读取时，我将它放入一个队列，这是简单的 Azure 队列存储，如这里描述的。

```
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputQueueWeather)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    string url = "https://api.openweathermap.org/data/2.5/weather?q=Wroclaw,pl&appid=<MYKEY>";
    var client = new HttpClient();
    var response = client.GetAsync(url).Result;
    var content = response.Content;
    string responseString = content.ReadAsStringAsync().Result;
    log.Info(responseString);
    var weather = JsonConvert.DeserializeObject<WeatherResponse>(responseString as string);
    string mainWeatherInWroclaw = weather.weather[0].main;
    log.Info(mainWeatherInWroclaw);
    outputQueueWeather.Add(mainWeatherInWroclaw);
}

class Coord {
    public double lat { get; set; }
    public double lon { get; set; }
}

class Weather {
    public int id {get; set;}
    public string main {get; set;}
    public string description {get; set;}
    public string icon {get; set;}
}

class WeatherResponse
{
    public Coord coord { get; set; }
    public IList<Weather> weather { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 保存已经读取的数据，队列- > DB

当数据被放入一个队列时，我把它放入一个存储在持久性中的集合中。这使得比较成为可能，从而决定变化是否发生。

```
using System;

public static void Run(string weatherQueueItem, TraceWriter log, ICollector<WeatherItem> outputDbDocument)
{
    log.Info("Executing with "+weatherQueueItem);
    var dbWeatherItem = new WeatherItem(weatherQueueItem);
    outputDbDocument.Add(dbWeatherItem);
}

public class WeatherItem
{
    public string Text { get; set; }
    public WeatherItem(string text)
    {
        Text = text;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 数据对比，DB - >队列

将文档插入 CosmosDB 集合后，它会触发一个计算。这意味着我们只取两个以前的文档，它们按时间戳降序排列。这在下面的清单中是**而不是**可见的，因为这个“查询”包含在函数定义(json 文件)中。当进行比较时，我们知道是否有变化。关于这一变化的信息被再次传播到消息传递系统，当然使用不同的队列。

```
#r "Microsoft.Azure.Documents.Client"
using System;
using System.Collections.Generic;
using Microsoft.Azure.Documents;

public class WeatherItem {
    public string Text {get; set;}
}

public static void Run(IReadOnlyList<Document> documents, IEnumerable<WeatherItem> lastWeathers, TraceWriter log, ICollector<string> outputChangedWeather)
{
    if (lastWeathers != null && lastWeathers.Count() > 0)
    {

        log.Info("Documents count " + lastWeathers.Count());
        if (lastWeathers.Count() >= 2) 
        {
            var enumerator = lastWeathers.GetEnumerator();
            enumerator.MoveNext();
            var firstValue = enumerator.Current.Text;
            enumerator.MoveNext();
            var secondValue = enumerator.Current.Text;
            if (firstValue != secondValue) 
            {
                string changeText = "change! "+firstValue +" > " + secondValue;
                log.Info(changeText);
                outputChangedWeather.Add(changeText);

            } 
            else 
            {
                log.Info("Values the same."+firstValue);
            }
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 检测到变化，采取措施！队列- > DB，邮箱

最后，当 Azure Queue 出现变化时，我们可以做些什么。我会将它保存到 DB(用于历史目的)并通知自己。就通知而言，有很多可能性。我使用 SendGrid binding 提供的简单电子邮件通知。首先你必须使用你的 SendGrid API 密匙，这个密匙对于开发者来说是免费的，证据是如下的。然后有了那个键，你就可以把它作为一个 SendGrid 绑定来使用，如下例所示

```
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(string myQueueItem, TraceWriter log, out Mail mail)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    mail = new Mail
    {        
        Subject = "Azure news",
        From = new Email("lukaszazurowy@azure.com")        
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("YOUR_EMAIL_GOES_HERE"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = myQueueItem
    };
    mail.AddContent(content);
    mail.AddPersonalization(personalization);

} 
```

Enter fullscreen mode Exit fullscreen mode

csharp

## 总结

我从 Pluralsight 了解了很多关于 Azure 的事情。使用 Azure 来满足我的“业务需求”很酷，因为我意识到，它是:

*   相对容易
*   有据可查
*   实现“短时间上市”
*   便宜，因为运行功能应用程序每次运行将近 0 美元

但是，我可以看到未来的改进:

*   数据作为字符串在函数间传播，我应该考虑函数间共享的类型和对象
*   我必须考虑连接是否设计良好(例如，我是否应该在 DB 文档插入后或直接从队列系统触发更改通知)。