# 每个开发人员都应该了解的安全基础知识

> 原文：<https://dev.to/ben/the-fundamentals-of-security-every-developer-should-understand-2m27>

上周我写了这个帖子:

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 每个开发人员都应该了解哪些安全基础知识？

### 本哈尔彭 3 月 24 日 181 分钟阅读

#discuss #security](/ben/what-are-some-fundamentals-of-security-every-developer-should-understand-3ki3)

我想和你分享一些很棒的答案。这里有大量的智慧，所以继续读下去。

[![dmfay profile image](img/130564eb95ceaeb2bb4b978edaafd9f3.png) ](/dmfay) [ Dian Fay ](/dmfay) • [<time datetime="2018-03-24T17:32:59Z" class="date-short-year"> Mar 24 '18 </time>](https://dev.to/dmfay/comment/2knh) 

1.  不要相信任何人。*尤其是*自己。
2.  唯一绝对安全的系统是一个已经断开连接、关闭电源、装在混凝土里、蒙着眼睛从直升机上丢进海里的系统。
3.  您可以使用的任何功能都是别有用心的人可以使用的功能。您可以通过您的系统访问的数据是其他人可以通过您的系统访问的数据。后门是一种固有的安全风险。
4.  [假设用户输入是恶意的，直到证明不是这样](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))。
5.  如果你有足够的能力开发自己的加密软件，你已经有了一份专门从事加密的工作。
6.  如果你只需要测试输入是否匹配你已经存储的东西(比如密码)，散列，不要加密。
7.  绑定准备好的语句，不要在查询中插入参数。
8.  如果你有一个公开可见的 API 来支持你的站点，[记住你的站点并不是唯一能击中它的东西](https://github.com/pillarjs/understanding-csrf)。
9.  思考并测试边缘案例。

[![adnanrahic profile image](img/9962f58ae35c1c4cf7a83b99dde848e3.png) ](/adnanrahic) [ Adnan Rahić ](/adnanrahic) • [<time datetime="2018-03-24T15:17:40Z" class="date-short-year"> Mar 24 '18 </time>](https://dev.to/adnanrahic/comment/2kml) 

1.  验证输入数据！
2.  说真的，验证输入数据。
3.  我提到验证输入数据了吗？

[![dmerand profile image](img/c65701e291f1b68bda273ed3e9ef820b.png) ](/dmerand) [ Donald Merand ](/dmerand) • [<time datetime="2018-03-24T16:01:07Z" class="date-short-year"> Mar 24 '18 </time>](https://dev.to/dmerand/comment/2kn2) 

您存储的数据越少，您面临的安全风险就越少，您的参与者就越安全。不要囤积理论上有用的数据——只保存你需要的东西，每次当你认为你需要它的时候，问问你自己。

如果你*必须*存储数据，尤其是敏感数据，千万不要以纯文本格式存储！研究 bcrypt 之类的哈希算法。

总是给你的参与者删除他们的数据的选项，当他们要求你删除时，你就真的删除。

[![mortoray profile image](img/3f601f0d3e93536df22a4b2d92f1094d.png) ](/mortoray) [ edA‑qa mort‑ora‑y ](/mortoray) • [<time datetime="2018-03-24T19:40:45Z" class="date-short-year"> Mar 24 '18 </time>](https://dev.to/mortoray/comment/2ko4) 

*   安保很难。有必要阅读各种攻击，以了解攻击方式的重要性。
*   你的系统会被攻破。缓解策略和“墙”一样重要。
*   一个系统从来都不是“安全的”，你只能用当前的风险和可用的资源来平衡安全目标。
*   隐私与安全密不可分。即使你不负责任，不关心你的用户，攻击者也会。
*   随着数据变得越来越有价值，安全性也变得越来越难。大多数系统确实是安全的，因为没有人真的想要他们存储的数据。随着一家公司变得成功，攻击者会来。
*   安全是一个移动的目标。您永远不会完成安全性的实现。
*   用户安全与公司安全一样重要。
*   公开安全性是知道它是正确的唯一方法。默默无闻是没有安全感的。
*   每个人都对安全负责。每个人和每台机器都是潜在的攻击媒介。

实用建议:

*   使用现有库
*   遵循最佳实践
*   让一切保持最新
*   敞开心扉，提出问题
*   防御性编码
*   意识到风险

[![pixeline profile image](img/c40e20321e12c3b8807bdd86465f86c3.png) ](/pixeline) [ Alexandre Plennevaux ](/pixeline) • [<time datetime="2018-03-26T07:47:58Z" class="date-short-year"> Mar 26 '18 </time>](https://dev.to/pixeline/comment/2l5h) 

无论何时从外部处理数据，都要按以下顺序处理:

1.  使卫生
2.  使生效
3.  执行
4.  显示反馈

示例:

```
 $errors = array();

// 1\. Sanitisation
$email = filter_var($_POST['email'], FILTER_SANITIZE_EMAIL);

// 2\. Validation
if (false === filter_var($email, FILTER_VALIDATE_EMAIL)) {
   $errors['email'] =  "Invalid email address";
}

// 3\. Exécution
if (count($errors)> 0){
    echo 'There are errors : ';
    print_r($errors);
    exit;
}
// At this point, all is fine, let's open the gate...
$bdd = new PDO('mysql:host=localhost;dbname=test', 'root', '');
//...

// 4\. Feedback information 
```

Enter fullscreen mode Exit fullscreen mode

### 说到基本面...

[![robdwaller](img/0db3dce1c6a8a971e76c79fd5f5482f3.png)](/robdwaller) [## 软件开发人员应遵循的四条安全原则

### rob Waller 2 月 22 日 188 分钟阅读

#security #privacy](/robdwaller/four-security-principles-that-software-developers-should-follow-24gi)

我希望这有所帮助。在安全方面我们可以遵循很多具体的规则，但从原则出发是关键。而且我们永远不能假设每个人都已经知道并掌握了基本面。向前迈进，保护您的软件！