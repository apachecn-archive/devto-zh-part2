# 如何用 PHP、cURL 和 API 创建自己的威胁情报平台

> 原文：<https://dev.to/sm0k3/how-to-create-own-threat-intelligence-platform-with-php-curl-and-api-53ch>

[![alt text](img/ad97cea32864aad60a63f396a2c8d8df.png "Threat Intelligence")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--whZhf6Q8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ry2oaewi8q2tywa6r90e.jpg)

几年前，当**信息安全**作为一个商业方向还不那么受欢迎的时候，还没有那么多有用的服务可以帮助你检测你的基础设施或网站上的技术问题、安全问题和漏洞。

实际上，问题不仅在于工具的可用性和质量，还在于所使用的技术、人员资格，如果谈到人为因素，那么就缺乏关于如何使您的代码安全、最佳实践模式的可用性等信息，这些信息是从以前的经验和创新技术中积累起来的。

作为一名优秀的开发人员，你必须亲自检查代码的漏洞，作为一名优秀的管理员，你需要安全地配置你的 Windows 或 Linux 机器/服务器，同样，一切都是基于如何发现和确定这些问题/漏洞的经验，没有多少开发人员能够正确地做到这一点。这里我们也不谈论**渗透测试**,因为它并不流行，也没有得到很好的推广。

从过去 5-7 年的旧新闻中，你可以找到许多有趣的黑客和曝光:索尼，国际银行和许多其他人，但这是怎么发生的？
有人忘记了使用默认/简单/演示凭证的测试/试运行服务器，有人使用简单和流行的密码，在关键漏洞被披露后没有更新，所有这些都导致了机密信息的严重泄露。

今天，有许多现成的，经过时间和社区测试的解决方案，为了不重新发明整个自行车，你可以尝试建立和调整系统来满足你的需求。是的，这个领域的许多系统都要花钱，但是如果把它们结合起来，关联和分析来自几个这样的来源的信息，你可以得到与从这些系统中的一个付费订阅几乎相同的输出(只是作为一个例子，当然不是 100%相同:)

这里列出了可以通过 API 轻松集成的平台:

*   肖丹
*   VirusTotal
*   网上冲浪
*   Vulners
*   打开 Bug 赏金

让我们来看看他是怎么工作的:

1.  通过本地 PHP 函数，我们可以找到我们需要开始分析的域名的 IP 地址
2.  我们将 IP/域名发送到 Shodan 和其他平台，以接收关于垃圾邮件活动和恶意软件活动的 IP 黑名单的所有所需数据，可以查看 IP 地址的变化历史(可以帮助检测隐藏在 WAF/CDN/Anti-DDoS 服务后面的 IP)
3.  它可能会帮助您找到漏洞猎人通过 OpenBugBounty 项目披露的可能漏洞
4.  Vulners 将显示所有可用的漏洞，检测和过时的软件安装在您的服务器上

在我的例子中，我还添加了类似“我被 pwned 了吗”和“IBM X-Force”这样的服务，以获得关于目标的更多实际数据:

[![alt text](img/edbbf7b94acdc17b7a5e3464c08e182e.png "Dev.to check with custom threat intelligence platform")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HLcpfMAb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7cvo3o872yn3cji3ioyj.PNG)

[![alt text](img/4537b9010126610e15a00a99ba2c365b.png "Dev.to check with custom threat intelligence platform")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G4bxZ8nF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uhq5k7e0ehqqds2qrc00.PNG)

正如您所理解的，不需要专业的安全工程师或开发人员来找出这些数据并对项目进行基本的安全检查，我们需要做的事情很少:

1.  查找适用的服务
2.  阅读 API 文档
3.  用 curl 基本了解 php

下面两个函数，一个用于 shodan，另一个用于 virustotal:

```
 function shodanHost($host) {
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "https://api.shodan.io/shodan/host/".$host."?key=<YourApiKey");
    curl_setopt($ch, CURLOPT_HEADER, 0);
    curl_setopt($ch, CURLOPT_USERAGENT,'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.13) Gecko/20080311 Firefox/2.0.0.13');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
    $shodanResponse = curl_exec($ch);
    curl_close ($ch);
    return json_decode($shodanResponse);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 function virustotalCheck($url) {
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "https://www.virustotal.com/vtapi/v2/domain/report?apikey=YourApiKey&domain=".$url);
    curl_setopt($ch, CURLOPT_HEADER, 0);
    curl_setopt($ch, CURLOPT_USERAGENT,'Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.13) Gecko/20080311 Firefox/2.0.0.13');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
    $virustotalResponse = curl_exec($ch);
    curl_close ($ch);
    return json_decode($virustotalResponse);
} 
```

Enter fullscreen mode Exit fullscreen mode

你得到的所有回答都是从 JSON 解码而来的易于理解的结构化格式，你可以随心所欲地输出。基于这种方法，您可以处理收到的数据，并开发可能的攻击或研究载体。

另外，使用 shodan 或 virustotal 等服务收集信息并不违法，您不需要对系统和服务执行真正的扫描，但您可以获得非常有用的数据来找出可能的安全问题。