# 函数式 PHP:第一种方法

> 原文：<https://dev.to/apium_hub/functional-php-a-first-approach-43a3>

函数式 PHP？PHP 不是函数式语言，但是一些函数式技术可以用来改进我们的代码:更好的可读性，更容易维护= >更便宜的代码。

许多年来，PHP 都是以过程化的方式编写脚本的，所有的脚本都在一个文件中，函数无处不在。

版本 5 之后。*，应用程序是使用[面向对象范例](https://apiumhub.com/tech-blog-barcelona/single-responsibility-principle/) (OO)编写的。但是我们很少想到函数式范例中的 PHP。当然，PHP 不是函数式语言，但是我们应该能够利用每种范式的优点。不是关于 OO 反对函数式编程(FP)，或者定义哪个更好。

由于 PHP 是一个非常“经典”的面向对象环境，我们建议使用“融合”方法。我们的方法朝着我们在多种语言中使用的方向发展，例如 [Scala](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38) 、 [Kotlin](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639) ，还有 Typescript:我们称之为“对象函数”的语言。

我们仍然创建类和对象，但是使用函数式思维。与其告诉计算机如何解决问题(又名命令式编程)，不如让我们开始告诉计算机我们想要实现什么(声明式编程)。

## 函数编程&函数 PHP

简而言之，函数编程:

*   **避免状态突变**对象一旦创建，就永远不会改变。没有我们在 PHP 中使用的变量。
*   **使用复杂类型系统**类型是值的分类器。它给出了运行时该值的信息，但它是在编译时指出的。例如，它们生成更详细的函数(正如您可以看到函数期望什么，输出将是什么)，但是作为回报，它们可以在编译时检测错误，早在您的 IDE 中编码时就可以检测到。PHP 有自己的“编译时间”，通常标记为“解析时间”，但它实际上是类似的，如果不是同义词的话，特别是当使用 OpCache 这样的工具来缓存解析结果时。值得一提的是，PHP 当前版本中的类型比它们在函数式语言中的对应物要“弱”一些，甚至与其他一些“经典”OO 语言相比也是如此。
*   **函数作为一级值**函数可以作为其他允许函数合成的函数的输入或输出。它们不是我们所知道的 PHP 中的函数。它们就像数学函数:相同的输入总是产生相同的输出(不管它被调用多少次)。它们被称为纯函数。

### 纯函数 vs 不纯函数

*   **纯函数**

    *   没有全局。值不是通过引用传递的
    *   不要有副作用(这是一个非常有趣的属性！！)
    *   不要使用任何类型的循环(for，for each，while…)
*   **不纯函数**

    *   变异全局状态
    *   可以修改它们的输入参数
    *   可能会引发异常
    *   可能执行任何 I/O 操作:他们需要与外部资源(如数据库、网络、文件系统等)通信
    *   即使输入参数相同，也可能产生不同的结果。
    *   可能有副作用

## 要有功能我的朋友

让我们来看一些函数式 PHP 的实际例子:

**避免临时变量**

在我们的代码中没有临时变量，我们避免了产生不需要的结果的局部状态。

在下一个例子中，我们保持状态，直到最后返回。

```
function isPositive(int $number) {
    if ($number > 0) {
        $status = true;
    } else {
        $status = false;
    }
    return $status;
} 
```

我们可以删除直接返回状态的临时变量:

```
unction isPositive(int $number) {
    if ($number > 0) {
        return true;
    }
    return false;
} 
```

最后一次重构:移除 if。最后，这是以功能性的方式完成的:

```
function isPositive(int $number) {
    return $number > 0;
} 
```

**小功能**

*   他们应该遵循单一的责任:只做一件事
*   这意味着它们更容易测试
*   它们可以被组合

```
function sum(int $number1, int $number2) {
    return $number1 + $number2;
}

echo sum(sum(3, 4), sum(5, 5));
// 17 
```

请注意，我们可以将 sum(3，4)改为 7，结果将是相同的:

```
echo sum(7, sum(5, 5));
// 17 
```

这就是所谓的参照透明性:一个函数可以被它的结果代替，而最终的结果根本不会改变。

**解除状态**

当我们习惯于用命令的方式写作时，这可能是相当困难的。在本例中，它计算数组中所有值的乘积。为此，我们使用一些聚合器和一个循环来迭代它。

```
unction productImperative(array $data) {
    if (empty($data)) {
        return 0;
    }
    $total = 1;
    $i = 0;
    while ($i < count($data)) {
        $total *= $data[$i];
        $i++;
    }
    return $total;
} 
```

在这种情况下，当有一些重复的动作时，可以用递归来删除状态:

```
function product(array $data) {
    if (empty($data)) {
        return 0;
    }
    if (count($data) == 1) {
        return $data[0];
    }
    return array_pop($data) * product($data);
} 
```

当然，这个例子非常简单，最好通过 reduce 来解决:

```
echo array_reduce([5, 3, 2], function($total, $item) {
   return $total * $item;
}, 1);
// 30 
```

**将不纯函数推到边界**

我们的 PHP 应用程序经常需要来自外部资源的一些输入并产生一些输出。

也就是说可能很难有纯函数。在这些情况下，我们应该将我们不纯的代码从

纯代码中分离出来。

例如，用 php.net 的代码讲述从一个文件中读取:

将文件放入数组。在本例中，我们将通过 HTTP 来获取

```
the HTML source of a URL.
$lines = file('https://www.google.com/');
// Loop through our array, show HTML source as HTML source
foreach ($lines as $line_num => $line) {
    echo htmlspecialchars($line) . "
\n";
} 
```

它从外部站点读取内容(外部输入)并将结果显示给控制台(外部输出)。我们能在这里做一些功能上的改变吗？当然，让我们把代码分成不同的函数。

```
function getFileContent($file) {
    return file($file);
}

function formatLines($lines) {
    return array_map(function($line) {
        return htmlspecialchars($line) . "\n";
    }, $lines);
}
print_r(formatLines(getFileContent('https://www.google.com/'))); 
```

结果是一样的，但现在我们有了:

*   一个不纯的函数(getFileContent)在我们的测试中很容易被模仿
*   一个纯函数(formatLines ),它总是返回相同的输出，很容易进行单元测试

**不要使用循环来迭代数组**

循环是命令式的，使用了一些临时变量，而且可读性不是很好。

**Map**

我们需要迭代一个数组来修改它的每一个内容。

示例:我们从数据库接收用户列表，并需要返回我们的

应用程序期望的模型。

以命令的方式我们会这样做:使用 foreach 告诉计算机做什么:

```
function getUsers() {
    return [
        ["firstname" => "john", "surname1" => "doe", "location" => "Barcelona", "numpets" => 2],
        ["firstname" => "david", "surname1" => "ee", "location" => "Girona", "numpets" => 10],
        ["firstname" => "jane", "surname1" => "qwerty", "location" => "Barcelona", "numpets" => 1],
    ];
}

function findUsers()
{
    $users = getUsers();
    if (empty($users)) {
        return false;
    }
    $usersDTO = [];
    foreach ($users as $user) {
        $usersDTO[] = new UserDTO($user);
    }
    return $usersDTO;
} 
```

更实用的风格是:

```
function findUsersMap()
{
    return array_map("convertUser", getUsers());
}

function convertUser(array $user) {
    return new UserDTO($user);
} 
```

我们使用 array_map 代替 foreach 并创建一个纯函数，它的唯一目的是转换用户的格式。这个版本的可读性比上一个版本好得多。

**Filter**

对数组进行迭代，但只返回那些通过某些条件的结果。

现在我们有了用户列表，我们想只显示那些居住在巴塞罗那的用户。

再次，我们需要遍历数组，逐个检查他们的城市。

```
function getUsersFromBcn(array $users) {
    $bcnUsers = [];
    foreach ($users as $user) {
        if ($user->getCity() == "Barcelona") {
            $bcnUsers[] = $user;
        }
    }
    return $bcnUsers;
} 
```

或者我们可以只询问巴塞罗那的用户:

```
function getUsersFromBcn(array $users) {
    return array_filter($users, "isFromBcn");
}

function isFromBcn(UserDTO $user) {
    return $user->getCity() == "Barcelona";
} 
```

这段代码更简单，更容易测试，没有临时变量，也没有 foreach + if 循环。

**缩小/折叠**

缩小一个数组返回值。

现在，我们想计算一下巴塞罗那市宠物的平均数量。

我们来迭代一下巴萨的用户，计算一下宠物的数量:

```
function getAvgPets(array $users) {
    $numPets = 0;
    foreach ($users as $user) {
        $numPets += $user->getPets();
    }
    return $numPets / count($users);
} 
```

或者我们可以合计宠物的数量:

```
function getAvgPets(array $users) {
    return array_reduce($users, "getTotalPets", 0) / count($users);
}

function getTotalPets($total, UserDTO $user) {
    return $total + $user->getPets();
} 
```

**使用管道**

让我们把所有的条件全部组合在一起:

```
echo getAvgPetsReduce(getUsersFromBcn(findUsers())); 
```

如果我们有多个条件，我们可能会以一个很长的句子结束，这个句子可能很难阅读。不幸的是，要解决这个问题，PHP 中没有一种本地方法。

幸运的是，有一些好的库可以使用管道。

**Laravel 集合**

Laravel 框架有一个很棒的[库](https://github.com/tightenco/collect)来处理数组，我们称之为集合。

可在 laravel 外使用，可通过 composer 安装

```
composer require tightenco/collect 
```

使用这个库，对象是不可变的，代码是可读的，更具声明性。

以巴塞罗那的平均宠物数量为例:

```
$collection = collect(getUsers());
echo $collection->map("convertUser")
                ->filter('isFromBcn')
                ->map("getListPets")
                ->average(); 
```

动作的顺序非常清楚

1.  转换用户
2.  只过滤来自巴塞罗那的
3.  获取每个用户的宠物列表
4.  获取平均值(使 reduce +计算平均值的库的方法)

## 结论:函数式 PHP

我们知道，PHP 并不是 100%实用的。以函数的方式改变我们的编程方式不是一件容易的事情。但是我们可以开始应用一些简单的方法，使我们的代码更简单，

可读性更好，可测试性更强，副作用更少。我们可以开始以一种更加
声明式的方式思考，一步一步我们会变得更加实用。我们将在接下来的文章中继续讨论函数式 PHP，让我们的 PHP 变得越来越函数化。

如果你有兴趣收到更多关于函数式 PHP 的文章，不要忘记订阅我们的每月简讯[这里](http://eepurl.com/cC96MY)。

## 如果你觉得这篇关于函数式 PHP 的文章很有趣，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

帖子[Functional PHP:a first approach](https://apiumhub.com/tech-blog-barcelona/functional-php/)首先出现在 [Apiumhub](https://apiumhub.com) 上。