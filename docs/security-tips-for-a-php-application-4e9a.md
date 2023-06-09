# PHP 应用程序的安全提示

> 原文：<https://dev.to/restoreddev/security-tips-for-a-php-application-4e9a>

我知道你们很多人在想什么！PHP 与安全？何必呢？就用另一种编程语言吧！

与流行的观点相反，PHP 仍然是构建 web 应用程序的可行语言。PHP 只是你需要学会正确使用的另一个工具。让我们讨论几个 PHP 特有的项目，它们会使你的站点更加安全。

### 1。将您的 Web 根目录限制为 index.php 和资产文件

在现代 PHP 中，您将把 web 服务器指向一个目录，其中有一个`index.php`文件，这将是整个站点的单一入口点。`index.php`将加载 Composer 包并引导您的私有代码来生成响应。将其他 PHP 文件放在 web 根目录下进行测试是很诱人的。但是，您不希望这些文件被意外提交并暴露您的应用程序中的问题。此外，任何配置文件都不应该放在 web 根目录中，因为 web 服务器可以公开传递它们。唯一应该在你的 web 根目录中的静态文件是 JavaScript、CSS 和图像文件。

### 2。关闭显示错误

您可以用一个名为`display_errors`的`php.ini`指令设置 PHP 来显示执行过程中发生的错误。显示的错误可能会泄露有关您的应用程序的重要信息，包括机密和 SQL 查询。在生产中关闭它们是很重要的。在你的`php.ini`文件中，设置`display_errors = Off`和`display_startup_errors = Off`。为了仍然捕获调试错误，打开`log_errors = 1`和`error_log = /var/log/php-error.log`。您可以将错误日志设置为任何文件路径，甚至可以将其设置为操作系统级别的日志服务。

### 3。使用 password_hash 存储您的密码

众所周知，不要以纯文本格式存储密码。然而，许多框架仍然使用不安全的哈希算法来转换密码(我看着你 WordPress)。你可能会看到一些旧文章推荐在 PHP 中使用`md5`或`hash`函数来散列密码。不要使用它们！在 PHP 中散列函数的最好方法是使用`password_hash`函数。可以这样用:

```
<?php

$hash = password_hash('iamapassword', PASSWORD_DEFAULT);
if (password_verify('iamapassword', $hash)) {
    echo "The hash matches!\n";
} 
```

Enter fullscreen mode Exit fullscreen mode

密码散列函数采用纯文本密码和一种算法类型(`PASSWORD_DEFAULT`是一个安全的选择)。该函数返回散列密码，您可以在用户登录时在`password_verify`函数中使用该密码。根据哈希是否与提交的密码匹配，密码验证返回 true 或 false。如果你看到一篇推荐给你的密码加盐的文章，不要担心`password_hash`会加盐。该功能会自动为您存储密码。

### 4。加密所有通信

在您的 PHP 应用程序中，您经常需要与其他服务或存储机制进行通信。务必始终使用加密的服务连接。当使用`curl`或`soap`时，总是使用 API 的`https`URL 来验证传输中的数据是加密的。此外，如果你使用 FTP，确保你使用某种形式的安全 FTP，无论是 FTPS 或 SFTP。如果您不使用任何类型的安全传输，那么您可能会在网络活动中泄露用户数据。使用 TLS 还可以验证 URL 是否如其所声称的那样，从而防止您将数据发送给不良行为者。

### 5。使用模板框架

PHP 本身经常被用作 HTML 文件中嵌入的模板语言。但是，PHP 在默认情况下不会对输出进行转义。转义可以防止坏人在用户控制的表单或页面中嵌入恶意的 JavaScript。考虑使用自定义模板解决方案，如 [Twig](https://twig.symfony.com/) 。Twig 使用它的模板语法自动转义所有输出，防止您忘记转义用户数据。

创建一个安全的网站，除了这些技巧还有很多，但希望它们有助于提高代码库的整体安全性。我在[一个更安全的网站的四个技巧](https://dev.to/restoreddev/four-tips-for-a-more-secure-website-52ip)中写过其他方法来保护你的网站。如果您想了解更多信息，请查看！