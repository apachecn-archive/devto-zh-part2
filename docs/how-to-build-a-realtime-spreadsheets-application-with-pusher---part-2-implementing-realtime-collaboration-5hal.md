# 如何用推送通道构建实时电子表格应用程序——第 2 部分:实现实时协作

> 原文：<https://dev.to/shalvah/how-to-build-a-realtime-spreadsheets-application-with-pusher---part-2-implementing-realtime-collaboration-5hal>

在本系列的第一部分中，我们创建了一个基本的电子表格应用程序，允许用户创建电子表格。用户还可以与其他用户共享工作表的链接，从而使他们能够编辑电子表格。

在本文中，我们将通过添加实时协作功能，使我们的应用程序功能更接近 Google Sheets。这意味着多个用户可以同时在同一个工作表中工作，并且他们的更改会立即显示在所有其他窗口中。以下是最终结果的一瞥:

[![](img/bf93c9d533387eff7ca1ddd06629395a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n2GPnbX---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_91E250D49C2F46522A90C69B0F2BA43B443748C32F623C6DFC50A0C7F5A0C71E_1524778753133_building-realtime-spreadsheets-app-laravel.gif)

## 先决条件

1.  PHP 7.2 以上，安装了 MongoDB 驱动。你可以在这里找到安装说明[。](http://php.net/manual/en/mongodb.installation.php)
2.  设计者
3.  MongoDB(3.4 或更高版本)。拿过来[这里](https://docs.mongodb.com/manual/installation/)。
4.  推销账户。在这里创建一个。

## 设置

> 注意:如果您已经完成了第一部分，并且仍然有项目代码，那么您可以跳过这一部分。

从 GitHub 克隆项目:

```
git clone -b part-1-users-spreadsheets https://github.com/shalvah/shoots.git 
```

或者直接从[这个链接下载源码。](https://github.com/shalvah/shoots/archive/part-1-users-spreadsheets.zip)

然后`cd`进入项目文件夹并安装依赖项:

```
composer install 
```

最后，将`.env.example`复制到名为`.env`的新文件中。运行以下命令生成应用程序加密密钥:

```
php artisan key:generate 
```

> 注意:如果您的 MongoDB 服务器需要用户名和密码，那么在您的`.env`文件中添加用户名和密码，分别作为`DB_USERNAME`和`DB_PASSWORD`。

## 制作实时编辑

我们的实时同步有两个部分:

*   每当用户打开一个表单，我们将为他们订阅一个特定的推送频道，由表单的 ID 标识。
*   当用户修改表单时( [Handsontable 的](https://github.com/handsontable/handsontable) `afterChange`事件)，我们通过 AJAX 将更新发送到服务器。然后，服务器将更新数据库中的表单内容，并在上面的频道上向所有订阅的用户广播这一更改，这样 UI 就可以相应地更新

我们开始吧！首先，我们将向工作表添加一个虚拟属性，`channel_name`。该属性将返回我们应该订阅的推送通道的名称，以便在该表单发生更改时得到通知。将以下方法添加到您的`Sheet`模型(`app/Models/Sheet.php`)中:

```
public function getChannelNameAttribute()
{
    return "presence-sheet-$this->_id";
} 
```

通道名称以`presence-`开头，表示这是一个[存在通道](https://pusher.com/docs/client_api_guide/client_presence_channels)。我们使用在线频道，因为它为我们提供了关于谁订阅了频道的**的附加信息，这是我们在本指南的下一部分需要的。**

现在，到我们的前端。用以下代码替换`resources/views/spreadsheet.blade.php`中的代码:

```
<head>
    
    <meta name="csrf-token" content="">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/handsontable/2.0.0/handsontable.min.css" t
    <!-- Fonts -->
    <link rel="dns-prefetch" href="https://fonts.gstatic.com">
    <link href="https://fonts.googleapis.com/css?family=Raleway:300,400,600" rel="stylesheet" type="text/css">

    <!-- Styles -->
    <link href="" rel="stylesheet">
</head>

<br>
<h2></h2>
<div id="sheet"></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/handsontable/2.0.0/handsontable.min.js"></script>
<script>
    let csrfToken = document.head.querySelector('meta[name="csrf-token"]').content;
    let sheetContent = @json($sheet->content);

    let container = document.getElementById('sheet');
    let table = new Handsontable(container, {
        data: sheetContent,
        rowHeaders: true,
        colHeaders: true,
        minCols: 20,
        minRows: 20,
        afterChange: function (change, source) {
            if (source === 'loadData') return;

            console.log(change, source);

            fetch('/sheets/', {
                method: 'PUT',
                body: JSON.stringify({ change: change[0] }),
                headers: {
                    'X-CSRF-TOKEN': csrfToken,
                    'Content-Type': 'application/json'
                },
                credentials: 'same-origin'
            })
        }
    });
</script> 
```

让我们仔细看看代码。每当表中的数据发生变化时，Handsontable 就会触发`afterChange`事件，无论是由于从服务器加载数据还是由于用户更改单元格。当这种情况发生时，我们的回调用两个参数调用:

*   **变更**是包含被变更的单元格的数组。单元格表示为一个包含四个元素的数组:行索引、列索引、旧值和新值。
*   **源**是“loadData”或“edit ”,这取决于上面提到的应用场景。

例如，让我们假设我们有一个空表。当页面被加载时，`afterChange`被触发，`change`为空数组，`source`为“loadData”。让我们假设我们的表看起来像这样:

```
 |   A   |   B   |   C 
---------------------------
1 |
2 |
3 | 
```

假设用户编辑单元格 A2 以包含值“hi”:

```
 |   A   |   B   |   C 
---------------------------
1 |
2 | hi
3 | 
```

当`source`为“编辑”时，`afterChange`事件将被触发。`change`参数将如下所示:

```
[
  [1, 0, null, 'hi']
] 
```

然后，假设用户改变主意，将“嗨”切换为“你好”，`change`将是:

```
[
  [1, 0, 'hi', "hello"]
] 
```

在这两种情况下:

*   第一个元素是行索引(索引从 0 开始，所以第 2 行是索引 1)
*   第二个元素是列索引(也是零索引)
*   第三个元素是单元格的旧值(如果单元格为空，则为`null`)
*   第四个元素是新值

在我们传递给上面的`afterChange`的回调中，我们使用[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)向服务器发送新的更改。让我们更新控制器来处理这个问题。

首先，我们需要安装和配置 Pusher 包，因为我们将向所有连接的客户机广播这一变化。运行以下命令:

```
composer require pusher/pusher-http-laravel
php artisan vendor:publish --provider="Pusher\Laravel\PusherServiceProvider" 
```

登录[你的 Pusher dashboard](http://app.pusher.com) 并创建一个新应用。从**应用密钥**部分复制您的应用凭证，并将其添加到您的`.env`文件中:

```
PUSHER_APP_ID=your-app-id
PUSHER_APP_KEY=your-app-key
PUSHER_APP_SECRET=your-app-secret
PUSHER_APP_CLUSTER=your-app-cluster 
```

> 注意:Laravel 有时会缓存旧的配置，所以为了让项目看到新的配置值，您可能需要运行命令`php artisan config:clear`

修改您的`app/Http/Controllers/SheetsController.php`的`update`方法，如下所示:

```
public function update($id)
{
    $sheet = Sheet::findOrFail($id);
    $change = \request('change');
    [$rowIndex, $columnIndex, $oldValue, $newValue] = $change;
    $sheet->content = $this->updateCell($rowIndex, $columnIndex, $newValue, $sheet->content);
    $sheet->save();
    \Pusher::trigger($sheet->channel_name, 'updated', ['change' => $change]);
    return response()->json(['sheet' => $sheet]);
}

protected function updateCell($rowIndex, $columnIndex, $newValue, $sheetContent)
{
    // we expand the sheet to reach the farthest cell
    for ($row = 0; $row <= $rowIndex; $row++) {
       // create the row if it doesnt exist
        if (!isset($sheetContent[$row])) {
            $sheetContent[$row] = [];
        }
        for ($column = 0; $column <= $columnIndex; $column++) {
            if (!isset($sheetContent[$row][$column])) {
                // create the column if it doesnt exist
                $sheetContent[$row][$column] = null;
            }
        }
    }
    $sheetContent[$rowIndex][$columnIndex] = $newValue;
    return $sheetContent;
} 
```

在这里，我们更新了工作表内容中发生变化的特定单元格，然后通过 Pusher 触发一个`updated`事件，将变化作为有效载荷。

> 注意:我们在这里使用了一个非常简单的方法:用新内容替换单元格中的旧内容。但是，在大型应用程序中，这可能会导致冲突，所以我们通常会使用更复杂的算法，比如操作转换(超出了本文的范围)。
> 
> 注意:在我们的`updateCell`函数中，我们扩展工作表到达最远的列，用`null`填充所有的空单元格。这样我们的工作表就不会以缺少行和列而结束

让我们回到前端，添加处理这个事件的代码。将此添加到您的`resources/views/spreadsheet.blade.php`底部:

```
<script src="https://js.pusher.com/4.2/pusher.min.js"></script>
<script>
    let pusher = new Pusher('your-app-key', {
        cluster: 'your-app-cluster',
        authEndpoint: '/sheets//subscription_auth',
        auth: {
            headers: {
                'X-CSRF-Token': csrfToken
            }
        }
    });
    pusher.subscribe("")
        .bind('updated', function (message) {
            let [rowIndex, columnIndex, oldValue, newValue] = message.change;
            addCellValue(rowIndex, columnIndex, newValue);
            table.loadData(sheetContent)
    });

    function addCellValue(rowIndex, columnIndex, newValue) {
      // we expand the sheet to reach the farthest cell
      for (let row = 0; row <= rowIndex; row++) {
          if (!sheetContent[row]) sheetContent[row] = [];
          for (let column = 0; column <= columnIndex; column++) {
              if (!sheetContent[row][column])
                sheetContent[row][column] = null;
          }
      }
      sheetContent[rowIndex][columnIndex] = newValue;
}
</script> 
```

将上面代码中的`your-app-key`和`your-app-cluster`替换为您之前从仪表盘中获得的 Pusher 应用程序密钥和集群。

在这段代码中:

*   我们初始化我们的 Pusher 客户端并订阅表单的频道。
*   由于存在通道需要认证，我们提供了一个`authEndpoint`，Pusher 将调用它来确定当前用户是否被允许订阅这个表单。
*   我们绑定到`updated`事件并更新表单的内容，就像我们在服务器上做的那样(在`addCellValue`函数中)，然后我们通过调用`table.loadData`刷新表格 UI。

我们需要实现的最后一块拼图是我们的身份验证端点。让我们现在做那件事。将此添加到您的`routes/web.php`的末尾:

```
Route::post('sheets/{id}/subscription_auth', 'SheetsController@authenticateForSubscription'); 
```

现在，将`authenticateForSubscription`添加到您的`app/Http/Controllers/SheetsController.php`中:

```
public function authenticateForSubscription($id)
{
    $authSignature = \Pusher::presence_auth(
        \request('channel_name'),
        \request('socket_id'),
        \Auth::user()->_id,
        \Auth::user()->toArray()
    );
    return response()->json(json_decode($authSignature));
} 
```

太好了。现在，让我们测试一下我们的应用程序。通过运行`mongod`启动 MongoDB 服务器。(在 Linux/macOS 上，您可能需要作为`sudo`运行它)。

然后运行以下命令启动您的应用程序:

```
php artisan serve 
```

在 http://localhost:8000/登录您的应用程序。登录(或者[http://localhost:8000/register](http://localhost:8000/register)，如果您在上一部分没有注册的话)并创建一个新的电子表格。从浏览器中拷贝电子表格的 url，并在第二个标签中打开它(以不同用户或相同用户的身份)。您应该能够在两个选项卡中对工作表进行更改，并看到它们实时显示出来。

## 结论

那很有趣，不是吗？但是还有更多的。在本系列的下一部分，我们将复制 Google Sheets 的另一个优秀特性:显示哪些用户当前正在实时查看电子表格。敬请关注。你可以在 GitHub 上查看这个应用的源代码。

*这篇文章最初出现在[推手的博客](https://pusher.com/tutorials/realtime-spreadsheets-part-2)上。*