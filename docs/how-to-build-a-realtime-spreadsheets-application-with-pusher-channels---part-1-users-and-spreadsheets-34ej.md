# 如何使用推送通道构建实时电子表格应用程序——第 1 部分:用户和电子表格

> 原文：<https://dev.to/shalvah/how-to-build-a-realtime-spreadsheets-application-with-pusher-channels---part-1-users-and-spreadsheets-34ej>

像 Google Docs 和 Google Sheets 这样的应用程序今天非常受欢迎，部分原因是它们允许用户轻松地共享文档，以便其他人进行协作。它们还使多个用户能够同时处理同一个文档，而不会发生冲突或担心数据丢失。

在这个由四部分组成的指南中，我们将构建一个类似于 Google Sheets 的电子表格应用程序。用户可以创建电子表格并与其他人共享该工作表的链接，使该人能够在工作表中实时协作，而不会覆盖彼此的编辑。我们的应用程序还将显示当前正在查看表单的用户。

## 先决条件

1.  PHP 7.2 以上，安装了 MongoDB 驱动。你可以在这里找到安装说明[。](http://php.net/manual/en/mongodb.installation.php)
2.  设计者
3.  MongoDB(3.4 或更高版本)。拿过来[这里](https://docs.mongodb.com/manual/installation/)。
4.  推销账户。在这里创建一个。

## 设置 app

Laravel 默认使用 SQL 数据库作为其雄辩模型的后端，但是我们在这个项目中使用 MongoDB，所以我们将从配置为使用 MongoDB 的 Laravel 安装开始。通过运行:
克隆存储库

```
git clone https://github.com/shalvah/laravel-mongodb-starter.git 
```

Enter fullscreen mode Exit fullscreen mode

你也可以直接从[这个链接下载源码。](https://github.com/shalvah/laravel-mongodb-starter/archive/master.zip)

然后将`cd`放入项目文件夹并安装依赖项:

```
composer install 
```

Enter fullscreen mode Exit fullscreen mode

最后，将`.env.example`复制到名为`.env`的新文件中。运行以下命令来生成应用程序加密密钥:

```
php artisan key:generate 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:如果您的 MongoDB 服务器需要用户名和密码，那么在您的`.env`文件中添加用户名和密码，分别作为`DB_USERNAME`和`DB_PASSWORD`。

## 用户认证

我们将通过运行:
来利用 Laravel 自带的内置用户认证系统

```
php artisan make:auth 
```

Enter fullscreen mode Exit fullscreen mode

我们需要配置一些东西。将您的`app/Http/Controllers/Auth/RegisterController.php`的`create`方法替换为以下内容:

```
 protected function create()
 {
    return \App\Models\User::create([
        'name' => $data['name'],
        'email' => $data['email'],
        'password' => Hash::make($data['password']),
        'viewed_sheets' => []
    ]);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的`app/Models/User.php`中，添加`'` `viewed_sheets` `'` `\`作为`$fillable`数组中的一个条目:

```
protected $fillable = [
    'name', 'email', 'password', 'viewed_sheets'
]; 
```

Enter fullscreen mode Exit fullscreen mode

属性是我们存储用户最近打开的所有工作表的 id 的地方，所以我们可以在用户的仪表板上显示它们。

## 构建用户仪表盘

现在，让我们构建一个用户登录时可以看到的仪表板。与 Google Sheets 类似，我们将显示他们最近查看的电子表格列表，以及一个创建新电子表格的按钮。用以下内容替换您的`resources/views/home.blade.php`中的内容:

```
@extends('layouts.app')

@section('content')
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Dashboard</div>

                    <div class="card-body">
                        <div class="text-center">
                            <a class="btn btn-lg btn-primary" href="{{ route('sheets.new') }}">Create new spreadsheet</a>
                        </div>

                        <div class="list-group">
                            @if($sheets = \Auth::user()->viewedSheets())
                                @foreach($sheets as $sheet)
                                        <a href="/sheets/{{ $sheet->_id }}" class="list-group-item">
                                            {{ $sheet->name }}
                                        </a>
                                @endforeach
                            @endif
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
@endsection 
```

Enter fullscreen mode Exit fullscreen mode

我们将向我们的`User`模型添加一个新方法`viewedSheets`。该方法将搜索 id 在`viewed_sheets`属性中的所有工作表并检索它们。首先，创建`Sheet`模型(`app/Models/Sheet.php)`，内容如下:

```
<?php

namespace App\Models;

use Jenssegers\Mongodb\Eloquent\Model;

class Sheet extends Model
{
    protected $guarded = [];
} 
```

Enter fullscreen mode Exit fullscreen mode

然后将`viewedSheets`方法添加到您的`app/Models/User.php` :

```
public function viewedSheets()
{
    return \App\Models\Sheet::whereIn('_id', $this->viewed_sheets)->get();
} 
```

Enter fullscreen mode Exit fullscreen mode

## 处理工作表

在这一点上，我们还有几件事要做:

*   点击**创建电子表格**按钮应该会创建一个新的空白表格，并打开它进行编辑
*   单击最近查看的工作表列表中的电子表格也应该可以打开它进行编辑
*   我们的应用程序将启用自动保存；我们将通过 AJAX 保存用户对表单的修改。这意味着我们需要一个端点来更新我们的工作表

我们的床单将具有以下特性:

*   一个 **ID。** MongoDB 自动为我们生成这个代码为`_id`
*   一个**名字。**(现在，工作表将被称为“无标题电子表格”)
*   一辆**车主**。我们将把它存储为`_owner`。(`_`表示它是一个 ID。)
*   **行和列中的内容**。我们将把它存储为一个行数组。每行是一个数组，每个条目是一列。

例如，使用这样的表格:

```
 | A | B | C 
--------------
1 |
2 |
3 |
4 | 
```

Enter fullscreen mode Exit fullscreen mode

列和行将表示为:

```
content = [
  [
    'A1', 'B1', 'C1'
  ],
  [
    'A2', 'B2', 'C2'
  ],
  [
    'A3', 'B3', 'C3',
  ],,
  [
    'A4', 'B4', 'C4',
  ],
]; 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建我们需要的路径:分别用于创建、查看和更新工作表。将以下内容添加到您的`routes/web.php`的末尾:

```
Route::get('sheets/new', 'SheetsController@newSheet')->name('sheets.new');
Route::get('sheets/{sheet}', 'SheetsController@view')->name('sheets.view');
Route::put('sheets/{id}', 'SheetsController@update'); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将在控制器中实现这些逻辑。创建文件`app/Http/Controllers/SheetsController.php`，内容如下:

```
<?php

namespace App\Http\Controllers;

use App\Models\Sheet;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class SheetsController extends Controller
{

    public function __construct()
    {
        $this->middleware('auth');
    }

    public function newSheet()
    {
        $sheet = Sheet::create([
            'name' => 'Untitled spreadsheet',
            '_owner' => Auth::user()->_id,
            'content' => [[]]
        ]);
        return redirect(route('sheets.view', ['sheet' => $sheet]));
    }

    public function view(Sheet $sheet)
    {
        Auth::user()->push('viewed_sheets', $sheet->_id);
        return view('spreadsheet', ['sheet' => $sheet]);
    }

    public function update($id)
    {
        $sheet = Sheet::where('_id', $id)->update(['content' => \request('content') ?: [[]]]);
        return response()->json(['sheet' => $sheet]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在剩下的就是景色了。我们将使用 [Handsontable](https://github.com/handsontable/handsontable) ，一个为我们提供电子表格界面的库。创建文件`spreadsheet.blade.php`，内容如下:

```
<head>
    {{ $sheet->name }}
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/handsontable/2.0.0/handsontable.min.css" t
    <!-- Fonts -->
    <link rel="dns-prefetch" href="https://fonts.gstatic.com">
    <link href="https://fonts.googleapis.com/css?family=Raleway:300,400,600" rel="stylesheet" type="text/css">

    <!-- Styles -->
    <link href="{{ asset('css/app.css') }}" rel="stylesheet">
</head>

<br>
<h2>{{ $sheet->name }}</h2>
<div id="sheet"></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/handsontable/2.0.0/handsontable.min.js"></script>
<script>
    let csrfToken = document.head.querySelector('meta[name="csrf-token"]').content;
    let data = @json($sheet->content);

    let container = document.getElementById('sheet');
    let table = new Handsontable(container, {
        data: data,
        rowHeaders: true,
        colHeaders: true,
        minCols: 20,
        minRows: 20,
        afterChange: function (change, source) {
            if (source === 'loadData') return;

            console.log(change, source);

            fetch('/sheets/{{ $sheet->_id }}', {
                method: 'PUT',
                body: JSON.stringify({content: data}),
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

Enter fullscreen mode Exit fullscreen mode

事情是这样的:

*   我们初始化包含工作表`content`中数据的手动电子表格。`data`变量被绑定为引用。这意味着每当用户对电子表格进行修改时，`data`的值就会被 Handsontable 自动更新以包含新的修改
*   我们监听`afterChange`事件。每当用户编辑完一个单元格(例如，他更改了一个单元格中的值并按下 **Enter** )时，就会触发该事件。当这个事件被触发时，我们获取`data`的当前值，并向我们的后端发出 HTTP 请求(使用[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API))来更新数据库中的工作表。

通过运行`mongod`启动 MongoDB 服务器。注意:在 Linux/macOS 上，您可能需要以`sudo`的身份运行它。

然后运行
来启动你的应用程序

```
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

在[http://localhost:8000/register](http://localhost:8000/register)创建一个新用户。您应该能够创建一个新的电子表格并编辑它。刷新页面时，您会看到您所做的更改已保存。

## 结论

在下一部分，我们将把协作添加到我们的应用程序中。我们将看到如何让不同的用户使用 Pusher 实时编辑同一个文档。到目前为止，你可以在这里查看这个应用的源代码。

*这篇文章最初发表在[推手的博客](https://pusher.com/tutorials/realtime-spreadsheets-part-1/)上。*