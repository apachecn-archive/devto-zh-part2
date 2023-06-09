# 用 PHP 创建一个 Messenger 聊天机器人来满足你的业务需求

> 原文：<https://dev.to/_shahroznawaz/create-a-basic-chatbot-for-your-business-needs-in-php--1h8m>

聊天机器人是社交媒体沟通渠道的最新热点。这些自动聊天系统特别用于在社交媒体聊天中接待访客，并向访客提供基本信息。这些信息可能包括活动时间表、产品信息、最新交易、商店优惠和品牌的一般信息。

企业家和品牌营销人员使用聊天机器人来处理大量的聊天查询。这样，大量的查询可以用最低的成本轻松处理。聊天机器人有助于减少对人工客户服务代表(CSR)的依赖。这些聊天机器人审查常见的查询，以便人类 CSR 可以专注于需要处理多个信息源的查询。由于聊天机器人将所有对话导向预设的方向，因此使用这些聊天机器人代替人类 CSR 既简单又省时。

在本文中，我将创建一个简单的脸书聊天机器人，它可以与用户进行有效的对话。创建聊天机器人的过程包括几个相互关联的步骤。创建一个关于脸书的网页

第一步是创建一个脸书页面。这需要一个活动帐户。登录然后创建一个页面。选择适当的类别，然后填写所需信息，包括页面名称、地址和联系信息。完成页面创建过程。出于本教程的目的，我创建了一个名为 Cloudways School 的页面。

[![](img/cd0f5ce568d68b7196dc0aefe40c8425.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FEKPSsBb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6lr257icyz8sbx1vdoqa.png)

## 创建一个脸书应用

第二步是创建连接聊天机器人和 Facebook 页面聊天的脸书应用程序。要创建应用，请转到脸书开发者页面，然后单击添加新应用。添加所有必需的信息，然后单击创建应用 ID 按钮。脸书可能会要求您验证验证码。

[![](img/a1b228a673ed07c93122830c934195ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---DuLNU9j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uicyy07ghr066b9tuwfy.png)

该应用程序将出现在开发者仪表盘中。打开应用程序并将 messenger platform 添加到应用程序中。

接下来，添加将应用程序连接到网站的 webhook。要添加 webhook，请生成页面访问令牌。

[![](img/607397b56f6a1c03ebca542f658bf948.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fQlErCnY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2gcwp611cg4d33nocvrz.png)

接下来，定义回调 URL 和访问令牌。在令牌生成选项卡的正下方，转到 Webhook 选项卡，然后单击设置 Webhooks。插入 URL。请注意，为了实现这一点，URL 必须是 https 格式。

## 创建 bot.php 文件

脸书应用程序现已激活。我现在将为机器人创建文件。

创建一个名为 bot.php 的新文件，并向其中添加以下代码。这段代码基于 Remy Mellet 编写的一个简单的 Facebook Messenger 机器人代码。我添加了更多代码来处理发送给机器人的结构化消息。基本代码是:

忘记 BFCM 吧！今年夏天节省、扩展并赢得 Cloudways 赠品！
(赠品)

```
// parameters
$hubVerifyToken = 'cloudwaysschool';
$accessToken =   "EAxxxxxxxxxxxqgBAKWAgizvoHnQLZBR7ZxxxxxxxxxxxxxxxxxxxxxxxxxxxxxptYSymSdocFFCp1ink3EHRVMrCSxxxxxxxxxxxxxxxxxxxxwMZApStyA8GbqAxxxxxxxxxxxxxxxxxxxxxxxxxxx9R6QttFVyNS4ZBurwZDZD";

// check token at setup
if ($_REQUEST['hub_verify_token'] === $hubVerifyToken) {
  echo $_REQUEST['hub_challenge'];
  exit;
}

// handle bot's anwser
$input = json_decode(file_get_contents('php://input'), true);

$senderId = $input['entry'][0]['messaging'][0]['sender']['id'];
$messageText = $input['entry'][0]['messaging'][0]['message']['text'];
$response = null;

//set Message
if($messageText == "hi") {
    $answer = "Hello";
}

//send message to facebook bot
$response = [
    'recipient' => [ 'id' => $senderId ],
    'message' => [ 'text' => $answer ]
];

$ch = curl_init('https://graph.facebook.com/v2.6/me/messages?access_token='.$accessToken);
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($response));
curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
if(!empty($input)){
$result = curl_exec($ch);
}
curl_close($ch); 
```

Enter fullscreen mode Exit fullscreen mode

$hubVerifyToken 应包含与 webhook 标记值中提供的值相同的值。在本例中，我已经在代码和 webhook 中为 cloudwaysschool 设置了两个值。一旦成功验证了质询，就会在$input 中收集传入的消息。由此，发送方 ID 被提取到$senderId 中，消息文本被提取到$messageText 中。接下来，将 messageText 中的字符串与精确的关键字进行匹配，并发送预先选择的响应。

例如，如果用户发送 Hi，关键字匹配并触发回复。执行该功能的代码是:

```
if($messageText == "hi") {
    $answer = "Hello";
}

//send message to facebook bot
$response = [
    'recipient' => [ 'id' => $senderId ],
    'message' => [ 'text' => $answer ]
]; 
```

Enter fullscreen mode Exit fullscreen mode

最后，使用 CURL 通过传递$response 作为响应来发送消息..

## 上传并测试 bot.php

由于 bot.php 脸书网络钩子只支持 https，所以只能在支持 SSL 的主机平台上测试。幸运的是，所有顶级主机提供商都提供某种形式的 SSL。出于本教程的目的，我使用了 [Cloudways](https://platform.cloudways.com/signup) 。我已经为我的应用程序安装了 Letsencrypt，并将其指向域。每个 Cloudways 应用程序都会提供一个暂存 URL。然而，我把我的领域指向了 symfonyfeed.com。只需使用 Filezilla 将 bot.php 上传到 public_html 文件夹中。

此时，当你在 facebook 页面上说 hi 时，你会得到 hello 的回复。

[![](img/cacf58290813b9228064053277ea53d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BehK0-EC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4hjdqn9vd0be60h9rx6b.png)

## 发送结构化消息

除了 Hello，在回复 hi 时，Facebook Messenger 还提供了几个有趣的模板消息，涵盖了最新消息、图片和列表消息。也可以发送按钮链接作为响应。一些常见的消息模板有:

-按钮模板
-通用模板
-列表模板
-收据模板

我会在这里向你解释一些模板来启动你的第一个 php 信使机器人。您可以在这里看到消息模板的完整文档。

## 通用消息

Facebook Messenger 提供了几个模板，可用于向用户发送回复。例如，如果用户希望查询你网站上的最新博客，他们可以发送一个博客。一般的反应会是这样的:

[![](img/ec0eb3b0b8dd3ad013854ee88edec868.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qyp8HDvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1hfrkktnhmp3f282hrx.png)

让我们为这条消息设置一个新的条件。尝试以下代码:

```
if($messageText == "blog"){
     $answer = ["attachment"=>[
      "type"=>"template",
      "payload"=>[
        "template_type"=>"generic",
        "elements"=>[
          [
            "title"=>"Welcome to Peter\'s Hats",
            "item_url"=>"https://www.cloudways.com/blog/migrate-symfony-from-cpanel-to-cloud-hosting/",
            "image_url"=>"https://www.cloudways.com/blog/wp-content/uploads/Migrating-Your-Symfony-Website-To-Cloudways-Banner.jpg",
            "subtitle"=>"We\'ve got the right hat for everyone.",
            "buttons"=>[
              [
                "type"=>"web_url",
                "url"=>"https://petersfancybrownhats.com",
                "title"=>"View Website"
              ],
              [
                "type"=>"postback",
                "title"=>"Start Chatting",
                "payload"=>"DEVELOPER_DEFINED_PAYLOAD"
              ]              
            ]
          ]
        ]
      ]
    ]];

     $response = [
    'recipient' => [ 'id' => $senderId ],
    'message' => $answer 
];} 
```

Enter fullscreen mode Exit fullscreen mode

## 列出消息

Facebook Messenger bot 也可以发送列表来响应用户的查询。例如，如果用户希望看到所有最新发布的博客列表，所有用户都可以在今天发送。作为响应，机器人发送一个列表。

[![](img/0e313a69f29dfc8d387fec76eca7067c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6e-wgml1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wyqprgdufl5v6qiij20u.png)

将这段代码添加到 bot.php 来测试这个选项:

```
if($messageText == "today"){

 $answer = ["attachment"=>[
      "type"=>"template",
      "payload"=>[
        "template_type"=>"list",
        "elements"=>[
          [
             "title"=> "Classic T-Shirt Collection",
                    "image_url"=> "https://www.cloudways.com/blog/wp-content/uploads/Migrating-Your-Symfony-Website-To-Cloudways-Banner.jpg",
                    "subtitle"=> "See all our colors",
                    "default_action"=> [
                        "type"=> "web_url",
                        "url"=> "https://www.cloudways.com/blog/migrate-symfony-from-cpanel-to-cloud-hosting/",                       
                        "webview_height_ratio"=> "tall",
                        // "messenger_extensions"=> true,
                        // "fallback_url"=> "https://peterssendreceiveapp.ngrok.io/"
                    ],
            "buttons"=>[
              [
                "type"=>"web_url",
                "url"=>"https://petersfancybrownhats.com",
                "title"=>"View Website"
              ],
            ]
          ],
            [
            "title"=>"Welcome to Peter\'s Hats",
            "item_url"=>"https://www.cloudways.com/blog/migrate-symfony-from-cpanel-to-cloud-hosting/",
            "image_url"=>"https://www.cloudways.com/blog/wp-content/uploads/Migrating-Your-Symfony-Website-To-Cloudways-Banner.jpg",
            "subtitle"=>"We\'ve got the right hat for everyone.",
            "buttons"=>[
              [
                "type"=>"web_url",
                "url"=>"https://petersfancybrownhats.com",
                "title"=>"View Website"
              ],
            ]
          ],
            [
            "title"=>"Welcome to Peter\'s Hats",
            "item_url"=>"https://www.cloudways.com/blog/migrate-symfony-from-cpanel-to-cloud-hosting/",
            "image_url"=>"https://www.cloudways.com/blog/wp-content/uploads/Migrating-Your-Symfony-Website-To-Cloudways-Banner.jpg",
            "subtitle"=>"We\'ve got the right hat for everyone.",
            "buttons"=>[
              [
                "type"=>"web_url",
                "url"=>"https://petersfancybrownhats.com",
                "title"=>"View Website"
              ],
            ]
          ]

        ]
      ]
    ]];

  $response = [
    'recipient' => [ 'id' => $senderId ],
    'message' => $answer
];

}
elseif($messageText == "hi") {
    $answer = "Hello";
      $response = [
    'recipient' => [ 'id' => $senderId ],
    'message' => [ 'text' => $answer ]
];
} 
```

Enter fullscreen mode Exit fullscreen mode

## 按钮提示信息

Facebook Messenger 也可以发送可操作的按钮作为响应。例如，如果用户要求更多，响应将是:

要发送按钮作为响应，请尝试下面的代码:

```
if($messageText == "more") {

  $answer = ["attachment"=>[
      "type"=>"template",
      "payload"=>[
        "template_type"=>"button",
        "text"=>"What do you want to do next?",
        "buttons"=>[
          [
            "type"=>"web_url",
            "url"=>"https://petersapparel.parseapp.com",
            "title"=>"Show Website"
          ],
          [
            "type"=>"postback",
            "title"=>"Start Chatting",
            "payload"=>"USER_DEFINED_PAYLOAD"
          ]
        ]
      ]
      ]];

      $response = [
    'recipient' => [ 'id' => $senderId ],
    'message' => $answer
];
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在本教程中，我讨论了如何设置一个基本的 Facebook Messenger 来迎合你的访客。我强调了如何使用通用的和结构化的回答来增加谈话的内容。我希望你能够通过 PHP 理解各种消息的代码和实现。

如果您对代码有任何疑问或想加入讨论，请在下面留下您的评论。