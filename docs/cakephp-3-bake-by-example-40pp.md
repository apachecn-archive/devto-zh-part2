# CakePHP 3:示例烘焙

> 原文：<https://dev.to/northgoingzax/cakephp-3-bake-by-example-40pp>

本文面向 CakePHP 3 框架的新手。

我是 CakePHP 的粉丝，大约 9 个月前才开始接触它。任何框架都需要一些时间来适应，但我对 Cake 3 最大的问题是手册。我觉得它写得很有“你对蛋糕 2 很熟悉，所以我们不需要对这个做太多解释”的感觉。

然而，一旦你习惯了使用这个框架，这是一个非常有益的框架，幸运的是栈溢出上的 [IRC 通道](https://irc.cakephp.org/)和 [cakephp-3.x](https://stackoverflow.com/questions/tagged/cakephp-3.x) 标签被积极地维护着，所以你可以很快地解决它或者得到答案。

与其抱怨手册，我想我应该发布一个基于示例的代码生成特性指南，来帮助其他新手开发者。

这些例子非常简单，它们只是让你开始使用代码生成器，而不仅仅是当前在[文档](https://book.cakephp.org/3.0/en/bake/usage.html)中可用的基本命令列表。

#### *使用烘焙命令之前*

1.  创建一些表格，遵循 CakePHP [惯例](https://book.cakephp.org/3.0/en/intro/conventions.html#database-conventions)
2.  检查你在你的项目的根目录，如果你做了一个目录列表`ls`，你应该看到文件夹“bin”，“config”，“src”，“webroot”

* * *

#### 为名为`users`的表格创建一个控制器

```
bin/cake bake controller Users 
```

Enter fullscreen mode Exit fullscreen mode

> 创建 src/Controller/users Controller . PHP

#### 为一个名为`users`的表创建一个模型(和实体)

```
bin/cake bake model Users 
```

Enter fullscreen mode Exit fullscreen mode

> 创建 src/Model/Table/user stable . PHP
> 创建 src/Model/Entities/User.php

#### 为表格创建默认模板文件(添加/编辑/查看/索引)

```
bin/cake bake template Users 
```

Enter fullscreen mode Exit fullscreen mode

> 创建 src/Template/Users/index . CTP add . CTP edit . CTP view . CTP

#### 在 1 个命令中烘焙以上所有内容

```
bin/cake bake all Users 
```

Enter fullscreen mode Exit fullscreen mode

#### 只烘焙索引模板文件

```
bin/cake bake template Users index 
```

Enter fullscreen mode Exit fullscreen mode

> 创建 src/Template/user/index . CTP

#### 烘焙前缀文件夹(如 admin)

如果你正在为你的站点的管理部分烘焙，你将使用单独的控制器和模板文件

```
bin/cake bake controller Users --prefix admin 
```

Enter fullscreen mode Exit fullscreen mode

> 创建 src/Controller/Admin/users Controller . PHP

```
bin/cake bake template Users --prefix admin 
```

Enter fullscreen mode Exit fullscreen mode

> 创建 src/Template/Admin/Users/index . CTP edit . CTP add . CTP view . CTP

* * *

### 从不同的数据库中烘焙

前面的例子都使用在`app.php`中定义为`'default'`的 db 连接。如果您有一个处理客户记录的遗留数据库，例如`db_records`，您的[配置文件](https://book.cakephp.org/3.0/en/orm/database-basics.html#configuration)可能看起来像这样

```
// in config/app.php
'Datasources' => [
        'default' => [
            'host' => 'localhost',
            'username' => 'db_user',
            'password' => 'pass123',
            'database' => 'db_application',
        ],
        'records' => [
            'host' => 'localhost',
            'username' => 'db_records_user',
            'password' => 'pass123',
            'database' => 'db_records',
        ],
] 
```

Enter fullscreen mode Exit fullscreen mode

#### 在记录数据库中为名为`user_records`的表创建一个模型

```
bin/cake bake model UserRecords -c records 
```

Enter fullscreen mode Exit fullscreen mode

> 创建/src/Model/Table/user record Table . PHP
> 创建/src/Model/Entities/user record . PHP

这将包含在您的`UserRecordsTable.php`文件
中

```
public static function defaultConnectionName()
{
    return 'records';
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 从该数据库为不遵循蛋糕惯例的桌子创建模型，例如`tbl_records_user`

```
bin/cake bake model UserRecords -c records --table tbl_records_user 
```

Enter fullscreen mode Exit fullscreen mode

> 创建/src/Model/Table/user record Table . PHP
> 创建/src/Model/Entities/user record . PHP

这将添加`defaultConnectionName()`并设置

```
$this->setTable('tbl_records_user'); 
```

Enter fullscreen mode Exit fullscreen mode

您还可以在这里设置其他选项，但是有些选项更容易通过以后编辑文件来设置。但是，例如，您也可以将[显示字段](https://book.cakephp.org/3.0/en/orm/retrieving-data-and-resultsets.html#finding-key-value-pairs)设置为非默认值，例如电子邮件

```
bin/cake bake model UserRecords -c records --table tbl_records_user --display-field email 
```

Enter fullscreen mode Exit fullscreen mode

这将修改显示字段

```
$this->setDisplayField('email'); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 联想

默认情况下，`bake`命令会寻找关联。如果您使用的是遗留表或不同的数据源，任何以`_id`结尾的字段标题都可能导致“找不到基表或视图”错误。

为了避免这种情况，你可以不加联想地烘焙

```
bin/cake bake model UserRecords -c records --table tbl_records_user --no-associations 
```

Enter fullscreen mode Exit fullscreen mode

### 用插件烘焙

假设你已经安装了一个插件，比如[friends ofcake/BootstrapUI](https://github.com/FriendsOfCake/bootstrap-ui)，这样你的模板文件默认使用引导样式
，你现在可以添加`-t BootstrapUI`到上面的任何命令中

#### 使用插件创建模板文件

> 创建/src/Template/Users/index . CTP add . CTP edit . CTP view . CTP

```
bin/cake bake template Users -t BootstrapUI 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用遗留数据库创建整个 MVC 框架(控制器、表格、实体、模板)

```
bin/cake bake all UserRecords -c records --table tbl_records_user -t BootstrapUI 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 有用的特性

Bake 依赖于数据库连接，因此为了避免您加载并记住系统中的所有表，您可以在不指定表名的情况下调用命令来查看可用表的列表。假设您的数据库有 3 个表:
`users`
`articles`
`activity_logs`

使用命令`bin/cake bake model`将产生一个可用表格列表

```
bin/cake bake model
Choose a model to bake from the following:
Users
Articles
ActivityLogs 
```

Enter fullscreen mode Exit fullscreen mode

也是如此

```
bin/cake bake controller
bin/cake bake template 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果你是第一次使用 CakePHP，希望这是烘焙控制台的有用参考。