# 如何构建一个带有推送通道的实时电子表格应用程序——第 4 部分:自动清理空文档

> 原文：<https://dev.to/shalvah/how-to-build-a-realtime-spreadsheets-application-with-pusher-channels---part-4-automatically-cleaning-up-empty-documents-57pj>

欢迎来到构建类似 Google Sheets 的电子表格编辑器的最后一部分。在本系列的前几部分中，我们已经实现了多个用户的实时同步编辑。

在本文中，我们将通过使用 Pusher 自动清理空文档来进一步扩展我们的应用程序功能，这样我们就不会将它们留在用户的历史记录(和我们的数据库)中。

[![](../Images/dd45728306809484c9e319810840e2d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--70wZmThh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_75C08CE87DF85DBF796E5DB651FDC17E0FE378B8D0604878A0DCE11C3EE8A962_1524848789025_building-realtime-spreadsheets-app-laravel-cleaning-up-empty-documents.gif)

## 先决条件

1.  PHP 7.2 以上，安装了 MongoDB 驱动。你可以在这里找到安装说明[。](http://php.net/manual/en/mongodb.installation.php)
2.  设计者
3.  MongoDB(3.4 或更高版本)。拿过来[这里](https://docs.mongodb.com/manual/installation/)。
4.  推销账户。在这里创建一个。

## 设置

> 注意:如果您已经完成了本系列的前几部分，并且仍然拥有您的项目代码，那么您可以跳过这一部分。

从 GitHub 克隆项目:

```
git clone -b part-3-whos-viewing https://github.com/shalvah/shoots.git 
```

Enter fullscreen mode Exit fullscreen mode

或者直接从[这个链接下载源码。](https://github.com/shalvah/shoots/archive/part-3-whos-viewing.zip)

然后将`cd`放入项目文件夹并安装依赖项:

```
composer install 
```

Enter fullscreen mode Exit fullscreen mode

最后，将`.env.example`复制到名为`.env`的新文件中。将您的 Pusher 应用凭证添加到`.env`文件:

```
PUSHER_APP_ID=your-app-id
PUSHER_APP_KEY=your-app-key
PUSHER_APP_SECRET=your-app-secret
PUSHER_APP_CLUSTER=your-app-cluster 
```

Enter fullscreen mode Exit fullscreen mode

在`resources/views/spreadsheet.blade.php` :
中查找这些 JavaScript 行

```
let pusher = new Pusher('your-app-key', {
    cluster: 'your-app-cluster'
}); 
```

Enter fullscreen mode Exit fullscreen mode

将您的 Pusher 应用程序密钥和群集插入适当的位置。

运行以下命令来生成应用程序加密密钥:

```
php artisan key:generate 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:如果您的 MongoDB 服务器需要用户名和密码，那么在您的`.env`文件中添加用户名和密码，分别作为`DB_USERNAME`和`DB_PASSWORD`。

## 问题

目前，当用户点击仪表板上的**创建新电子表格**按钮时会发生以下情况:

1.  数据库中会创建一个新的电子表格。
2.  用户被重定向到电子表格的视图，在那里他们可以编辑电子表格。
3.  当用户编辑工作表时，更改被同步到后端并保存到数据库。

问:如果用户在第二步后改变主意，关闭浏览器或离开页面**而不对工作表**做任何更改，会发生什么？

现在，没什么特别的。该文档仍然存在，并且显示在仪表板上的用户文档列表中。

但是，在 Gmail(撰写新邮件)或 Google Docs/Sheets(编辑新文档)等 web 应用程序中，如果您没有进行任何更改就退出页面，文档或草稿会自动被丢弃。

所以，我们开始吧。我们不会在用户的文档历史中塞满空文档，而是会删除用户没有修改的文档。

## 我们的解决方案

为了实现这一点，我们将利用 Pusher 的[通道存在 webhooks](https://pusher.com/docs/webhooks#channel-existence) ，特别是`[channel_vacated](https://pusher.com/docs/webhooks#channel_vacated)`事件。每当通道的所有成员都离开时，就会触发此事件。我们将这样实现:

*   当所有用户都离开文档通道(通过关闭文档)时，Pusher 将向我们的 webhook 触发`channel_vacated`事件。
*   我们的 webhook 代码将从数据库中检索相应的工作表，并检查它是否仍然为空。如果是，我们删除它。

我们开始吧。首先，我们将为我们的`Sheet`模型(`app/Models/Sheet.php`)添加一个实用方法来确定一个工作表是否为空:

```
public function isEmpty(): bool
{
    if ($this->content == [[]]) {
        return true;
    }

    foreach ($this->content as $row) {
        foreach ($row as $cell) {
            if (!is_null($cell)) {
                return false;
            }
        }
    }

    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将在我们的`routes/api.php` :
中实现 webhook 路由

```
<?php

use App\Models\Sheet;
use Illuminate\Http\Request;

Route::post('sheets/webhook', function (Request $request) {
    $body = $request->post();
    foreach ($body['events'] as $event) {
        if ($event['name'] == 'channel_vacated') {
            $sheetId = str_replace('presence-sheet-', '', $event['channel']);
            $sheet = Sheet::find($sheetId);
            if ($sheet->isEmpty()) {
                $sheet->delete();
            }
        }
        http_response_code(200);
    }}); 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:我们将这个路由放在`routes/api.php`而不是`routes/wen.php`中，因为这个路由不会从浏览器中调用，我们不希望 Laravel 提供的`web`中间件应用于它。

最后，我们需要向 Pusher 注册这个 webhook。由于应用程序目前位于我们的本地机器上，我们需要一种通过公共 URL 公开它的方法。Ngrok 是一个简单易用的工具，可以帮助你做到这一点。在 http://ngrok.com[上注册，并按照说明安装 ngrok。然后通过运行:
在您的机器上暴露](http://ngrok.com) [http://localhost:8000](http://localhost:8000)

```
./ngrok http 8000 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到如下输出:

[![](../Images/286eb6658ba922a08486147674520f15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Te10PB_q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_75C08CE87DF85DBF796E5DB651FDC17E0FE378B8D0604878A0DCE11C3EE8A962_1524843624407_image.png)

复制第二个**转发** URL(使用 HTTPS 的那个)。您的 webhook URL 将会是`<your-ngrok-url>/api/sheets/webhook`(例如，对于上面的截图，我的 webhook URL 是`https://fa74c4e1.ngrok.io/api/sheets/webhook`)。

现在你需要在 Pusher 上注册 webhook。登录您的 [Pusher 应用仪表板](https://app.pusher.com)并选择您的电子表格应用。点击 **Webhooks** 选项卡，选择**频道存在**单选按钮。在文本框中，粘贴上面获得的 webhook 的 URL，点击**添加**。

现在，让我们来测试一下。通过运行`mongod`启动 MongoDB 服务器。(在 Linux/macOS 上，您可能需要作为`sudo`运行它)。

然后在新的终端窗口运行
来启动你的应用

```
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

在[http://localhost:8000/log in](http://localhost:8000/login)登录您的应用程序(如果您之前没有注册，请在[http://localhost:300/register](http://localhost:300/register)注册)，并创建一个新的电子表格。通过访问另一个 URL 离开该页面。然后回到仪表盘([http://localhost:8000/home](http://localhost:8000/home))。您应该会看到您刚刚创建的电子表格没有显示出来。

> 注意:在从列表中删除文档之前，您可能需要刷新我们的应用仪表板一次或多次。这是因为，由于我们从本地机器加载我们的仪表板，这比删除过程要快得多(删除过程必须通过互联网传输到 Pusher 并返回)。在生产服务器中，这通常不会发生。

## 结论

在这个由四部分组成的系列中，我们构建了一个电子表格应用程序，它利用 Pusher 的不同特性来提供 Google Sheets 的一些功能。我希望你和我一样关注并喜欢这个系列！你可以在 GitHub 上查看完整的源代码[。](https://github.com/shalvah/shoots)

*本文原载于[推手的博客](https://pusher.com/tutorials/realtime-spreadsheets-part-4)。*