# 处理 AWS 简单电子邮件服务通知

> 原文：<https://dev.to/abdallah/handling-aws-simple-email-service-notifications-3n9o>

显然，仅仅收集你的电子邮件发送、退回和投诉信息是不够的。你还需要在出现问题时采取行动，否则亚马逊会将你的账户置于缓刑期，如果你不解决问题，甚至可能完全关闭你的账户。

根据他们的示例代码和 aws 博客中的[解释，实现起来非常容易](https://aws.amazon.com/blogs/messaging-and-targeting/handling-bounces-and-complaints/)

下面是我用来设置它的步骤:

*   创建一个社交网络话题。让我们称之为 SES-bounds。或者创建 2 个 SNS 主题(1 个用于退信，另一个用于投诉)。我只带一个。
*   在你的网站上创建一个服务来处理退信。为此，我使用了一些简单的 PHP 代码(粘贴在下面)。
*   在您的 SNS 主题中创建订阅(首先创建)

该服务应该处理两件事:

*   订阅(那会发生一次！):调用 SubscribeURL 就大功告成了。
*   拒绝/投诉:把邮件地址从你的邮件列表中删除，或者把它们列入黑名单，或者…主要的想法是停止给那些投诉的人发邮件。不要再试图给那些被退回的地址发邮件了。

这是 PHP 服务的代码，非常简单

```
if ( 'POST' !== $\_SERVER['REQUEST\_METHOD'] ) { http\_response\_code( 405 ); die; } require 'vendor/autoload.php'; use Aws\Sns\Message; use Aws\Sns\MessageValidator; function parse\_bounce( $message ) { $recipients = array(); foreach ( $message['bounce']['bouncedRecipients'] as $recipient ) { $recipients[] = $recipient['emailAddress']; } switch ( $message['bounce']['bounceType'] ) { case 'Transient': error\_log( "BouncesController - Soft bounces occurred for " . join( ', ', $recipients ) ); return array(); break; case 'Permanent': error\_log( "BouncesController - Hard bounces occurred for " . join( ', ', $recipients ) ); return $recipients; break; } } function parse\_complaint( $message ) { $recipients = array(); foreach ( $message['complaint']['complainedRecipients'] as $recipient ) { $recipients[] = $recipient['emailAddress']; } return $recipients; } function blacklist\_recipients( $recipients ) { foreach ( $recipients as $email\_address ) { // blacklist those emails } } try { $sns\_message = Message::fromRawPostData(); $validator = new MessageValidator(); $validator-\>validate( $sns\_message ); if ( $validator-\>isValid( $sns\_message ) ) { if ( in\_array( $sns\_message['Type'], ['SubscriptionConfirmation', 'UnsubscribeConfirmation'] ) ) { file\_get\_contents( $sns\_message['SubscribeURL'] ); error\_log( 'Subscribed to ' . $sns\_message['SubscribeURL'] ); } if ( $sns\_message['Type'] == 'Notification' ) { $message = $sns\_message['Message']; $notification\_type = $message['notificationType']; if ( $notification\_type == 'Bounce' ) { blacklist\_recipients( parse\_bounce( $message ) ); } if ( $notification\_type == 'Complaint' ) { blacklist\_recipients( parse\_complaint( $message ) ); } } } } catch ( Exception $e ) { error\_log( 'Error: ' . $e-\>getMessage() ); http\_response\_code( 404 ); die; } 
```

不要忘记运行 composer 来获得这些依赖项:

```
{ "require": { "aws/aws-sdk-php": "3.\*", "aws/aws-php-sns-message-validator": "1.4.0" } } 
```

并且确保你没有发送未经请求的电子邮件！就是不好看。