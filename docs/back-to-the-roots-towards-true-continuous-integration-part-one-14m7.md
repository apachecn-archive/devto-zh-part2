# 追根溯源:走向真正的持续集成(第一部分)

> 原文：<https://dev.to/apium_hub/back-to-the-roots-towards-true-continuous-integration-part-one-14m7>

在本文中，我将向您展示许多人认为 CI 是什么，什么是真正的持续集成，什么不是 CI。还有，我会举一些例子让你更好的理解。

## 什么是 CI？

CI(持续集成的首字母缩写)是一种软件开发实践，在这种实践中，持续集成服务器轮询版本控制存储库，构建一个工件，并用一组定义的测试来验证工件。对于大多数企业和个人来说，这是一种常见的做法…这不是真正的持续集成定义，抱歉开了这个玩笑。

## 什么是真正的持续集成？

好吧，真正的持续集成不是“简单地”某种“Jenkins | Travis | Go | Teamcity ”,它轮询项目的 git 库，编译它并对工件运行一系列测试。事实上，这是 CI 中不太有趣的部分，它不是一种技术(Jenkins ),而是 Grady Booch 创建的敏捷实践，并被极端编程方法学采用和规定。

作为另一种极限编程技术[的类比，TDD](https://apiumhub.com/tech-blog-barcelona/advantages-of-test-driven-development/) 不是关于[单元测试](https://apiumhub.com/tech-blog-barcelona/top-benefits-of-unit-testing/)(尽管它使用单元测试)，而是关于反馈，关于尽快获得反馈以加速开发周期(在单元测试的具体使用中实现)。

使用 CI，软件一天要构建几次(理想的是每隔几个小时)，每次开发人员在主线中集成代码(这应该是经常的)，以避免“集成地狱”(在开发交互结束时合并来自不同开发的代码)。CI 通过尽可能快地集成代码，并迫使团队成员查看其他开发人员正在做什么，以对新代码做出共享的团队决策，来避免这种“集成地狱”。

该方法声明每个团队成员尽可能经常地集成到主线中。对 VCS(版本控制系统)的每一个贡献都可能是一个发布，所以每一个贡献都不应该破坏功能，并且应该通过所有已知的测试。

CI 服务器将从主线的最后来源构建一个工件，并通过所有已知的测试。如果有一个失败，CI 将警告团队的所有成员构建的状态(红色)。

团队的最高优先级是保持构建的默认值(绿色)。

## 什么不是 CI？

一旦我们认识到 CI 远不止是 CI 服务器的简单使用，我们就可以说:

*   使用特征分支并拥有配置项检查主项不是配置项
*   处理拉取请求不是 CI

重要的是要注意，我不是根据好/坏的实践来判断，分支和拉请求都是不同于 CI 的方法。

功能分支和拉请求都依赖于在不同于主分支(由 CI 服务器监控的分支)上工作，这导致它们在合并到主分支之前需要更长的周期。

突出严重依赖团队资源/任务规划的分支和拉请求，以避免一个任务(分支)上的重构影响另一个任务(分支)上的开发，从而缩小线程化的“集成地狱”。

集成地狱的例子:我们有下面的代码，两个利用 API rest 调用外部 API 的类:

```
￼APIUsersAccessor
class APIUsersAccessor
{
    const USERS_API_PATH = "/users";
 /**
     * @var string
     */
    private $host;
    /**
     * @var string
     */
    private $username;
    /**
     * @var string
     */
    private $password;
    public function __construct(string $host, string $username, string
$password)
    {
        $this->host = $host;
        $this->username = $username;
        $this->password = $password;
}
    public function getAllUsers(): array
    {
        $data = array(
            "email" => $this->username,
            "password" => $this->password
        );
        $headers = array(
            "Content-Type" => "application/json;charset=UTF-8"
        );
        $request = \Requests::GET($this->host.self::USERS_API_PATH,
$headers, json_encode($data));
        return json_decode($request->body);
    }
}
￼
APIProductsAccessor
class APIProductsAccessor
{
    const PRODUCTS_API_PATH = "/products";
    /**
* @var string
     */
    private $host;
    /**
     * @var string
     */
    private $username;
    /**
     * @var string
     */
    private $password;
    public function __construct(string $host, string $username, string
$password)
    {
        $this->host = $host;
        $this->username = $username;
        $this->password = $password;
}
    public function getAllProducts(): array
    {
        $data = array(
            "email" => $this->username,
            "password" => $this->password
        );
        $headers = array(
            "Content-Type" => "application/json;charset=UTF-8"
        );
        $request = \Requests::GET($this->host.self::PRODUCTS_API_PATH,
$headers, json_encode($data));
        return json_decode($request->body);
    }
} 
```

如你所见，这两个代码非常相似(是经典的代码重复)。现在我们将开始两个开发特性，包含两个开发分支。第一次开发必须将电话号码添加到对产品 API 的请求中，第二次开发必须创建一个新的 API 来查询商店中所有可用的汽车。这是添加电话号码后产品 API 中的代码:

￼

```
￼APIUsersAccessor (with telephone)
class APIUsersAccessor
{
....
    public function __construct(string $host, string $username, string
$password)
{
.......
  $this->telephone = $telephone;
    }
    public function getAllUsers(): array
    {
        $data = array(
            "email" => $this->username,
            "password" => $this->password,
   "tel" => $this->telephone
        );
..... }
} 
```

好了，开发人员已经添加了缺少的字段，并将其添加到请求中。分支 1 的开发人员认为这种差异是与主分支合并的结果:

[![true continuous integration](../Images/f86b00faa604368f3cec11ca1ecfd4d1.png)T2】](https://apiumhub.com/wp-content/uploads/2018/04/Screen-Shot-2018-04-06-at-11.07.36-1.png)

但问题是 developer1 并不知道 developer 2 为了减少代码重复做了一次重构，因为 CarAPI 和 UserAPI、ProductAPI 太像了，所以他分支里的代码会是这样的:

```
￼￼BaseAPIAccessor
abstract class BaseAPIAccessor
{
    private $apiPath;
    /**
* @var string
     */
    private $host;
    /**
     * @var string
     */
    private $username;
    /**
     * @var string
     */
    private $password;
    protected function __construct(string $host,string $apiPath, string
$username, string $password)
    {
        $this->host = $host;
        $this->username = $username;
        $this->password = $password;
        $this->apiPath = $apiPath;
}
    protected function doGetRequest(): array
    {
        $data = array(
            "email" => $this->username,
            "password" => $this->password
        );
        $headers = array(
            "Content-Type" => "application/json;charset=UTF-8"
        );
        $request = \Requests::GET($this->host.$this->apiPath, $headers,
json_encode($data));
        return json_decode($request->body);
    }
}
￼concrete APIs
class ApiCarsAccessor extends BaseAPIAccessor
{
    public function __construct(string $host, string $username, string
$password)
    {
        parent::__construct($host, "/cars", $username, $password);
}
    public function getAllUsers(): array
    {
        return $this->doGetRequest();
    }
}
class APIUserAccessor extends BaseAPIAccessor
{
    public function __construct(string $host, string $username, string
$password)
    {
        parent::__construct($host, "/users", $username, $password);
}
    public function getAllUsers(): array
    {
        return $this->doGetRequest();
    }
}
class APIProductsAccessor extends BaseAPIAccessor
{
    public function __construct(string $host, string $username, string
$password)
    {
        parent::__construct($host, "/products", $username, $password);
}
    public function getAllProducts(): array
    {
        return $this->doGetRequest();
    }
} 
```

所以真正的合并将是:

[![true continuous integration](../Images/04c928104a0fe8712f1118d1dd647c39.png)](https://apiumhub.com/wp-content/uploads/2018/04/Screen-Shot-2018-04-06-at-11.10.57.png) 所以基本上我们会在开发周期结束时将 branch1 和 branch2 合并到主线中，这会有一个很大的冲突。我们将不得不做大量的代码审查，这将涉及一个考古过程，审查开发阶段所有过去的决定，看看如何合并代码。在这种具体情况下，电话号码也将涉及某种重写。

有些人会认为 developer2 不应该进行 refactor，因为 planning 声明他只需要开发 CarApi，并且 planning 明确声明不应该与 UserAPI 冲突。是的…但是为了使这种极端的计划工作，应该有一个所有资源的良好计划，我们应该有很多包括 developer1 和 developer2 的架构会议。

在这个架构会议中，开发人员 1 和开发人员 2 应该已经意识到存在某种代码重复，他们必须决定干预并重新规划，或者什么都不做并增加技术债务，将重构决策转移到未来的迭代中。这听起来可能不太敏捷，对吗？但关键是很难混合敏捷和非敏捷实践。

如果我们有分支/拉取请求，那么完整的迭代规划过程会更好，如果我们正在进行敏捷持续集成，那么它就是正确的工具。同样，我不是说特性分支/拉请求是好/坏的工具，我只是说它们是非敏捷的实践。

敏捷是关于沟通的，是关于持续改进的，是关于尽快反馈的。在敏捷方法中，developer1 在开始时会意识到 developer2 的重构，能够与 developer1 开始对话，并检查它提出的抽象类型是否正确，是否也适合添加电话号码。

好的……但是 **等等！我需要一个功能分支！如果在迭代结束时不是所有的特性都是可交付的，那该怎么办？**

特性分支是一个问题的解决方案:如果在迭代结束时不是所有的代码都是可交付的，该怎么办，但这不是唯一的解决方案。

CI 对此问题有另一个解决方案——“特性切换”。功能分支通过一个分支将进行中的功能从最终产品中分离出来(w.i.p .存在于代码的一个单独的副本中)，功能切换使用..代码！

一个人能写的最简单的特性切换是可怕的 if-then-else，这是当你在谷歌上搜索“特性切换”时你会在大多数网站上找到的例子。这不是实现的唯一方法，因为任何其他类型的软件工程，你都可以用多态来代替这种条件逻辑。

在 Slim 的这个例子中，我们在当前的迭代中创建了一个新的 REST 端点，我们不想为生产做好准备，我们有这样的代码:

￼

```
￼￼code prior the toggling
<?php
require '../vendor/autoload.php';
use resources\OriginalEndpoint
$config = [
    'settings' => [
        'displayErrorDetails' => true,
        'logger' => [
            'name' => "dexeus",
            'level' => Monolog\Logger::DEBUG,
            'path' => 'php://stderr',
], ],
];
$app = new \Slim\App(
$config );
$c = $app->getContainer();
$c['logger'] = function ($c) {
    $settings = $c->get('settings');
    $logger = LoggerFactory::getInstance($settings['logger']['name'],
$settings['logger']['level']);
    $logger->pushHandler(new
Monolog\Handler\StreamHandler($settings['logger']['path'],
$settings['logger']['level']));
    return $logger;
};
$app->group("", function () use ($app){
 OriginalEndpoint::get()->add($app); //we are registering the endpoint
in slim }); 
```

我们可以用一个简单的 if 子句定义特性切换

￼

```
￼￼￼if clause feature toggle
<?php ....
$app->group("", function () use ($app){
 OriginalEndpoint::get()->add($app);
    if(getenv("APP_ENV") === "development") {
        NewEndpoint::get()->add($app); // we are registering the new
endpoint if the environment is set to development (devs machines should
have APP_ENV envar setted to development)
} }); 
```

我们可以精炼我们的代码来更好地表达我们正在做的事情，并且能够有几种环境(也许是为了测试 AB 情况？)

￼￼

```
￼￼configuration map feature toggle
<?php
......
$productionEnvironment = function ($app){
    OriginalEndpoint::get()->add($app);
};
$aEnvironment = function ($app){
    productionEnvironment($app);
    NewEndpointA::get()->add($app);
};
$bEnvironment = function ($app){
    productionEnvironment($app);
    NewEndpointB::get()->add($app);
};
$develEnvironment = function ($app){
    productionEnvironment($app);
    NewEndpointInEarlyDevelopment::get()->add($app);
};
$configurationMap = [
    "production" => $productionEnvironment,
    "testA" => $aEnvironment,
    "testB" => $bEnvironment,
    "development" => $develEnvironment
];
$app->group("", function () use ($app, $configurationMap){
    $configurationMap[getenv("APP_ENV")]($app);
}); 
```

这种技术的优势与 CI 的主要目标是一致的(具有关于代码集成/验证/以及与其他开发的冲突的持续反馈)，正在进行的代码被开发并部署到产品中，我们具有关于新功能与代码其余部分的集成的持续反馈，利用了开发时启用该功能的风险。

一旦新特性稳定下来，为了避免增加代码库的复杂性，从代码中移除这种切换是一个很好的做法。

好了，我们已经到达了真正持续集成的第一部分的末尾。我们重新发现，持续集成“不仅仅”使用 CI 服务器，而是采用一种坚持不懈和自律的实践。在第二部分，我们将讨论如何建立一个好的竞争情报流程。

为了不错过真正持续集成的第二部分，请在此订阅我们的每月简讯[。](http://eepurl.com/cC96MY)

## 如果你觉得这篇关于真正持续集成的文章很有趣，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

[真正持续集成的优势](https://apiumhub.com/tech-blog-barcelona/benefits-of-continuous-integration/)

[超过功能数量的工作软件](https://dev.to/apium_hub/working-software-go-live-strategy)

[TDD 的优势](https://apiumhub.com/tech-blog-barcelona/advantages-of-test-driven-development/)

[詹金斯的优势](https://dev.to/apium_hub/advantages-of-jenkins-12)

[在 iOS 中与 fastlane & Jenkins 实现真正的持续集成](https://dev.to/apium_hub/ios-continuous-integration-with-fastlane--jenkins-19n)

帖子[追根溯源:走向真正的持续集成(上)](https://apiumhub.com/tech-blog-barcelona/true-continuous-integration/)最早出现在 [Apiumhub](https://apiumhub.com) 上。