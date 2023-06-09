# 构建部署就绪的 PHP 应用程序

> 原文：<https://dev.to/sadick/building-deploy-ready-php-apps-1kfm>

部署就绪是什么意思？
这意味着你的应用程序应该能够部署在任何环境中，而不需要改变应用程序代码。

为了确保您的应用程序已准备好部署，在部署之间可能发生变化的内容(试运行、alpha 或生产)不应该硬编码到您的应用程序中。

我知道您在尝试建立数据库连接时遇到过甚至编写过类似下面这样的代码。

```
class Database
{
    public function connect($host, $user, $password)
    {
        $pdo = new PDO("mysql:host=$host;dbname=myDB", $user, $password);
        return $pdo;
    }
}

$db = new Database();
$db->connect("localhost", "root", "password"); 
```

Enter fullscreen mode Exit fullscreen mode

上述方法有两个问题:

*   您正在硬编码并公开您的数据库登录凭证，这是不安全的
*   当您将应用程序部署到其他环境时，您必须编辑连接代码，以便连接不会失败(除非该环境有确切的配置)

为了补救上面的代码，您需要一种方法来提供凭证，而不需要对它们进行硬编码。这可以通过使用环境变量来实现。
一个[环境变量](https://en.wikipedia.org/wiki/Environment_variable)是一个动态命名的值，它可以影响正在运行的进程在计算机上的行为方式。在你的例子中，动态值是`database username, address, password`

**设置环境变量**

如果您拥有将要部署到的服务器，您可以如下设置环境变量。

**在 windows 服务器上**

```
setx DB_NAME test
setx DB_HOST localhost
setx DB_PASSWORD password 
```

Enter fullscreen mode Exit fullscreen mode

**在 ubuntu 服务器上**

*   打开终端(通过按 CtrlAltT)
*   sudo -H gedit /etc/environment
*   键入您的密码
*   编辑刚打开的文本文件:

```
DB_NAME=test DB_HOST=localhost
DB_PASSWORD=password 
```

Enter fullscreen mode Exit fullscreen mode

*   保存，然后注销并重新登录以使更改生效。

设置了环境变量后，您现在可以更改代码来使用这些变量，而不是硬编码。

```
$env = getenv();

$user = $env['DB_USER'];
$password = $env['DB_PASSWORD'];
$host = $env['HOST'];

$db = new Database();
$db->connect($host, $user, $password); 
```

Enter fullscreen mode Exit fullscreen mode

对于那些使用 Apache 共享服务器的人。根文件夹中需要有一个`.htaccess`文件。正常情况下`public_html`。
要设置环境变量，将以下内容添加到文件

```
SetEnv DB_USER your_production_db_user
SetEnv DB_PASSWORD your_production_db_password
SetEnv DB_NAME your_prodution_db_name 
```

Enter fullscreen mode Exit fullscreen mode

这会将变量设置到服务器。这有一个缺点，因为`getenv()`只获得系统环境变量。为了解决这个问题，你可以将`getenv()`和`$_SERVER`合并到一个数组中。

```
$env = array_merge(getenv(), $_SERVER); 
```

Enter fullscreen mode Exit fullscreen mode

我尽可能地避免在我的开发机器上设置环境变量，因为我的机器上有太多的事情要做。我通常做的是在没有找到环境变量的情况下使用默认值。

```
$user = $env['DB_USER'] ?? 'root';
$password = $env['DB_PASSWORD'] ?? 'password';
$host = $env['HOST'] ?? 'locahost'; 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经完成了，部署变得非常容易，因为您不需要更改任何代码就可以连接到数据库。并且您也没有公开您的数据库凭证。