# 消除隐藏的依赖性

> 原文：<https://dev.to/shalvah/eliminating-hidden-dependencies--116c>

在软件中，当应用程序中的一个模块 *A* 依赖于另一个模块或环境 *B* 时，就会产生依赖。当 *A* 以一种不明显的方式依赖于 *B* 时，就会发生*隐藏的*依赖。

要发现一个隐藏的依赖关系，通常需要深入研究模块的源代码。模块可以指任何东西，从整个服务到一个类或函数，再到几行代码。

这里有一个依赖关系的小例子，比较了两种表达方式:

```
let customer = Customer.find({id: 1});

// explicit -- the customer has to be passed to the cart
function Cart(customer) {
  this.customer = customer;
}
let cart = new Cart(customer);

// hidden -- the cart still needs a customer,
// but it doesn't say so outright
function Cart() {
  this.customer = customer; // a global variable `customer`
}
let cart = new Cart(); 
```

Enter fullscreen mode Exit fullscreen mode

注意到细微的差别了吗？Cart 构造函数的两种实现都依赖于一个客户对象。但是第一个要求您传入这个对象，而第二个要求环境中已经有一个可用的 customer 对象。一个 dev seeing `let cart = new Cart()`没有办法知道 cart 对象依赖于一个全局客户变量，除非他们看一下 Cart 构造函数。

## 隐藏在野外的依赖

我将分享一些我在现实世界代码库中遇到的隐藏依赖的例子。

### PHP `include`文件

我们拿一个典型的 PHP 后端 app 来说。在我们的`index.php`，我们应用程序的入口点，我们可以有这样的东西:

```
include "config.php";
include "loader.php";
$app = new Application($config); 
```

Enter fullscreen mode Exit fullscreen mode

代码看起来很可疑，不是吗？`$config`变量从何而来？让我们看看。

`include`指令类似于 HTML <脚本>标签；它告诉解释器获取指定文件的内容，执行它，如果它有返回语句，将返回值传递给调用者。这是一种将代码拆分到多个文件中的方法。像<脚本>标签一样，`include`也可以把变量放入全局范围。

让我们看看我们包括的文件。`config.php`文件包含后端应用的典型配置设置:

```
$config = [
    'database' => [
        'host' => '127.0.0.1',
        'port' => '3306',
        'name' => 'home',
    ],
    'redis' => [
        'host' => '127.0.0.1',
    ]
]; 
```

Enter fullscreen mode Exit fullscreen mode

`loader.php`基本上是一个自制的类加载器。下面是其内容的简化版本:

```
$loader = new Loader(__DIR__);
$loader->configure($config); 
```

Enter fullscreen mode Exit fullscreen mode

看到问题了吗？`loader.php`中的代码(以及`index.php`中的其余代码)依赖于某个名为`$config`的变量，但是在打开`config.php`之前并不清楚`$config`在哪里定义。这种编码模式其实并不少见。

### 通过<脚本>标签包含 JavaScript 文件

这大概是一个比较常见的例子。比较这两个代码片段(假设`cart-fx`和`cart-utils`是一些随机的 JS 库):

```
<script src="//some-cdn/cart-fx.js"></script> <script src="//some-cdn/cart-utils.js"></script> 
/* lots and lots of code */

<script>
var cart = new Cart(CartManager.default, new Customer());
</script> 
```

Enter fullscreen mode Exit fullscreen mode

```
import Cart from 'cart-fx';
import CartManager from 'cart-utils';

/* lots and lots of code */

const cart = new Cart(CartManager.default, new Customer()); 
```

Enter fullscreen mode Exit fullscreen mode

在第二个例子中，很明显`Cart`和`CartManager`变量是分别从`cart-fx`和`cart-utils`模块引入的。首先，我们只能猜测哪个模块拥有`Cart`，哪个模块拥有`CartManager`。别忘了还有`Customer`！(记住，我们自己的代码也是一个模块。)

### 从环境中阅读

我是这件事的罪魁祸首。前段时间写了一个 PHP 包和一个 API 交互。这个包允许您将 API 键传递给构造函数。不幸的是，它也允许您将您的键指定为环境变量，包会自动使用它们。[偷看一眼，别笑我](https://github.com/Hng-X/moneywave-php/blob/ef4d491c9a23f084d7a13e7279219213e8d4f87f/README.md#configuration)。

当我这样做的时候，我相信我让开发人员的生活变得更容易了。然而，实际上，我是在对代码运行的环境进行假设，并将包的功能与环境中满足的某些条件联系起来。

## 那么，为什么隐藏依赖是危险的呢？

两个原因:

*   **在没有移除依赖关系**的情况下，很容易无意中移除所依赖的模块。例如，以我上面的包为例。想象一个开发者建立了一个应用程序，在一个新的环境中使用这个包。在决定从旧环境中继承哪些环境变量时，开发人员可能会忽略添加包所需的变量，因为*他们在代码库*的任何地方都找不到它们。
*   对相关代码的小改动可能会破坏整个应用程序，或者引入错误。以我们上面的`index.php`文件为例——交换前两行看似无害的改变，但它会破坏应用程序，因为第 2 行依赖于第 1 行中设置的变量。(这实际上发生在我身上，并导致我写这篇文章。)

更严重的情况是这样的:

```
$config = [...];
include "bootstrap.php";
$app = new Application($config); 
```

Enter fullscreen mode Exit fullscreen mode

假设我们的`bootstrap.php`文件对`$config`做了一些重要的修改。如果出于某种原因，第二行被移动到底部，应用程序将运行而不会抛出任何错误，但`bootstrap.php`做出的关键配置更改将不会被应用程序看到。

## 摆脱隐藏的依赖

像许多软件工程一样，没有处理隐藏依赖的硬性规则。随着时间的推移，我发现了一些帮助我实现这一目标的指导原则:

1.  *编写真正模块化的代码*，而不仅仅是分散在多个文件中。一个理想的模块应该是独立的，并且对共享的全局状态有最小的依赖。一个模块也应该明确地声明它的依赖关系。
2.  *减少模块需要对其环境或其他模块做出的假设数量*。
3.  *露出一个清晰的界面*。理想情况下，除了函数/类签名之外，模块的用户不应该查看源代码来判断模块的依赖关系。
4.  *避免乱扔环境垃圾*。抵制向父作用域添加变量的诱惑。尽可能经常地，倾向于向调用者显式地返回或导出变量。

记住这些，让我们尝试重构第一个例子。首先，我们将让配置文件*返回配置数组*,这样调用者可以显式地将其设置为一个变量；

```
// config.php

return [
    'database' => [
        'host' => '127.0.0.1',
        'port' => '3306',
        'name' => 'home',
    ],
    'redis' => [
        'host' => '127.0.0.1',
    ]
]; 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们要做的是改变加载文件来返回一个函数。这个函数将接受一个配置参数。通过这种方式，我们明确了加载文件的过程依赖于一些预设的配置:

```
// loader.php

return function (array $config)
{
  $loader = new Loader(__DIR__);
  $loader->configure($config);
} 
```

Enter fullscreen mode Exit fullscreen mode

将这些放在一起，我们最终得到了这样的`index.php`文件:

```
$config = include 'config.php';
(include 'loader.php')($config);
$app = new Application($config); 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至可以更进一步:

```
$config = include 'config.php';

$loadClasses = include 'loader.php';
$loadClasses($config);

$app = new Application($config); 
```

Enter fullscreen mode Exit fullscreen mode

现在任何人看到`index.php`一眼就能看出:

1.  文件`config.php`返回**某个东西**(我们可以猜测这是某种配置，但现在这并不重要)
2.  加载器文件和`Application`都依赖于**和**来完成它们的工作

好多了，不是吗？

让我们来看看第二个例子。我们可以用几种方式重构它:切换到受支持的浏览器的`import` / `require`，或者使用提供聚合填充的构建工具。但是我们可以做一个小小的改变，让事情变得更好:

```
<script src="//some-cdn/cart-fx.js"></script> <script src="//some-cdn/cart-utils.js"></script> 
/* lots and lots of code */

<script>
var cart = new CartFx.Cart(CartUtils.CartManager.default, new Customer());
</script> 
```

Enter fullscreen mode Exit fullscreen mode

通过将`CartManager`和`Cart`对象附加到全局`CartFx`和`CartUtils`对象上，我们有效地将这两个对象移到了名称空间中，将潜在的隐藏依赖项的数量减少到每个模块只有一个。

## 有时候，你就是没办法

有时候，您可能会受到可用工具、有限资源等等的限制。但是，重要的是要记住，对于代码作者来说显而易见的事情，对于新手来说可能并不如此，并寻找一些可以改进这一点的优化。

有任何关于隐藏依赖或处理它们的技术的经验吗？请在评论中分享。