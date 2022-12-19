# 框架印象:Symfony 4 简介

> 原文：<https://dev.to/buphmin/framework-impressions-a-brief-look-into-symfony-4-4l70>

编辑:

我继续运行了一些性能测试，通过优化获得了更好的结果。

# 前言

这是我计划撰写的一系列文章中的第一篇，在这些文章中，我将着眼于各种服务器端 web 框架或在几种语言中不使用框架。

## 目标

我的主要目标是强调几个服务器端框架，并给出足够的信息，以便读者可以判断是否应该对您的用例做更多的调查。

以下是一些目标:

*   测试灵活性
    *   它妨碍开发吗？
*   SQL 兼容性
    *   你能使用原始 sql 吗
    *   如果有的话，ORM 对任何类型的查询都有用吗？
*   多连接 api 端点的快速响应时间性能
    *   在响应时间对于良好的 UX 来说太高之前，框架可以支持多少并发用户。

## 设置

我已经在 docker 中做了所有的事情，以便于将事情分开。对于负载测试，我将使用 [k6](https://k6.io/) 。

回购:[https://github.com/buphmin/symfony4-impressions](https://github.com/buphmin/symfony4-impressions)
负载测试器:

```
docker pull loadimpact/k6 
```

Enter fullscreen mode Exit fullscreen mode

作为性能部分的参考，下面是我的测试方法

### 硬件/硬件配置

我的硬件比大多数人都快，因此您的里程可能会因基准而异。我还在 linux mint 19 VM 中运行所有的东西。规范由虚拟机保留。

i7 8700k 以 4.9ghz 运行 6 个内核
8GB ram 以 3600mhz 运行

核心 0-1 为 web 服务器保留
核心 2-3 为数据库保留
核心 4-5 为 k6 保留

# 观感

因此，作为免责声明，我已经与 Symfony 合作多年，所以这很难说是我的第一印象。然而，我确实觉得我可以客观地描述 Symfony 的优势和劣势，这样你就可以确定它是否值得你关注。你应该总是自己判断一项技术，而不是仅仅因为(此处插入知名人士)这么说就盲目地认为它是好的。

## 什么是 Symfony

Symfony 是一个全功能的全栈框架，旨在为您提供一个 web 开发的一体化解决方案。Symfony 试图成为一个通用的框架，内置认证、服务器端渲染/模板、api 支持等。

## 灵活性

就灵活性而言，Symfony 非常灵活，尤其是对于一个功能齐全的框架。但这并不意味着 Symfony 是完美的。你必须接受 Symfony 需要的配置文件，并以 Symfony 的方式设置它们。此外，默认情况下，Symfony 希望你把你的文件放在特定的位置，迫使你

Symfony 允许你覆盖它的许多惯例，但你可能会发现麻烦。例如，您可以[覆盖](https://symfony.com/doc/current/configuration/override_dir_structure.html)目录结构，但是当文档认为您使用的是默认值时，您可能会感到困惑。

对于 SQL，Symfony 附带了用于 ORM 和 DBAL(数据库抽象层)的[原则](https://www.doctrine-project.org/index.html)。两者可以单独使用，也可以同时使用。教条 ORM 非常好，将允许你使用*大多数*数据库结构。一个显著的缺点是将复合外键关系作为主键，这要么无法实现，要么需要一些创造性来解决。原则映射可能需要一些时间来设置，但 Symfony 附带了一些[工具](https://symfony.com/doc/current/doctrine/reverse_engineering.html)，如果你已经构建了数据库，这个过程会更简单。另一方面，您可以对原始 SQL 使用 DBAL 原则。DBAL 只是 PHP 原生数据库连接库 PDO 的一个轻量级包装器，它提供了一些方便的方法。Symfony 也可以使用其他连接接口，比如连接到 MongoDB，但是 Symfony 的文档回避了这一点，我在这里就不介绍了。在 ORM 和 DBAL 之间，您可以根据需要使用 SQL 数据库。

下面是一些带有多重连接的原始 SQL 和 ORM 端点的例子:

```
 /**
     * @param integer $id
     * @param Connection $conn
     * @return JsonResponse
     * @Route(path="/league_players/raw/{id}", name="league_player_raw")
     */
    public function getLeaguePlayerRawAction($id, Connection $conn)
    {
        $sql = "
            select * from 
            league_player lp
            join league l on lp.league_id = l.id
            join player p on lp.player_id = p.id
            where lp.id = :id
        ";

        $leaguePlayers = $conn->fetchAll($sql, [
            'id' => $id
        ]);

        if (count($leaguePlayers) === 0) {
            throw new NotFoundHttpException('Player not found');
        }

        return new JsonResponse($leaguePlayers[0]);
    }

    /**
     * @param LeaguePlayer $leaguePlayer
     * @param SerializerInterface $serializer
     * @return Response
     * @Route(path="/league_players/{leaguePlayer}", name="league_player")
     */
    public function getLeaguePlayerAction(LeaguePlayer $leaguePlayer, SerializerInterface $serializer)
    {
        $data = $serializer->serialize($leaguePlayer, 'json');
        return new Response($data);
    } 
```

Enter fullscreen mode Exit fullscreen mode

在灵活性部分的最后，有趣的是，Symfony 框架是由各种独立的[可重用组件](https://symfony.com/components)构建而成。所有这些都可以在 Symfony 框架之外作为一个整体使用。例如，如果你只需要一个路由器工具，那么你可以使用 Symfony 路由组件，自己管理栈的其余部分。

## 表现

我将判断上面的两个端点，以判断原始 SQL 实现和 ORM 实现。ORM 实现略有不同，但它是您可以用 ORM 端点做的事情的现实实现。

### 1 个并发用户

Raw:

```
docker run --network host --cpuset-cpus "4-5" -v $PWD/loadTests:/src -i loadimpact/k6 run --vus 1 --duration 10s /src/getLeaguePlayerRaw.js 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
http_req_duration..........: avg=4.51ms  min=3.51ms  med=4.46ms  max=5.76ms   p(90)=5.09ms  p(95)=5.42ms 
```

Enter fullscreen mode Exit fullscreen mode

相当不错的表现，11.73ms 时长。显然，由于这是局部对局部的，这并不完全是“真实”的世界性能，但它给了我们一个粗略的概念。

蛇:T0

```
docker run --network host --cpuset-cpus "4-5" -v $PWD/loadTests:/src -i loadimpact/k6 run --vus 1 --duration 10s /src/getLeaguePlayer.js 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
http_req_duration..........: avg=16.74ms min=15.06ms med=15.98ms max=21.15ms  p(90)=19.43ms p(95)=20.29ms 
```

Enter fullscreen mode Exit fullscreen mode

在这个场景中，ORM 的实现几乎是请求时间的两倍。

### 10 个并发用户

将- vus 更改为 10

Raw:

```
 http_req_duration..........: avg=8.81ms  min=3.24ms med=8.35ms max=19.11ms  p(90)=12.5ms  p(95)=17.18ms 
```

Enter fullscreen mode Exit fullscreen mode

蛇:T0

```
http_req_duration..........: avg=51.44ms min=14.81ms med=51.05ms max=82.89ms  p(90)=76.4ms  p(95)=80.5ms 
```

Enter fullscreen mode Exit fullscreen mode

ORM 方法的额外负担变得很明显。

### 50 个并发用户

将- vus 更改为 50

Raw:

```
http_req_duration..........: avg=34.75ms  min=4.57ms med=31.57ms max=90.81ms  p(90)=58.4ms  p(95)=76.5ms 
```

Enter fullscreen mode Exit fullscreen mode

蛇:T0

```
http_req_duration..........: avg=201.94ms min=15ms    med=216.65ms max=1.69s    p(90)=334.99ms p(95)=369.24ms 
```

Enter fullscreen mode Exit fullscreen mode

编辑:

在我对 50 个并发用户的测试中，原始 SQL 的性能相当不错。我会注意到，我看到了一个非常广泛的运行之间的变化。大多数运行时间在 28-45 毫秒之间，但有些运行时间达到了 400 毫秒，这可能是我的测试环境的限制。利用实现的优化性能，我能够在 200 个并发用户时获得大约 130 毫秒的响应时间。这很好，但仍然落后于 node.js 中的任何内容，我们将在以后的文章中看到这一点；)

现在我们已经达到了双核所能达到的极限。在大多数情况下，500 毫秒对于良好的 UX 来说太长了。我们发现 ORM 比原始的 SQL 实现增加了额外的开销，这是意料之中的。

## 结果

我对 Symfony 的印象是，它非常灵活，给你提供了很多处理业务的方法。

赞成的意见

*   灵活性
*   好医生
*   基本上不会妨碍你

骗局

*   ORM 的一些限制
*   高并发用户性能并不好

推荐:
[![thumbs](../Images/c23b068e19a71ba124171c399fb9ff2e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--GDAfqgaQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/pix.iemoji.com/images/emoji/apple/ios-11/256/thumbs-up.png)

我想说 Symfony 对于大多数对框架感兴趣的人来说是值得一看的。它提供了良好的灵活性和相当好的低负载性能，这使它成为一个内部公司应用程序的理想选择。

如果你有任何见解或问题，请告诉我，因为这将是我的第一篇相当深入的文章。