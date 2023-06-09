# 按照面向对象的方法创建数据库连接

> 原文：<https://dev.to/_mertsimsek/interfaces-and-constants-with-database-connection-1i9e>

在本帖中，我们将学习 PHP 中一个漂亮的数据库连接。在这种情况下，我们必须服从 OOP。首先，我将在项目目录中有一个名为`IconnectionInfo.php`的接口文件。如下图。但是我们不能在接口中包含变量，你可以包含常量。例如，下面的接口具有在 MySQL 连接中使用的常量:

```
<?php

interface IConnectInfo
{
    const HOST ="localhost";
    const UNAME ="root";
    const DBNAME = "symf0ny";
    const PW = "sem";
    function getConnection();
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

像任何其他 PHP 接口一样实现该接口。然后可以在实现中使用范围解析操作符传递常量的值:

```
<?php

include_once('IConnectInfo.php');

class Database implements IConnectInfo
{

    private static $instance = null;
    private $conn;

    private $server = IConnectInfo::HOST;
    private $currentDB = IConnectInfo::DBNAME;
    private $user = IConnectInfo::UNAME;
    private $pass = IConnectInfo::PW;

    private function __construct()
    {
        try {
            $this->conn = new PDO("mysql:host=$this->server;dbname=$this->currentDB", $this->user, $this->pass
            );
            $this->conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
            $this->conn->exec('set names utf8');
            echo $this->server . " connected successfully" . PHP_EOL;
        } catch (PDOException $e) {
            echo "Connection failed: " . $e->getMessage();
            die;
        }
    }

    public static function getInstance()
    {
        if (!self::$instance) {
            self::$instance = new Database();
        }

        return self::$instance;
    }

    public function getConnection()
    {
        return $this->conn;
    }

    public function getSelectQueryResult($query = '')
    {
        try {
            $query = $this->conn->prepare($query);
            $query->execute();
            return $query->fetchAll(PDO::FETCH_ASSOC);
        } catch (PDOException $e) {
            echo $query . "<br>" . $e->getMessage();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一的方法是 getConnection()，但是如果您愿意，您的接口可以只由常量组成。使用接口名称(IConnectInfo)、范围解析操作符和常量名称将值传递给类属性(本例中为私有变量)。

我们只需要初始化文件。它被命名为`app.php`。下面的代码就是这个意思。

```
<?php

require __DIR__ . '/class/Database/Database.php';

$dbInstance = Database::getInstance();
$dbConnection = $dbInstance->getConnection(); 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，我们可以连接数据库了。

[![alt text](img/0a801d8f87785ab924e326c9fcc34713.png "Pretty Database Connection")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ls7ofXYr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g9j7yquzlorxdq0bh25c.png)

太酷了！我的目标正是用 OOP 和数据库连接来启发我们的思维。我希望，我能做到。在这篇文章中，就说到这里。我们已经学习了用接口和常量连接任何数据库连接。