# 编写漂亮的代码

> 原文：<https://dev.to/restoreddev/writing-beautiful-code-10p6>

因为编程是一门技术职业，所以我们经常在没有想到设计的情况下接近它。代码是为了让计算机做我们需要的事情，为什么设计如此重要？我们使用的大多数编程语言都是为程序员设计的，并被转换成一些为计算机优化的东西。因此，我们需要编写代码，不仅仅是为了编译器或解释器，也是为了让人们阅读。为了使代码可读，我把它当作一门艺术。当你把代码当作艺术时，它就变得很容易理解和扩展。创造性的代码也减少了错误，因为更容易理解它正在执行什么。

巧妙编码的问题是不容易做到。通常，一开始就让代码工作已经够难了，更不用说要有创造性了！然而，就像任何艺术形式一样，我们可以遵循一些基本技术来编写设计良好的代码。我仍然在探索编写漂亮代码的最佳方法，但是我遵循这些步骤来给我的编程带来一点艺术。

### 从一个单元测试开始

测试驱动开发并不被普遍喜欢，但是我发现它是一个非常好的实践。它不仅能确保你所有的代码都经过测试，还能迫使你在编写代码之前考虑你的代码将如何被使用。在这些例子中，让我们想象我们正在用 PHP 编写一个电子邮件客户端，我们希望在客户端的设计上有所创新。让我们从发送邮件的单元测试开始:

```
<?php

class EmailClientTest extends \PHPUnit\Framework\TestCase
{
    public function test_sending_an_email()
    {
        $client = new \Email\Client();
        $emailMessage = new \Email\Message('Hello world!');

        $result = $client->send('andrew@dev.to', $emailMessage);

        $this->assertTrue($result);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们首先编写了单元测试，所以我们已经知道我们希望代码是如何形成的。我们知道我们将有一个`Email`名称空间，其中有两个类分别代表`Client`和`Message`。我们还知道，我们将有一个`send`方法用于向客户端提交电子邮件消息的过程。在单元测试中，我们还决定了我们的代码片段将如何命名，这就引出了我的下一个观点。

### 使用语法命名变量和方法

程序员经常开玩笑说给事物命名很难，这是事实！然而，给一段代码命名不仅很难，也很重要。代码的命名方式决定了开发人员理解代码的速度，从而减少错误和开发时间。在上面的例子中，我们决定把所有的代码放在 Email 名称空间中，它包含了非常清楚的内容。接下来，我们选择`Client`来表示发送消息的对象，选择`Message`来表示电子邮件本身，这两个都是描述性名词。最后，我们称之为`send`方法，这是一个通常与电子邮件联系在一起的动词。现在，我们看到了一个很好的模式:使用名词作为类名，使用动词作为方法。

### 保持你的功能小

将一个操作的所有代码放在一个函数中是很诱人的。然而，为了可读性和可测试性，把它分成小块是非常重要的。让我们以`send`方法为例。

```
<?php

namespace Email;

class Client
{
    public function send(string $emailAddress, Message $message): bool
    {
        // Validate parameters
        $validateEmail = filter_var($emailAddress, FILTER_VALIDATE_EMAIL);
        $validateMessage = strlen($message->text()) < 1000;
        if (!$validateEmail || !$validateMessage) {
            return false;
        }

        // Communicate with email server
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, 'https://mail.example.com');
        curl_setopt($curl, CURLOPT_POST, true);
        curl_setopt($curl, CURLOPT_POSTFIELDS, [
            'to' => $emailAddress,
            'text' => $message->text(),
        ]);

        $result = curl_exec($curl);
        curl_close($curl);

        return $result;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法很简单，但是很混乱。这有点难以理解，所以我们添加了一些评论，以便读者了解正在发生的事情。然而，如果我们创建一个新的方法而不是一个注释呢？

```
<?php

namespace Email;

class Client
{
    public function send(string $emailAddress, Message $message): bool
    {
        if (!$this->validateParameters($emailAddress, $message)) {
            return false;
        }

        return $this->sendToMailServer([
            'to' => $emailAddress,
            'text' => $message->text(),
        ]);
    }

    private function validateParameters(string $emailAddress, Message $message): bool
    {
        $validateEmail = filter_var($emailAddress, FILTER_VALIDATE_EMAIL);
        $validateMessage = strlen($message->text()) < 1000;
        if (!$validateEmail || !$validateMessage) {
            return false;
        }

        return true;
    }

    private function sendToMailServer(array $params): bool
    {
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, 'https://mail.example.com');
        curl_setopt($curl, CURLOPT_POST, true);
        curl_setopt($curl, CURLOPT_POSTFIELDS, $params);

        $result = curl_exec($curl);
        curl_close($curl);

        return $result;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的方法现在干净多了。其他函数执行单个任务，每个任务不到 10 行代码，这使得它们易于阅读。

### 使用样式向导

最后，我建议为您选择的编程语言使用一个风格指南。对于 PHP，我喜欢在代码中使用 [PSR-2](https://www.php-fig.org/psr/psr-2/) 图标准。FIG 标准被广泛认可，并被 Laravel 和 Symfony 等流行的 PHP 框架所使用。此外，Symfony 团队已经发布了一个 CLI 实用程序，可以自动重新格式化您的代码，以遵循 PSR-2: [PHP 编码标准修复程序](https://cs.sensiolabs.org/)。

### 结论

设计代码需要努力和实践，但会收获很多回报。它使你的代码变得更小，更易读，并通过清晰和集中来减少错误。要继续学习更多，我建议阅读设计良好的代码。Laravel 和 T2 的 Ruby on Rails 代码库都是干净代码的例子。在这两方面都做一些源潜水，以了解更多！