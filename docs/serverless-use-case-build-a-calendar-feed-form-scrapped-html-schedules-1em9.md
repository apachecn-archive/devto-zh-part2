# 无服务器用例:从废弃的 HTML 时间表构建一个日历提要

> 原文：<https://dev.to/thatfrankdev/serverless-use-case-build-a-calendar-feed-form-scrapped-html-schedules-1em9>

这篇文章不是教程。为了尽量简短，我省略了许多实现细节。

我每周玩一次终极游戏。这是一项我非常喜欢的运动。我纠结的一件蠢事就是记住本周的比赛是什么时候。我当地的终极组织有一个网站，我可以登录并看到所有的东西，但登录页面的“记住我”功能，实际上根本不记得我。

因此，作为一个典型的懒惰的开发人员，我看到了一个机会，将这个刺激物变成一个整洁的 lifehack 实验。

## 我想要什么

所以我有这个 HTML 表格，里面有所有比赛的时间表。我想要的是把它们都放在一个 ICS 文件中，理想情况下:

*   自动更新自身
*   在网上提供，这样我就可以订阅了

## 挑选工具

我选择了 [Apify](https://www.apify.com/) 进行数据抓取。该产品允许您抓取页面，从中提取数据，并通过 API 端点公开这些数据。

为了将这些数据压缩成 ICS 文件，我决定使用 Azure Function 作为我的无服务器平台。

生成的文件存储在 [Azure Blob 存储器](https://azure.microsoft.com/en-us/services/storage/blobs/)中。

## 收集数据

因此，表格看起来像这样:

[![schedule HTML table](../Images/8ad4485817b83d90e21a02ad61a0a024.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W0Jkpr7i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thatfrankdevcaneast.blob.core.windows.net/public/dev.to/20180409/schedule-screenshot.png)

现在，我就不赘述 Apify 的工作原理了。只需知道，一旦我知道如何设置爬虫登录和 cookies 保留，我就能够设置开始/伪 URL 和可点击的元素。之后我需要做的是编写他们称之为“页面函数”的代码，即在每次页面抓取时提取数据的函数。看起来是这样的:

```
function pageFunction(context) {
  var $ = context.jQuery;
  var req = context.request;
  var result = null;

  function getTeam(row, order) {
    var teamLink = row.find("td.team a");
    var team = {};
    team.name = $(teamLink[order]).text();
    team.url = "http://www.ultimatequebec.ca" + teamLink.attr("href");
    return team;
  }

  function getPlace(row) {
    var placeLink = row.find("td.place a");
    var place = {};
    place.name = placeLink.text();
    place.url = placeLink.attr("href");
    return place;
  }

  var calendar = req.loadedUrl.match(/calendriers/);

  if (calendar) {

    var games = [];
    var date = "";

    $("table.calendar").each(function () {
      $(this).find("tr").each(function (index) {
        if (index === 0) {
          date = $(this).text();
          return;
        }

        var row = $(this);

        var game = {};
        game.date = date;
        game.hour = row.find("td.hour").text();
        game.place = getPlace(row);
        game.team1 = getTeam(row, 0);
        game.team2 = getTeam(row, 1);
        game.url = "http://www.ultimatequebec.ca" + row.find("td:last-child() a").attr("href");
        games.push(game);
      });
    });

    result = {
      games: games
    };
  }

  return result ? result : {};
} 
```

Enter fullscreen mode Exit fullscreen mode

漂亮的~~丑陋的~~基本的 jQuery 位，对吗？

一旦爬虫运行，它的结果 API 端点将按预期返回抓取的数据:

```
"games":  [  {  "date":  "lundi, 09 avril 2018",  "hour":  "19h30",  "place":  {  "name":  "Marc-Simoneau, C- Bord",  "url":  "https://www.google.ca/maps/place/Centre+sportif+Marc-Simoneau/@46.8616686,-71.2080988,15.5z/data=!4m5!3m4!1s0x0:0x30df80932f5ceacb!8m2!3d46.8617603!4d-71.209124"  },  "team1":  {  "name":  "Cut in the End Zone",  "url":  "http://www.ultimatequebec.ca/members/teams/cut-in-the-end-zone"  },  "team2":  {  "name":  "Les muppets",  "url":  "http://www.ultimatequebec.ca/members/teams/cut-in-the-end-zone"  },  "url":  "http://www.ultimatequebec.ca/members/games/14332"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

我将爬虫配置为每天运行两次，这样我就可以一直拥有新鲜的数据。

太好了！现在，来点数据处理怎么样？

## 转换数据

既然 Azure 功能[现在运行。网芯 2.0](https://blogs.msdn.microsoft.com/appserviceteam/2017/09/25/develop-azure-functions-on-any-platform/) ，我决定试一试。我采用的方法是创建一个函数[作为类库](https://docs.microsoft.com/en-us/azure/azure-functions/functions-dotnet-class-library)。

它由 POST HTTP 调用触发。Apify crawler 有一个 webhook 配置，它在每次成功运行结束时戳这个函数。

我再一次告诉你实现的细节，但是这个函数的`Run`方法是这样的:

```
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Function, "post")] HttpRequest req,
    [Blob("uq-schedule", Connection = "TFD_STORAGE")] CloudBlobContainer container,
    TraceWriter log)
{
    var teamsDict = new Dictionary<string, GameCalendarWriter>();

    var fullScheduleBlob = container.GetBlockBlobReference("uq-full-schedule.ics");
    var fullScheduleBlobStream = await fullScheduleBlob.OpenWriteAsync();

    var apiToken = FunctionHelpers.GetEnvironmentVariable("TFD_APIFY_TOKEN");
    var userId = FunctionHelpers.GetEnvironmentVariable("TFD_APIFY_USERID");

    IApifyClient apifyClient = new ApifyClient(apiToken, userId);

    var result = await apifyClient.GetLastRunResultsAsync("ultimateqc_schedule");

    var games = result
        .Where(r => r.PageFunctionResult.Games?.Any() ?? false)
        .SelectMany(r => r.PageFunctionResult.Games)
        .ToList();

    using (var fullCalendarWriter = new GameCalendarWriter(fullScheduleBlobStream))
    {
        foreach (var game in games)
        {
            await fullCalendarWriter.WriteGame(game);
            await (await GetTeamWriter(container, teamsDict, game.Team1)).WriteGame(game);
            await (await GetTeamWriter(container, teamsDict, game.Team2)).WriteGame(game);
        }    
    }

    foreach (var writer in teamsDict)
        writer.Value?.Dispose();
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将为所有球队的所有比赛生成一个全局 ICS 文件，也为每个球队生成一个。所有这些文件都将存储在 Azure Blob 存储容器中，可以通过它们的 URL 公开访问。以下是我得到的一个片段:

[![ICS files listing](../Images/ae0bfe5044eb3e65c7213d12cb7682b4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9WI_Cezw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thatfrankdevcaneast.blob.core.windows.net/public/dev.to/20180409/storage-explorer.png)

## 最后，订阅

我现在可以登录我的谷歌日历，并通过它的网址订阅我的团队的 ICS 文件。谷歌将随时更新该文件内容的变化。

我都准备好了！我现在可以在我的日历应用程序中访问所有即将到来的游戏。我可以设置自定义提醒，过上更快乐、更懒散的生活！

[![Mobile calendar screenshot](../Images/bb9cc494daf7790a2ef1c73e98ae286d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W1bIL55y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thatfrankdevcaneast.blob.core.windows.net/public/dev.to/20180409/mobile-calendar-app-screenshot.png)

* * *

感谢阅读，希望你喜欢:)

英语是我的第二语言，所以请告诉我你是否发现了打字错误、语法错误或任何可能使这篇文章难以阅读的地方。我一直在努力做得更好。