# 轻松地以查询的方式查询您的 SQL 数据库

> 原文：<https://dev.to/projectair/query-your-sql-database-in-the-cqured-way-with-ease-c02>

将 EntityModel.php 包含到您的 PHP 文件中，并使用名称空间 Lynq \ Entity \ EntityModel

你也可以将命名空间从 EntityModel.php 中移除，这样就可以了。
例子

```
<?php
use Lynq\Entity\EntityModel;

class PracticeModel
{
private $table = 'Media';

/**
* Connect to database in constructor
*/
public function __construct()
{
$dsn = 'mysql:dbname=airMediaDB;host=127.0.0.1';
$user = 'root';
$password = 'glory';
$this->mediaDB = new EntityModel($dsn, $user, $password);
}

/**
* Get ALL items via search $key
*/
public function getItems($key)
{
return $this->mediaDB->table($this->table)
->where('title', 'LIKE', '%'.$key.'%')
->orderBy('id')
->get();
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://gitlab.com/snippets/1728351](https://gitlab.com/snippets/1728351)