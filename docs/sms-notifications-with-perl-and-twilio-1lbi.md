# 使用 Perl 和 Twilio 的短信通知

> 原文：<https://dev.to/mitchjacksontech/sms-notifications-with-perl-and-twilio-1lbi>

Twilio 非常有用，而且易于集成。这是一种一旦开始使用就无法停止使用的工具。您可以在一个下午的时间内将短信通知和密码重置添加到您现有的应用程序中。

我在 dev.to 上看到了几个使用 Twilio 的例子，但是当然，到目前为止，还没有一个用于 Perl 的例子。

## 首先，整个简单程序:

```
#!/usr/bin/env perl
use v5.10;
use strict;
use warnings;
use LWP::UserAgent;

my %text_message = (
    From => '12513001300', # Me
    To   => '16198675309', # Jenny
    Body => 'Thank you for being a friend! Travel down the road and back again!'
);

my %credentials = (
    account_id => '____YOUR_ACCOUNT_SID_GOES_HERE____',
    auth_token => '____YOUR_AUTH_TOKEN_GOES_HERE____',
);

my %twilio = (
    # HTTP Authentication
    api_domain => 'api.twilio.com:443',
    api_realm  => 'Twilio API',

    # Keep your line length within 80 characters please!
    api_url    => 'https://api.twilio.com/2010-04-01/Accounts/'
                  . $credentials{account_id}
                  . '/Messages'
);

my $ua = LWP::UserAgent->new;
$ua->credentials(
    $twilio{api_domain},
    $twilio{api_realm},
    $credentials{account_id},
    $credentials{auth_token}
);

my $response = $ua->post( $twilio{api_url}, \%text_message );

say $response->is_success
    ? "Message delivered"
    : "Error: ".$response->decoded_content; 
```

Enter fullscreen mode Exit fullscreen mode

## 由于这是一个#初学者的帖子，让我们把它一条一条地分解。

```
#!/usr/bin/env perl
use v5.10;
use strict;
use warnings; 
```

Enter fullscreen mode Exit fullscreen mode

从一条[射棒线](https://en.wikipedia.org/wiki/Shebang_(Unix))开始。如果您从一个 shell 中执行这个程序，这就是 shell 如何知道为该程序使用哪个解释器的。`#!/usr/bin/env perl`已经取代了长在牙齿上的标准`#!/usr/bin/perl`理由很充分。

在这里调用`use v5.10;`允许我们使用 [say](https://perldoc.perl.org/functions/say.html) 函数。

`use strict`和`use warnings`要求 Perl 在你做蠢事时发出警告。比如拼错一个变量名，或者在凌晨 2 点写博客。

```
use LWP::UserAgent; 
```

Enter fullscreen mode Exit fullscreen mode

LWP::用户代理可以执行你的网络浏览器的大多数操作。您可以使用它来浏览页面、单击链接和发布表单。它可以保存持久的 cookies。比如说，如果你想写一个机器人来欺骗网上投票，不用再找了。

```
my %text_message = (
    From => '12513001300', # Me
    To   => '16198675309', # Jenny
    Body => 'Thank you for being a friend! Travel down the road and back again!'
); 
```

Enter fullscreen mode Exit fullscreen mode

将文本信息存储到一个散列中。您需要用自己的数据替换示例数据。珍妮已经受够了电话骚扰。

```
my %credentials = (
    account_id => '____YOUR_ACCOUNT_SID_GOES_HERE____',
    auth_token => '____YOUR_AUTH_TOKEN_GOES_HERE____',
); 
```

Enter fullscreen mode Exit fullscreen mode

您可以在仪表板的“设置/通用”下找到您的 Twilio 帐户凭据。Twilio 提供测试 API 访问。您可以使用测试 API 向自己发送文本。

```
my %twilio = (
    # HTTP Authentication
    api_domain => 'api.twilio.com:443',
    api_realm  => 'Twilio API',

    # Keep your line length within 80 characters please!
    api_url    => 'https://api.twilio.com/2010-04-01/Accounts/'
                  . $credentials{account_id}
                  . '/Messages'
); 
```

Enter fullscreen mode Exit fullscreen mode

向 Twilio API 发送请求所需的信息。Twilio 使用[基本 HTTP 认证](https://en.wikipedia.org/wiki/Basic_access_authentication)。这是该身份验证方案的域和领域。Twilio API URL 包含您的帐户 SID，因此完整的 API URL 连接在这里。

```
my $ua = LWP::UserAgent->new;
$ua->credentials(
    $twilio{api_domain},
    $twilio{api_realm},
    $credentials{account_id},
    $credentials{auth_token}
); 
```

Enter fullscreen mode Exit fullscreen mode

实例化 LWP::UserAgent 对象的新实例，并设置它将用于对 Twilio API 的 HTTP 请求的凭证。

```
my $response = $ua->post( $twilio{api_url}, \%text_message ); 
```

Enter fullscreen mode Exit fullscreen mode

$ua 向$twilio{api_url}发出 HTTP POST 请求。%text_message 中的数据作为表单值发送。这个操作返回一个 [HTTP::Response](https://metacpan.org/pod/HTTP::Response) 对象。

```
say $response->is_success
    ? "Message delivered"
    : "Error: ".$response->decoded_content; 
```

Enter fullscreen mode Exit fullscreen mode

该语句包括三进制操作符，这是 if-else 块的缩写。(旁注:如果你不知道什么是三元运算符，或者它叫什么，你在一些代码中看到这个语句块，你将如何在互联网搜索中了解这个语句在做什么？请在评论中告诉我...)

`$response->is_success`根据 web 服务器返回的 [HTTP 状态码](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)返回 true 或 false。

如果 Twilio 报告请求成功，您会在终端上看到**消息已发送**。如果 Twilio 报告了一个问题，您将在终端上看到 Twilio API 的完整响应。

## 看 Twilio 多容易？去做点什么吧...

坦率地说，Twilio 让它们变得如此易于使用，以至于大多数语言都可以将它们的使用分解为一行程序。您可以使用 curl 通过 shell 轻松发送短信。

如果你遇到一个 Twilio 工程师，一定要请他们喝一杯啤酒。

你还在读书吗？去造点东西吧！