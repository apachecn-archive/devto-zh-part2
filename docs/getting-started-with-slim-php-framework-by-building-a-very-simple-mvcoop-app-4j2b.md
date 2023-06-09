# 通过构建一个非常简单的 MVC/OOP 应用程序开始使用 Slim PHP 框架

> 原文：<https://dev.to/charliedevelops/getting-started-with-slim-php-framework-by-building-a-very-simple-mvcoop-app-4j2b>

*目的:*
我们将构建一个小巧的应用程序，创建一个农场围栏，并输出围栏内奶牛&鸡发出的声音。

*需求:*
-一只笔只能包含一头牛和一只鸡(将帮助我们练习与瘦依存注射容器)
-必须实现一个 MVC 方法(帮助我们了解模型，视图和控制器如何一起工作)

**1。**所以我们需要 slim skeleton 应用程序——打开命令行并运行:

`composer create-project slim/slim-skeleton slimFarm`

*   如果您没有全局安装 composer，请使用 composer 文档来获取它

**2。**在命令行上，导航(cd)到为您创建的 slimFarm 目录

**3。**我们将运行一个 php 服务器，这样我们就可以看到 slim 应用程序存在并工作

*   在您的命令行运行:`php -S 0.0.0.0:8080 -t ./public/`

**4。**进入 [http://0.0.0.0:8080/](http://0.0.0.0:8080/) 检查其工作情况(你应该会得到 slim 默认页面)

**5。**在文本编辑器中打开文件，在 src 中创建一个 Classes 文件夹

**6。**将您的名称空间添加到 composer.json 中的自动加载:

```
"autoload":  {  "psr-4":  {  "Farm\\":  "src/classes/"  }  }, 
```

Enter fullscreen mode Exit fullscreen mode

*   注意:这是对已经存在的 autoload-dev 的补充

*   这意味着我们可以使用名称空间来引用我们的类，而不是到处都有大量的 require。

**7。**在命令行中打开一个新标签，然后在命令行中运行`composer dump-autoload`

*   这将重新生成我们的自动加载和名称空间，以包含我们刚刚在步骤 6 中所做的更改。

**8。**我们需要我们的牛和鸡进入围栏！所以把我们的类放在类/模型文件夹中:

```
namespace Farm\Models;

class ChickenModel
{
    public $speak = 'cluck';
} 
```

Enter fullscreen mode Exit fullscreen mode

```
namespace Farm\Models;

class CowModel
{
    public $speak = 'moo';
} 
```

Enter fullscreen mode Exit fullscreen mode

**9。**在 Classes/Models 文件夹中创建一个 Pen 类:
命名空间 Farm \ Models

```
class PenModel
{
    public $cow;
    public $chicken;

    public function __construct($cow, $chicken)
    {
        $this->cow = $cow;
        $this->chicken = $chicken;
    }

    public function getCowNoise(){
        return $this->cow->speak;
    }

    public function getChickenNoise(){
        return $this->chicken->speak;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

10。让我们创建一个工厂，这样只需一次调用就可以快速创建 Pen 及其依赖项。

*   创建一个工厂文件夹(在类文件夹中)
*   制作 PenModelFactory:

```
namespace Farm\Factories;

class PenModelFactory
{
    function __invoke()
    {
        $cow = new \Farm\Models\CowModel();
        $chicken = new \Farm\Models\ChickenModel();
        return new \Farm\Models\PenModel($cow, $chicken);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   我们的工厂不需要构造函数，逻辑放在 __invoke magic 方法中，因为它是从我们的依赖注入容器(DIC)中调用的

**11。**现在我们已经创建了工厂，让我们将它放入我们的 DIC 中

*   可以把 DIC 想象成一个大的关联数组，它知道如何为你实例化我们放入其中的对象，以便在我们的应用程序中使用
*   打开 src/dependencies . PHP
*   DIC 中已经有预先存在的东西
*   像这样添加我们的工厂:

```
$container['penModel'] = new \Farm\Factories\PenModelFactory(); 
```

Enter fullscreen mode Exit fullscreen mode

*   我们说“在我们的容器(DIC)中添加一个新的‘pen model’键，当我们请求这个‘pen model’时，运行我们的工厂来实例化并返回一个准备好的 pen 对象，所有的依赖项(cow + chicken)都已处理好。”

**12。**让我们看看我们的路由文件，看看我们如何处理对我们的应用程序的不同 HTTP 请求。

*   就像这里已经写好的默认值一样，我们可以使用快速匿名函数来节省创建工厂的时间，但是我们要“正确地做”，所以我们将创建一个路由，并使用控制器来调用我们的业务逻辑。
*   删除现有的$app->get(.....)文件中已有的路线(我们将创建自己的路线)
*   设置我们的路线:

```
$app->get('/makeMeAPen', <where our callback will go> ); 
```

Enter fullscreen mode Exit fullscreen mode

*   /makeMeAPen 是人们希望访问以运行我们的代码的 URL 扩展
*   但是我们还没有控制器，所以....

13。让我们制作一个控制器来调用我们的 PenFactory 并呈现一个视图:

*   在 src/Classes 中创建一个控制器目录
*   在 src/Classes/Controllers/中创建 PenController

```
namespace Farm\Controllers;

class PenController
{
    protected $container;

    //this constructor passes the DIC in so we can get our PenFactory out of it later
    function __construct($container)
    {
        $this->container = $container;
    }

    function __invoke($request, $response, $args)
    {
        //create our pen from Penfactory in DIC
        $pen = $this->container->get('penModel');

        //assign args (variables that will be available on rendered view)
        $args['cowNoise'] = $pen->getCowNoise();
        $args['chickenNoise'] = $pen->getChickenNoise();

        //get the default template renderer out of DIC and pass the response and $args to a template file
        return $this->container->get('renderer')->render($response, 'showFarm.phtml', $args);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**14。**现在我们的控制器存在了，让我们把它添加进来，作为我们的路线被点击时发生的事情:

```
$app->get('/makeMeAPen', \Farm\Controllers\PenController::class); 
```

Enter fullscreen mode Exit fullscreen mode

15。请注意，在我们的 PenController 中，我们正在调用一个尚不存在的模板文件，因此让我们创建它:

*   在/templates 文件夹中创建 showFarm.phtml
*   使用放入$args 关联数组的值作为普通变量:

```
<!DOCTYPE html>
<html>
    <head>
        slimFarm
        <style>
            h2 {
                font-size: 10rem;
                color: red;
            }
        </style>
    </head>
    <body>
            <h2>Cows go: <?php echo htmlspecialchars($cowNoise); ?>!</h2>
            <h2>Chickens go: <?php echo htmlspecialchars($chickenNoise); ?>!</h2>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

16。导航到[http://0 . 0 . 0 . 0:8080/make meapen](http://0.0.0.0:8080/makeMeAPen)看看你的超级高级应用的光彩吧！