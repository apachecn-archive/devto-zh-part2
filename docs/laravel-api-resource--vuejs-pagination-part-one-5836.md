# Laravel API 资源+ Vue.js 分页(第一部分)

> 原文：<https://dev.to/fwartner/laravel-api-resource--vuejs-pagination-part-one-5836>

Laravel 从 5.5 版本开始提供自己的 api-transformers，将您的雄辩模型转换成 JSON。当您使用 Laravel 构建 API 时，这在很多情况下会非常方便。

但是如何使用 Vue.js 在应用程序中消费和分页 api 的结果呢？在本教程中，我将向你展示如何管理结果的分页。

## 你将需要什么

我假设您的机器上安装了 Laravel 代客和 Laravel 安装程序。我还假设你知道在 Laravel 中使用雄辩资源的基本知识。
我假设您对 Bootstrap 4 有基本的了解，因为我们在这个例子中使用的是 BS4。

## 创建新的 Laravel 项目

打开你的终端，输入

```
laravel new pagination-example 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的机器上设置一个新的 laravel 5.6 应用程序。

现在，通过键入
将“cd”导入到您的应用程序中

```
cd pagination-example 
```

Enter fullscreen mode Exit fullscreen mode

## 准备一个演示模型

我们需要演示数据。我们需要大量的演示数据。Laravel 将帮助我们，使用模型工厂创建这些数据。

但是首先我们需要一个模型，一个迁移和一个我们想要分页的概念(橙子，苹果，汽车，等等！)..我将为这个例子选择简单的人，因为我像大多数人一样用数据创建了一些项目😆。

类型

```
php artisan make:model Person -m 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的`app/`目录中创建一个名为`Person`的新模型，并在您的`database/`目录中创建一个名为`2018_05_03_create_persons_table.php`的新迁移文件。

这是我的*人*模型的样子:

```
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreatePeopleTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('people', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email');
            $table->string('city');
            $table->string('country');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('people');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

类型

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

迁移您的数据库。

## 雄辩的资源

既然我们已经有了模型和迁移，我们还需要一个有说服力的资源。
您可以通过键入
轻松创建一个

```
php artisan make:resource PersonResource 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的`app/Http/Resources/`目录中创建一个名为`PersonResource`的新文件。

现在准备您的模型，允许使用我们的数据进行批量分配:

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Person extends Model
{
    protected $guarded = [];
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建模型工厂

创建一个模型工厂非常简单。你基本上只需要输入

```
php artisan make:factory PersonFactory --model=Person 
```

Enter fullscreen mode Exit fullscreen mode

输入你的终端，就这样。(没有..我只是在开玩笑😆)

这样编辑你的人工厂:

```
<?php

use Faker\Generator as Faker;

$factory->define(App\Person::class, function (Faker $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->safeEmail,
        'city' => $faker->city,
        'country' => $faker->country
    ];
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 伪造数据

我们仍然需要数据。要创建数据，只需键入

```
php artisan tinker 
```

Enter fullscreen mode Exit fullscreen mode

进入您的终端，然后按

```
$persons = factory('App\Person', 30)->create(); 
```

Enter fullscreen mode Exit fullscreen mode

一旦廷克装上子弹。在您按下 enter 键后，您将会看到我们资源的模拟数据数组。

## 回馈 JSON(收藏)

既然我们已经为测试应用程序准备好了几乎所有的东西，我们仍然需要提供数据..
雄辩——资源很聪明。
您可以将数组或对象传递给资源，它会自动将数据映射到 json-collection / json-object。

我们现在需要通过修改我们的`routes/web.php`文件来显示数据，如下所示:

```
<?php

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('/', function () {
    $persons = \App\Person::paginate(5);

    return \App\Http\Resources\PersonResource::collection($persons);
}); 
```

Enter fullscreen mode Exit fullscreen mode

当你在浏览器中打开`http://pagination-example.test`时，这会给我们一个数据库中所有人的集合。

## 返回 JSON(对象)

正如我之前告诉你的，资源也可以处理简单的对象..
为了只显示我们集合中的 ***一个*** ，修改`routes/web.php`文件如下:

```
<?php

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/
// Collection
Route::get('/', function () {
    $persons = \App\Person::paginate(5);

    return \App\Http\Resources\PersonResource::collection($persons);
});

// Object
Route::get('{id}', function ($id) {
    $person = \App\Person::find($id);

    return \App\Http\Resources\PersonResource::make($person);
}); 
```

Enter fullscreen mode Exit fullscreen mode

当你在浏览器中打开`http://pagination-example.test/1`时，这会给我们一个数据库中 ID 为 *1* 的人的简单对象。

## 结论

这是“Laravel API Resource + Vue.js 分页”两部分系列的第一部分 。
在下一部分，我将向您展示如何管理 vue——这是我们示例的一部分。

你可以在 [GitHub](https://github.com/fwartner/laravel-vue-pagination/tree/34171ca452c657670a20ed1dc5c4e4b124e88996) 上找到这部分的源代码。