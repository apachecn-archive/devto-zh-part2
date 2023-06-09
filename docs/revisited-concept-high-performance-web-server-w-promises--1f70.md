# 重新审视的概念:高性能 web 服务器与承诺

> 原文：<https://dev.to/theopensourceu/revisited-concept-high-performance-web-server-w-promises--1f70>

[![Revisited Concept: High Performance web server w/ Promises](img/3546610a802112a2e01bf048eb9d8b96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T1RvBYgm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1520764816423-52375cbff016%3Fixlib%3Drb-0.3.5%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ%26s%3De533e5b4614cdb82fe0651d8ae79a0b0)

[这是我之前文章](https://theopensourceu.org/revisited-concept-high-performance-web-server-promises/)[的后续](https://theopensourceu.org/concept-high-performance-web-server-w-promises/):概念:高性能 web 服务器 w/ Promises 。

我决定跟进这件事有两个主要原因。首先，我发布的原始代码中有一个错误，我不记得这个错误是存在于我实际测试的代码中，还是在我撰写本文的过程中引入的。第二，我没有任何详细的数据可以分享，我觉得这与这类文章有关。

## 概念

*TL；DR* 的概念是:我们是否可以通过使用一个承诺来获得更高的 HTTP 服务器吞吐量(更高的 RPS ),即使在不需要的时候。也就是说，不存在异步操作。

更多详情请见[第一篇](https://theopensourceu.org/concept-high-performance-web-server-w-promises/)。

## 方法论

我重新创建了我的项目，但这次我将它保存为[开源](https://github.com/TheOpenSourceU/tOSU-PromiseWebServer-Experiment)。这是一个基本的 [WebStorm](https://www.jetbrains.com/webstorm/) express 模板，使用[手柄](https://handlebarsjs.com/)作为视图管理器。我对它进行了修改，以介绍我的概念，它看起来像:

```
const myData = { title: 'Promise Express' };

/* GET /promise/ */
router.get('/', function(req, res) {
    new Promise(function(resolve, reject) {
        resolve(myData); //setup data
    }).then(function(d) {
        res.render('index', d); //render the view in the 'then'
    });
}); 
```

我用[大炮](https://artillery.io/docs/getting-started/)进行负载测试。cannon 是在我的台式电脑上运行的(不是在运行 express 项目的服务器上)。使用[大炮](https://artillery.io/docs/getting-started/)，我们创造了以下场景:

*   10 个用户每人发出 20 个 GET 请求
*   50 个用户，每个用户发出 30 个 GET 请求
*   100 个用户，每个用户发出 30 个 GET 请求
*   500 个用户，每个用户发出 100 个 GET 请求

确切的服务器详情是:[Ubuntu 16.04 LTS](https://theopensourceu.org/tag/linux/)[digital ocean Droplets](https://m.do.co/c/0595daf97871)，Flexible Droplets-2gb 2x vcpu(15 美元一个月)。

注意:我还在 1 GB 1x vCPU 液滴上运行了一次，记录在最底部。

## 差异汇总

除了内容的深度，我最初的文章和这个版本有一些技术上的不同。(**格式** : *第一篇* vs *这篇*)

*   无极库:[小手指](https://github.com/floatdrop/pinkie-promise) vs [蓝鸟](http://bluebirdjs.com/docs/getting-started.html)
*   服务器:1 个 CPU、1 GB VPS 与 2 个 CPU、2 GiB VPS
    *   我确实查过了。配置，但没有得到错误。
*   车把:无视图模板引擎 vs 车把

## 结果

结果和我第一篇文章不一样。基本上没什么帮助但也不疼...

[![Revisited Concept: High Performance web server w/ Promises](img/d1a436ce51e405477f124ec5eb2d81b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K0RwbY5k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theopensourceu.org/conteimg/2018/03/chart.PNG)

没有场景抛出任何错误(据我回忆，这与我第一次运行时不同。)*我已经做出了假设，这与我选择的无极库有关；[蓝鸟众所周知，写得非常快](http://bluebirdjs.com/docs/benchmarks.html)* 。

# 原始结果

以下是来自火炮的“*剪切和粘贴*原始结果，供详细参考和查看。此外，以防我在图表或表格中输入错误。

# 2 个虚拟 CPU，2 个 GiB RAM

## 基本- 10 个“虚拟用户”，20 个中计

```
λ artillery quick --count 10 -n 20 http://159.65.99.177:3000/basic
Started phase 0, duration: 1s @ 15:00:41(-0600) 2018-03-06
Report @ 15:00:44(-0600) 2018-03-06
  Scenarios launched: 10
  Scenarios completed: 10
  Requests completed: 200
  RPS sent: 68.49
  Request latency:
    min: 64.3
    max: 139.3
    median: 71.8
    p95: 119.8
    p99: 137.9
  Codes:
    200: 200

All virtual users finished
Summary report @ 15:00:44(-0600) 2018-03-06
  Scenarios launched: 10
  Scenarios completed: 10
  Requests completed: 200
  RPS sent: 68.03
  Request latency:
    min: 64.3
    max: 139.3
    median: 71.8
    p95: 119.8
    p99: 137.9
  Scenario counts:
    0: 10 (100%)
  Codes:
    200: 200 
```

## 承诺——10 个“虚拟用户”，20 个中计

```
λ artillery quick --count 10 -n 20 http://159.65.99.177:3000/promise
Started phase 0, duration: 1s @ 15:01:44(-0600) 2018-03-06
Report @ 15:01:47(-0600) 2018-03-06
  Scenarios launched: 10
  Scenarios completed: 10
  Requests completed: 200
  RPS sent: 82.64
  Request latency:
    min: 62.3
    max: 144.8
    median: 71.1
    p95: 116.5
    p99: 140.7
  Codes:
    200: 200

All virtual users finished
Summary report @ 15:01:47(-0600) 2018-03-06
  Scenarios launched: 10
  Scenarios completed: 10
  Requests completed: 200
  RPS sent: 82.3
  Request latency:
    min: 62.3
    max: 144.8
    median: 71.1
    p95: 116.5
    p99: 140.7
  Scenario counts:
    0: 10 (100%)
  Codes:
    200: 200 
```

## 基本- 50 个“虚拟用户”，30 个中计

```
λ artillery quick --count 50 -n 30 http://159.65.99.177:3000/basic
Started phase 0, duration: 1s @ 15:06:18(-0600) 2018-03-06
Report @ 15:06:22(-0600) 2018-03-06
  Scenarios launched: 50
  Scenarios completed: 50
  Requests completed: 1500
  RPS sent: 373.13
  Request latency:
    min: 63
    max: 376.4
    median: 84.5
    p95: 130.4
    p99: 149.2
  Codes:
    200: 1500

All virtual users finished
Summary report @ 15:06:22(-0600) 2018-03-06
  Scenarios launched: 50
  Scenarios completed: 50
  Requests completed: 1500
  RPS sent: 372.21
  Request latency:
    min: 63
    max: 376.4
    median: 84.5
    p95: 130.4
    p99: 149.2
  Scenario counts:
    0: 50 (100%)
  Codes:
    200: 1500 
```

## 承诺——50 个“虚拟用户”，30 个中计

```
λ artillery quick --count 50 -n 30 http://159.65.99.177:3000/promise
Started phase 0, duration: 1s @ 15:06:57(-0600) 2018-03-06
Report @ 15:07:01(-0600) 2018-03-06
  Scenarios launched: 50
  Scenarios completed: 50
  Requests completed: 1500
  RPS sent: 374.06
  Request latency:
    min: 63.2
    max: 360.3
    median: 82.6
    p95: 132.8
    p99: 166
  Codes:
    200: 1500

All virtual users finished
Summary report @ 15:07:01(-0600) 2018-03-06
  Scenarios launched: 50
  Scenarios completed: 50
  Requests completed: 1500
  RPS sent: 372.21
  Request latency:
    min: 63.2
    max: 360.3
    median: 82.6
    p95: 132.8
    p99: 166
  Scenario counts:
    0: 50 (100%)
  Codes:
    200: 1500 
```

## 基本——100 个“虚拟用户”，30 个中计

```
λ artillery quick --count 100 -n 30 http://159.65.99.177:3000/basic
Started phase 0, duration: 2s @ 15:11:15(-0600) 2018-03-06
Report @ 15:11:21(-0600) 2018-03-06
  Scenarios launched: 100
  Scenarios completed: 100
  Requests completed: 3000
  RPS sent: 543.48
  Request latency:
    min: 62.3
    max: 220.3
    median: 114
    p95: 155.4
    p99: 174.5
  Codes:
    200: 3000

All virtual users finished
Summary report @ 15:11:21(-0600) 2018-03-06
  Scenarios launched: 100
  Scenarios completed: 100
  Requests completed: 3000
  RPS sent: 541.52
  Request latency:
    min: 62.3
    max: 220.3
    median: 114
    p95: 155.4
    p99: 174.5
  Scenario counts:
    0: 100 (100%)
  Codes:
    200: 3000 
```

## 承诺——100 个“虚拟用户”，30 个中计

```
λ artillery quick --count 100 -n 30 http://159.65.99.177:3000/promise
Started phase 0, duration: 2s @ 15:12:23(-0600) 2018-03-06
Report @ 15:12:28(-0600) 2018-03-06
  Scenarios launched: 100
  Scenarios completed: 100
  Requests completed: 3000
  RPS sent: 542.5
  Request latency:
    min: 63.6
    max: 240.4
    median: 117.1
    p95: 196.8
    p99: 209.9
  Codes:
    200: 3000

All virtual users finished
Summary report @ 15:12:28(-0600) 2018-03-06
  Scenarios launched: 100
  Scenarios completed: 100
  Requests completed: 3000
  RPS sent: 540.54
  Request latency:
    min: 63.6
    max: 240.4
    median: 117.1
    p95: 196.8
    p99: 209.9
  Scenario counts:
    0: 100 (100%)
  Codes:
    200: 3000 
```

## 基本——200 个“虚拟用户”，50 个中计

```
λ artillery quick --count 200 -n 50 http://159.65.99.177:3000/basic
Started phase 0, duration: 4s @ 15:21:01(-0600) 2018-03-06
Report @ 15:21:11(-0600) 2018-03-06
  Scenarios launched: 200
  Scenarios completed: 0
  Requests completed: 5737
  RPS sent: 592.91
  Request latency:
    min: 62.1
    max: 528.2
    median: 272
    p95: 403.4
    p99: 431.9
  Codes:
    200: 5737

Report @ 15:21:17(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 200
  Requests completed: 4263
  RPS sent: 735.08
  Request latency:
    min: 66.1
    max: 552.1
    median: 214.4
    p95: 272.9
    p99: 289.2
  Codes:
    200: 4263

All virtual users finished
Summary report @ 15:21:17(-0600) 2018-03-06
  Scenarios launched: 200
  Scenarios completed: 200
  Requests completed: 10000
  RPS sent: 641.03
  Request latency:
    min: 62.1
    max: 552.1
    median: 245.8
    p95: 382.5
    p99: 424.9
  Scenario counts:
    0: 200 (100%)
  Codes:
    200: 10000 
```

## 承诺——200 个“虚拟用户”，50 个中计

```
λ artillery quick --count 200 -n 50 http://159.65.99.177:3000/promise
Started phase 0, duration: 4s @ 15:22:10(-0600) 2018-03-06
Report @ 15:22:20(-0600) 2018-03-06
  Scenarios launched: 200
  Scenarios completed: 0
  Requests completed: 5664
  RPS sent: 585.81
  Request latency:
    min: 65.7
    max: 468.6
    median: 290.4
    p95: 384.7
    p99: 414.3
  Codes:
    200: 5664

Report @ 15:22:27(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 200
  Requests completed: 4336
  RPS sent: 589.17
  Request latency:
    min: 62.7
    max: 433.5
    median: 249.1
    p95: 375.4
    p99: 408.7
  Codes:
    200: 4336

All virtual users finished
Summary report @ 15:22:27(-0600) 2018-03-06
  Scenarios launched: 200
  Scenarios completed: 200
  Requests completed: 10000
  RPS sent: 585.14
  Request latency:
    min: 62.7
    max: 468.6
    median: 270.6
    p95: 381.6
    p99: 410
  Scenario counts:
    0: 200 (100%)
  Codes:
    200: 10000 
```

## 基本- 500 个“虚拟用户”，100 个中计

```
λ artillery quick --count 500 -n 100 http://159.65.99.177:3000/basic
Started phase 0, duration: 10s @ 15:27:51(-0600) 2018-03-06
Report @ 15:28:01(-0600) 2018-03-06
  Scenarios launched: 499
  Scenarios completed: 0
  Requests completed: 2969
  RPS sent: 347.05
  Request latency:
    min: 68.3
    max: 1459.7
    median: 657.3
    p95: 1170.9
    p99: 1273.4
  Codes:
    200: 2969

Report @ 15:28:11(-0600) 2018-03-06
  Scenarios launched: 1
  Scenarios completed: 0
  Requests completed: 3221
  RPS sent: 322.3
  Request latency:
    min: 1228.9
    max: 2406.5
    median: 1474.4
    p95: 2157.3
    p99: 2234.8
  Codes:
    200: 3221

Report @ 15:28:21(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3465
  RPS sent: 347.19
  Request latency:
    min: 1170.7
    max: 1972.4
    median: 1472.9
    p95: 1591.9
    p99: 1623.6
  Codes:
    200: 3465

Report @ 15:28:31(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 2944
  RPS sent: 295.29
  Request latency:
    min: 1336.3
    max: 2194.1
    median: 1659.1
    p95: 1851.4
    p99: 1917.3
  Codes:
    200: 2944

Report @ 15:28:41(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 2695
  RPS sent: 269.5
  Request latency:
    min: 1511
    max: 2925.6
    median: 1708.7
    p95: 2599.9
    p99: 2763
  Codes:
    200: 2695

Report @ 15:28:51(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3173
  RPS sent: 317.3
  Request latency:
    min: 1215.4
    max: 2177.5
    median: 1605.9
    p95: 1855.3
    p99: 1914.4
  Codes:
    200: 3173

Report @ 15:29:01(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3103
  RPS sent: 311.55
  Request latency:
    min: 1365.4
    max: 2064.8
    median: 1575.2
    p95: 1790.9
    p99: 1839.5
  Codes:
    200: 3103

Report @ 15:29:11(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3926
  RPS sent: 392.21
  Request latency:
    min: 1007.3
    max: 1879
    median: 1298.1
    p95: 1652.6
    p99: 1695.8
  Codes:
    200: 3926

Report @ 15:29:21(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3500
  RPS sent: 351.05
  Request latency:
    min: 1035.3
    max: 2560.6
    median: 1223.3
    p95: 2261.8
    p99: 2317.5
  Codes:
    200: 3500

Report @ 15:29:31(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3762
  RPS sent: 375.82
  Request latency:
    min: 892
    max: 2616
    median: 1372.8
    p95: 1992.1
    p99: 2263.5
  Codes:
    200: 3762

Report @ 15:29:41(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 4787
  RPS sent: 478.22
  Request latency:
    min: 872.3
    max: 1304.4
    median: 1029.1
    p95: 1211.3
    p99: 1256.7
  Codes:
    200: 4787

Report @ 15:29:51(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 2
  Requests completed: 4288
  RPS sent: 429.89
  Request latency:
    min: 955.4
    max: 1546.2
    median: 1154.3
    p95: 1316
    p99: 1342.4
  Codes:
    200: 4288

Report @ 15:30:01(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 43
  Requests completed: 3345
  RPS sent: 330.86
  Request latency:
    min: 1169.9
    max: 2025.5
    median: 1439.5
    p95: 1695.3
    p99: 1735.2
  Codes:
    200: 3345

Report @ 15:30:11(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 128
  Requests completed: 3091
  RPS sent: 297.19
  Request latency:
    min: 1062.3
    max: 2158.7
    median: 1313.9
    p95: 1688.9
    p99: 1725.6
  Codes:
    200: 3091

Report @ 15:30:17(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 327
  Requests completed: 1731
  RPS sent: 256.67
  Request latency:
    min: 67.3
    max: 1544.1
    median: 812.7
    p95: 1134.7
    p99: 1194.8
  Codes:
    200: 1731

All virtual users finished
Summary report @ 15:30:17(-0600) 2018-03-06
  Scenarios launched: 500
  Scenarios completed: 500
  Requests completed: 50000
  RPS sent: 342.4
  Request latency:
    min: 67.3
    max: 2925.6
    median: 1343.2
    p95: 1868.3
    p99: 2289
  Scenario counts:
    0: 500 (100%)
  Codes:
    200: 50000 
```

## 承诺——500 个“虚拟用户”，100 个中计

```
λ artillery quick --count 500 -n 100 http://159.65.99.177:3000/promise
Started phase 0, duration: 10s @ 15:23:49(-0600) 2018-03-06
Report @ 15:23:59(-0600) 2018-03-06
  Scenarios launched: 499
  Scenarios completed: 0
  Requests completed: 2840
  RPS sent: 334.13
  Request latency:
    min: 73.3
    max: 1409
    median: 838
    p95: 1189.6
    p99: 1291.6
  Codes:
    200: 2840

Report @ 15:24:09(-0600) 2018-03-06
  Scenarios launched: 1
  Scenarios completed: 0
  Requests completed: 3281
  RPS sent: 328.3
  Request latency:
    min: 1109.5
    max: 2140.4
    median: 1508.1
    p95: 1780.6
    p99: 1811.6
  Codes:
    200: 3281

Report @ 15:24:19(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3164
  RPS sent: 316.4
  Request latency:
    min: 1341.6
    max: 2064.7
    median: 1568.2
    p95: 1747.3
    p99: 1788.5
  Codes:
    200: 3164

Report @ 15:24:29(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3389
  RPS sent: 338.9
  Request latency:
    min: 1130.1
    max: 2303.4
    median: 1441.1
    p95: 1931.9
    p99: 2009.6
  Codes:
    200: 3389

Report @ 15:24:39(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3653
  RPS sent: 365.3
  Request latency:
    min: 1129.9
    max: 1912.8
    median: 1344
    p95: 1672
    p99: 1750.4
  Codes:
    200: 3653

Report @ 15:24:49(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3239
  RPS sent: 323.8
  Request latency:
    min: 1177.4
    max: 2181.4
    median: 1541.4
    p95: 1996.8
    p99: 2080.1
  Codes:
    200: 3239

Report @ 15:24:59(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3167
  RPS sent: 316.7
  Request latency:
    min: 1211.1
    max: 2267.6
    median: 1557
    p95: 1819.5
    p99: 1874.5
  Codes:
    200: 3167

Report @ 15:25:09(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3285
  RPS sent: 328.6
  Request latency:
    min: 1291.1
    max: 2083.3
    median: 1534.9
    p95: 1748.2
    p99: 1818.5
  Codes:
    200: 3285

Report @ 15:25:20(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3217
  RPS sent: 322.02
  Request latency:
    min: 1195
    max: 2412.1
    median: 1554.5
    p95: 1967.8
    p99: 2017.9
  Codes:
    200: 3217

Report @ 15:25:30(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3744
  RPS sent: 375.53
  Request latency:
    min: 1024.8
    max: 1831.5
    median: 1325
    p95: 1604.3
    p99: 1667.5
  Codes:
    200: 3744

Report @ 15:25:40(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3376
  RPS sent: 337.6
  Request latency:
    min: 1266.4
    max: 1897.7
    median: 1509.7
    p95: 1641.8
    p99: 1688.3
  Codes:
    200: 3376

Report @ 15:25:50(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 3311
  RPS sent: 331.1
  Request latency:
    min: 1124.3
    max: 2060.3
    median: 1534.3
    p95: 1781.6
    p99: 1822.9
  Codes:
    200: 3311

Report @ 15:26:00(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 10
  Requests completed: 3122
  RPS sent: 312.45
  Request latency:
    min: 1254.5
    max: 2007.5
    median: 1627.3
    p95: 1750.8
    p99: 1789.1
  Codes:
    200: 3122

Report @ 15:26:10(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 50
  Requests completed: 3369
  RPS sent: 332.23
  Request latency:
    min: 1104.9
    max: 1970.4
    median: 1416.5
    p95: 1659.1
    p99: 1730.9
  Codes:
    200: 3369

Report @ 15:26:20(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 209
  Requests completed: 3203
  RPS sent: 299.4
  Request latency:
    min: 827.1
    max: 1679.7
    median: 1181.5
    p95: 1464.4
    p99: 1502.7
  Codes:
    200: 3203

Report @ 15:26:22(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 231
  Requests completed: 640
  RPS sent: 162.95
  Request latency:
    min: 118.2
    max: 944.7
    median: 790.7
    p95: 894
    p99: 928.5
  Codes:
    200: 640

All virtual users finished
Summary report @ 15:26:22(-0600) 2018-03-06
  Scenarios launched: 500
  Scenarios completed: 500
  Requests completed: 50000
  RPS sent: 326.71
  Request latency:
    min: 73.3
    max: 2412.1
    median: 1460.3
    p95: 1776.8
    p99: 1962.2
  Scenario counts:
    0: 500 (100%)
  Codes:
    200: 50000 
```

# 1x CPU，1 GiB Ram

## 基本- 500 个“虚拟用户”，100 个中计

```
λ artillery quick --count 500 -n 100 http://159.65.98.204:3000/basic
Started phase 0, duration: 10s @ 16:06:07(-0600) 2018-03-06
Report @ 16:06:17(-0600) 2018-03-06
  Scenarios launched: 499
  Scenarios completed: 0
  Requests completed: 6656
  RPS sent: 714.79
  Request latency:
    min: 64.3
    max: 734.5
    median: 357.6
    p95: 558.2
    p99: 615.7
  Codes:
    200: 6656

Report @ 16:06:27(-0600) 2018-03-06
  Scenarios launched: 1
  Scenarios completed: 0
  Requests completed: 8778
  RPS sent: 876.05
  Request latency:
    min: 499.7
    max: 868.6
    median: 575.8
    p95: 605.5
    p99: 626.5
  Codes:
    200: 8778

Report @ 16:06:37(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 8864
  RPS sent: 884.73
  Request latency:
    min: 476.5
    max: 873.3
    median: 568.8
    p95: 603.4
    p99: 615.9
  Codes:
    200: 8864

Report @ 16:06:47(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 8760
  RPS sent: 874.25
  Request latency:
    min: 473.4
    max: 849.6
    median: 569.5
    p95: 614.6
    p99: 633.4
  Codes:
    200: 8760

Report @ 16:06:57(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 65
  Requests completed: 8857
  RPS sent: 877.45
  Request latency:
    min: 467.9
    max: 856.4
    median: 542.4
    p95: 590
    p99: 613.9
  Codes:
    200: 8857

Report @ 16:07:07(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 435
  Requests completed: 8085
  RPS sent: 802.73
  Request latency:
    min: 63
    max: 554.6
    median: 415.7
    p95: 503
    p99: 517.4
  Codes:
    200: 8085

All virtual users finished
Summary report @ 16:07:07(-0600) 2018-03-06
  Scenarios launched: 500
  Scenarios completed: 500
  Requests completed: 50000
  RPS sent: 834.72
  Request latency:
    min: 63
    max: 873.3
    median: 552.7
    p95: 600.5
    p99: 622.7
  Scenario counts:
    0: 500 (100%)
  Codes:
    200: 50000 
```

## 承诺——500 个“虚拟用户”，100 个中计

```
λ artillery quick --count 500 -n 100 http://159.65.98.204:3000/promise
Started phase 0, duration: 10s @ 16:08:07(-0600) 2018-03-06
Report @ 16:08:17(-0600) 2018-03-06
  Scenarios launched: 499
  Scenarios completed: 0
  Requests completed: 7403
  RPS sent: 788.61
  Request latency:
    min: 63.5
    max: 700
    median: 306.2
    p95: 516.4
    p99: 564.6
  Codes:
    200: 7403

Report @ 16:08:28(-0600) 2018-03-06
  Scenarios launched: 1
  Scenarios completed: 0
  Requests completed: 9068
  RPS sent: 904.99
  Request latency:
    min: 470.2
    max: 822.4
    median: 555.3
    p95: 593.2
    p99: 613.7
  Codes:
    200: 9068

Report @ 16:08:38(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 0
  Requests completed: 8916
  RPS sent: 893.39
  Request latency:
    min: 473.4
    max: 872.2
    median: 561.1
    p95: 603.4
    p99: 629.3
  Codes:
    200: 8916

Report @ 16:08:48(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 9
  Requests completed: 8784
  RPS sent: 875.75
  Request latency:
    min: 503.8
    max: 694.4
    median: 565.7
    p95: 645.4
    p99: 677.2
  Codes:
    200: 8784

Report @ 16:08:58(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 90
  Requests completed: 8995
  RPS sent: 888.62
  Request latency:
    min: 406.5
    max: 804.5
    median: 516.3
    p95: 569
    p99: 588.2
  Codes:
    200: 8995

Report @ 16:09:06(-0600) 2018-03-06
  Scenarios launched: 0
  Scenarios completed: 401
  Requests completed: 6834
  RPS sent: 799.25
  Request latency:
    min: 62.2
    max: 533.6
    median: 375.8
    p95: 449.2
    p99: 506.3
  Codes:
    200: 6834

All virtual users finished
Summary report @ 16:09:06(-0600) 2018-03-06
  Scenarios launched: 500
  Scenarios completed: 500
  Requests completed: 50000
  RPS sent: 855.72
  Request latency:
    min: 62.2
    max: 872.2
    median: 536.9
    p95: 598.4
    p99: 645.7
  Scenario counts:
    0: 500 (100%)
  Codes:
    200: 50000 
```