# 如何使用推送通道构建实时电子表格应用程序——第 3 部分:显示当前查看者

> 原文：<https://dev.to/shalvah/how-to-build-a-realtime-spreadsheets-application-with-pusher---part-3-showing-current-viewers-2lfn>

在本系列的前两部分中，我们构建了一个电子表格应用程序，它支持多个用户同时编辑，在所有客户端上实时同步更改。

在本文中，我们将添加 Google Sheets 中的另一个有用的功能，即实时查看谁在查看工作表的功能。我们将显示与当前查看表单的每个用户相对应的头像。这是我们的应用程序的外观:

[![](img/ca44c89628beeff3922e15bb2234a978.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ltpWR4t3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_E865783628DF374E07AD41774F75FC9C9CEBE398DCA5A3373AADB6CDBB8FE311_1524848803309_building-realtime-spreadsheets-app-laravel-whos-viewing.gif)

## 先决条件

1.  PHP 7.2 以上，安装了 MongoDB 驱动。你可以在这里找到安装说明[。](http://php.net/manual/en/mongodb.installation.php)
2.  设计者
3.  MongoDB(3.4 或更高版本)。拿过来[这里](https://docs.mongodb.com/manual/installation/)。
4.  推销账户。在这里创建一个。

## 设置

> 注意:如果您完成了第二部分，并且仍然有您的项目代码，那么您可以跳过这一部分。

从 GitHub 克隆项目:

```
git clone -b part-2-realtime-collaboration https://github.com/shalvah/shoots 
```

Enter fullscreen mode Exit fullscreen mode

或者直接从[这个链接下载源码。](https://github.com/shalvah/shoots/archive/part-2-realtime-collaboration.zip)

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

## 用推动器实现在场

在本系列的上一部分中，当用户打开一个表单时，我们为每个用户订阅一个存在通道。该通道的名称与工作表的 ID 相关联。这个在线频道将提供我们的“正在观看”功能的核心，因为在线频道向我们提供了关于谁订阅了某个频道的信息。我们将挂钩到某些[存在事件](https://pusher.com/docs/client_api_guide/client_presence_channels#events)由频道上的推送器触发。

我们将这样做:

*   当用户成功订阅频道时，事件`[**pusher:subscription_succeeded**](https://pusher.com/docs/client_api_guide/client_presence_channels#pusher-subscription-succeeded)`被触发。包含的有效负载包含所有订阅成员的列表。我们将监听这个事件，并使用有效负载来显示当我们打开工作表时查看它的每个人的头像。
*   当一个新用户加入通道时(其他人打开表单)，触发事件[**pusher:member _ added**](https://pusher.com/docs/client_api_guide/client_presence_channels#pusher-member-added)* * * *。我们将监听此消息，并将新成员的头像添加到我们的 UI 中。
*   当用户离开频道(关闭浏览器窗口)时，触发事件[**pusher:member _ removed**](https://pusher.com/docs/client_api_guide/client_presence_channels#pusher-member-removed)* * * *。当这种情况发生时，我们将从我们的 UI 中删除该成员的头像。

我们走吧！

## 创建标记

首先，让我们为我们的头像创建标记。头像本质上是彩色圆形背景上的用户姓名首字母(类似于谷歌的默认用户头像)。当我们悬停在它上面时，它应该会显示一个包含用户名的工具提示。

我们将使用 Bootstrap 和 jQuery 来设计我们的头像和工具提示。打开您的`resources/views/spreadsheet.blade.php`，在结束的`</head>`标签:
之前包含引导和 jQuery 资产

```
<head>
  ...

  <link
    rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" 
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
  <script src="https://code.jquery.com/jquery-3.3.1.min.js"
    integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
    crossorigin="anonymous">
  </script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa"
    crossorigin="anonymous">
  </script>
</head> 
```

Enter fullscreen mode Exit fullscreen mode

在结束的`</head>`标签:
之后，您应该有这样的代码

```
<h2>{{ $sheet->name }}</h2>
<div id="sheet"></div> 
```

Enter fullscreen mode Exit fullscreen mode

修改它，使它看起来像这样:

```
<h2>{{ $sheet->name }}</h2>
<p>
  <span style="float: right; margin-right: 50px; margin-bottom: 40px; font-size: 16px;">Now viewing: <span id="viewers"></span>
  </span>
</p>
<br> <br>

<div id="sheet"></div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，在结束的`</head>`标签之前添加`avatar`类的样式:

```
<style>
    .avatar {
        color: rgb(255, 255, 255);
        background-color: #fc0093;
        display: inline-block;
        font-family: Arial, sans-serif;
        font-size: 20px;
        border-radius: 50%;
        width: 36px;
        height: 36px;
        text-align: center;
    }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 响应在场事件

让我们修改我们的推送订阅代码，以响应我们上面考虑的事件。在你的`resources/views/spreadsheet.blade.php`中，你应该有这样的代码:

```
pusher.subscribe("{{ $sheet->channel_name }}")
    .bind('updated', function (message) {
        let [rowIndex, columnIndex, oldValue, newValue] = message.change;
        addCellValue(rowIndex, columnIndex, newValue);
        table.loadData(sheetContent);
    }); 
```

Enter fullscreen mode Exit fullscreen mode

修改这些行，如下所示:

```
pusher.subscribe("{{ $sheet->channel_name }}")
    .bind('pusher:subscription_succeeded', (data) => {
        Object.entries(data.members)
            .forEach(([id, member]) => addViewer(member));
    })
    .bind('pusher:member_added', (member) => addViewer(member.info))
    .bind('pusher:member_removed', (member) => removeViewer(member))
    .bind('updated', function (message) {
        let [rowIndex, columnIndex, oldValue, newValue] = message.change;
        addCellValue(rowIndex, columnIndex, newValue);
        table.loadData(sheetContent);
    }); 
```

Enter fullscreen mode Exit fullscreen mode

我们要做的最后一件事是实现`addViewer`和`removeViewer`函数。将以下代码添加到您的`resources/views/spreadsheet.blade.php`的末尾:

```
<script>
    function addViewer(viewer) {
        const userInitials = viewer.name.split(' ')
            .reduce((initials, name) => {
                initials.push(name[0]);
                return initials;
            }, []).join('');
        let $avatar = $('<span>')
            .addClass('avatar')
            .attr('data-toggle', 'tooltip')
            .attr('id', `avatar-${viewer._id}`)
            .attr('title', viewer.name)
            .text(userInitials);
        $('#viewers').append($avatar);
        // enable the tooltip
        $avatar.tooltip();
    }

    function removeViewer(viewer) {
        $(`#avatar-${viewer.id}`).remove();
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

`addViewer`函数创建 avatar 元素并使用 jQuery 将其添加到 DOM 中，而`removeViewer`函数定位对应于指定查看器的 avatar 并删除它。

全部完成！让我们测试我们的应用程序。通过运行`mongod`启动 MongoDB 服务器。(在 Linux/macOS 上，您可能需要作为`sudo`运行它)。

然后运行
来启动你的应用程序

```
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

在 [http://localhost:8000](http://localhost:8000) 登录您的应用程序，并创建一个新的电子表格。在不同的浏览器中，在[http://localhost:8000/register](http://localhost:8000/register)注册为新用户。然后复制您在第一个浏览器中创建的电子表格的 URL，并在第二个浏览器中打开它。您应该看到用户头像在打开和关闭表单时显示在右上角。

## 结论

到目前为止，多亏了 Pusher，我们已经能够构建一个具有实时协作和“现在查看”功能的电子表格应用程序。在下一部分中，我们将看到如何使用 Pusher 添加另一个漂亮的 Google Sheets 特性——当空文档关闭时自动删除它们。敬请期待！

你可以在 GitHub 上查看这个 app [的源代码。](https://github.com/shalvah/shoots/tree/part-3-whos-viewing)

*本文原载于[推手的博客](https://pusher.com/tutorials/realtime-spreadsheets-part-3)。*