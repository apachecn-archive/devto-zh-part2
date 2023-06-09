# 在 Azure 中使用 Chrome 和 Puppeteer Sharp 和 Browserless.io

> 原文：<https://dev.to/hardkoded/using-chrome-in-azure-with-puppeteer-sharp-and-browserlessio-2odk>

木偶师 Sharp v0.4 最令人兴奋的功能之一是连接到远程浏览器的能力，如 browserless.io。所以，当我决定写一篇关于在 Azure 中使用 Chrome 的帖子时，我知道我有两个选择:

第一个选项大概是这样的:

"要连接到远程浏览器，您需要使用 ConnectAsync 函数:

```
var options = new ConnectOptions()
{
    BrowserWSEndpoint = $"wss://chrome.browserless.io?token={apikey}"
};

var browser = await Puppeteer.ConnectAsync(options);
var page = await browser.NewPageAsync(); 
```

结束了”

第二个选择更有趣一点:电报网络摄影师。

什么电视？

# 要求

我们需要实现一个 Azure 函数，它接收一个 URL 并返回该 URL 的截图。这个 Azure 功能不仅可以帮助电报摄影师，还可以帮助我们想要实现的任何其他服务。

我们还想在中实现一个电报机器人。NET 核心并使用 Docker 容器部署它。这个机器人会监听截图请求，调用我们的 Azure 函数，并将图像返回给客户端。

# 我们开始吧

## 天蓝色功能

由于我们无法在 Azure 函数中执行 Chrome，我们将需要使用 SaaS Chrome，如 browserless.io.
一旦我们知道了 Chrome 实例的 URL，我们就可以使用`ConnectAsync`函数连接到这个外部 Chrome 进程。

```
var options = new ConnectOptions()
{
    BrowserWSEndpoint = $"wss://chrome.browserless.io?token={apikey}"
};

var browser = await Puppeteer.ConnectAsync(options); 
```

我们的 Azure 函数看起来会像这样:

```
namespace ScreenshotFunction
{
    public static class TakeScreenshot
    {
        [FunctionName("TakeScreenshot")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequest req, 
            TraceWriter log,
            Microsoft.Azure.WebJobs.ExecutionContext context)
        {
            var config = new ConfigurationBuilder()
                .SetBasePath(context.FunctionAppDirectory)
                .AddJsonFile("local.settings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build();

            string url = req.Query["url"];
            if (url == null)
            {
                return new BadRequestObjectResult("Please pass a name in the query string");
            }
            else
            {
                string apikey = config["browserlessApiKey"];
                var options = new ConnectOptions()
                {
                    BrowserWSEndpoint = $"wss://chrome.browserless.io?token={apikey}"
                };

                var browser = await Puppeteer.ConnectAsync(options);
                var page = await browser.NewPageAsync();

                await page.GoToAsync(url);
                var stream = await page.ScreenshotStreamAsync(new ScreenshotOptions
                {
                    FullPage = true
                });

                byte[] bytesInStream = new byte[stream.Length];
                stream.Read(bytesInStream, 0, bytesInStream.Length);
                stream.Dispose();

                await page.CloseAsync();
                browser.Disconnect();

                return new FileContentResult(bytesInStream, "image/png");
            }
        }
    }
} 
```

小菜一碟！

## 在电报上创建机器人

首先，我们需要创建我们的电报机器人。让我们和 BotFather 聊一聊:

[![chat](img/f9051981d74ad4d091de64cc49f19845.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0q5bkF9j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/kblok/kblok.github.io/raw/master/img/webphotographer/CreatingABot.png)

## 控制台 app

多亏了 [Telegram，实现一个电报机器人非常容易。Bot](https://www.nuget.org/packages/Telegram.Bot/) 。我们来连线电报:

```
private static ManualResetEvent Wait = new ManualResetEvent(false);

static void Main(string[] args)
{
    string telegramApiKey = Environment.GetEnvironmentVariable("WEBPHOTOGRAPHER_APIKEY");
    var botClient = new TelegramBotClient(telegramApiKey);

    botClient.OnMessage += BotClient_OnMessage;

    botClient.StartReceiving(Array.Empty<UpdateType>());
    Console.WriteLine("Telegram Bot Started\npress any key to exit");
    Wait.WaitOne();
    botClient.StopReceiving();
} 
```

如果你对`ManualResetEvent`类有疑问；这只是在 Docker 容器中实现这一过程的一个方法。简单的`Console.ReadLine`在那里行不通。

然后，在消息事件上，我们需要监听 URL，调用我们的 Azure 函数并将图像发送回客户端。

```
private static async void BotClient_OnMessage(object sender, Telegram.Bot.Args.MessageEventArgs e)
{
    var linkParser = new Regex(@"\b(?:https?://|www\.)\S+\b", RegexOptions.Compiled | RegexOptions.IgnoreCase);
    var bot = (TelegramBotClient)sender;

    if (!string.IsNullOrEmpty(e.Message.Text))
    {
        foreach (Match m in linkParser.Matches(e.Message.Text))
        {
            await bot.SendTextMessageAsync(e.Message.Chat.Id, "Prepping a screenshot for you my friend");

            var url = (m.Value.StartsWith("http") ? string.Empty : "https://") + m.Value;
            MemoryStream stream = null;

            try
            {
                var data = await new WebClient().DownloadDataTaskAsync(azureFunction + url);
                stream = new MemoryStream(data);

                await bot.SendPhotoAsync(
                    e.Message.Chat.Id,
                    new Telegram.Bot.Types.FileToSend("url", stream),
                    m.Value);

            }
            catch(Exception ex)
            {
                await bot.SendTextMessageAsync(e.Message.Chat.Id, "Unable to get a screenshot for you");
            }
            finally
            {
                stream?.Close();
            }
        }
    }
} 
```

## 部署时间！

关于部署 Azure 功能没有太多要说的:右键单击，发布，下一个，下一个，下一个，它就会出现在 Azure 中。

为了在 Docker 中部署我们的控制台应用程序，我们需要这两个文件:

A Dockerfile file:

```
FROM microsoft/dotnet:2.0-sdk
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# copy and build everything else
COPY . ./
RUN dotnet publish -c Release -o out
ENTRYPOINT ["dotnet", "out/WebPhotographerBot.dll"] 
```

和坞站-合成. yml 文件:

```
version: '2.1'

services:
  telegrambotfriend:
    image: telegramwebphotographer
    build: .
    environment:
      - WEBPHOTOGRAPHER_APIKEY=BOT_APIKEY
      - WEBPHOTOGRAPHER_AZUREFUNCTION=FUNCTIONENDPOINT 
```

我们包含了真正的电报 API 密钥和 Azure 函数端点。

最后，我们运行`docker-compose up`，让我们的机器人启动并运行。我们来看看这个！

[![demo](img/787cd37fd2d5ca38e5d6a00b2bb9afa3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oi0o0134--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/kblok/kblok.github.io/raw/e320f821e48ae160a72a98be8bcd06c4c29ad9f4/img/webphotographer/Chat.png)

# 最后的话

首先，这是一个概念证明。不要在生产中使用这个代码！
其次，我知道这个解决方案有点过度设计了。这个想法是展示如何连接到一个远程的 Chrome 实例，并玩一点。NET Core 和 Docker。
总结一下，你可以在 Github 上找到这个代码，名为 [telegram-webphotographer](https://github.com/kblok/telegram-webphotographer) ，你可以随意使用它。

不要停止编码！

最初发布于[harkoded.com](https://www.hardkoded.com/blogs/azure-chrome-puppeteer-browserless)