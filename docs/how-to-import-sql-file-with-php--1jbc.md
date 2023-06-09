# 如何用 PHP 导入 SQL 文件

> 原文：<https://dev.to/erhankilic/how-to-import-sql-file-with-php--1jbc>

这篇文章最初发表于[如何用 PHP 导入 SQL 文件](http://erhankilic.org/post/how-to-import-sql-file-with-php/)

有时，您不能使用 PhpMyAdmin 或任何数据库程序，您必须使用其他选项将 Sql 文件导入数据库。在这里，我将告诉你如何在 Php 中同时导入旧的 mysql_query 和新的 MySQL _ query。你也可以从我的 [Github 项目](https://github.com/erhankilic/importSqlWithPhp)中访问代码。

你需要对这两种方法做以下工作:在同一个地方上传 Sql 文件和 php 文件，通过站点地址运行 php 文件。您也可以通过服务器运行控制台命令。

用 sql 的名称更新 **$filename** 。用数据库服务器更新 **$mysql_host** 变量。如果数据库服务器没有不同，您可以让它保持原样。 **$mysql_username** 和 **$mysql_password** 是您的数据库的用户名和密码。 **$mysql_database** 是数据库名，用你的数据库名更新它。

## 带有 mysql_query 功能

```
<?php

// Name of the file
$filename = 'sql.sql';
// MySQL host
$mysql_host = 'localhost';
// MySQL username
$mysql_username = 'username';
// MySQL password
$mysql_password = 'password';
// Database name
$mysql_database = 'database';

// Connect to MySQL server
mysql_connect($mysql_host, $mysql_username, $mysql_password) or die('Error connecting to MySQL server: ' . mysql_error());
// Select database
mysql_select_db($mysql_database) or die('Error selecting MySQL database: ' . mysql_error());

// Temporary variable, used to store current query
$templine = '';
// Read in entire file
$lines = file($filename);
// Loop through each line
foreach ($lines as $line) {
// Skip it if it's a comment
    if (substr($line, 0, 2) == '--' || $line == '')
        continue;

// Add this line to the current segment
    $templine .= $line;
// If it has a semicolon at the end, it's the end of the query
    if (substr(trim($line), -1, 1) == ';') {
        // Perform the query
        mysql_query($templine) or print('Error performing query \'<strong>' . $templine . '\': ' . mysql_error() . '<br /><br />');
        // Reset temp variable to empty
        $templine = '';
    }
}
echo "Tables imported successfully"; 
```

Enter fullscreen mode Exit fullscreen mode

## 用 mysqli_query 类

```
<?php

// Name of the file
$filename = 'sql.sql';
// MySQL host
$mysql_host = 'localhost';
// MySQL username
$mysql_username = 'username';
// MySQL password
$mysql_password = 'password';
// Database name
$mysql_database = 'database';

// Connect to MySQL server
$con = @new mysqli($mysql_host,$mysql_username,$mysql_password,$mysql_database);

// Check connection
if ($con->connect_errno) {
    echo "Failed to connect to MySQL: " . $con->connect_errno;
    echo "<br/>Error: " . $con->connect_error;
}

// Temporary variable, used to store current query
$templine = '';
// Read in entire file
$lines = file($filename);
// Loop through each line
foreach ($lines as $line) {
// Skip it if it's a comment
    if (substr($line, 0, 2) == '--' || $line == '')
        continue;

// Add this line to the current segment
    $templine .= $line;
// If it has a semicolon at the end, it's the end of the query
    if (substr(trim($line), -1, 1) == ';') {
        // Perform the query
        $con->query($templine) or print('Error performing query \'<strong>' . $templine . '\': ' . $con->error() . '<br /><br />');
        // Reset temp variable to empty
        $templine = '';
    }
}
echo "Tables imported successfully";
$con->close($con); 
```

Enter fullscreen mode Exit fullscreen mode