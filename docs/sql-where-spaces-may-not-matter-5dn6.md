# SQL:空格可能不重要

> 原文：<https://dev.to/antogarand/sql-where-spaces-may-not-matter-5dn6>

# 简介

下面是一个主动易受攻击的应用程序的源代码:

[https://git lab . com/AntonyGarand/pwn _ fix _ repeat _ size _ does _ matter](https://gitlab.com/AntonyGarand/pwn_fix_repeat_size_does_matter)

它将很快作为一个挑战被添加到 [pwnfixrepe.at](https://pwnfixrepe.at/challenges.html) 网站上，在那里你可以解决这样的安全问题。

如果你接受的话，你的任务是以管理员身份登录应用程序。

如果您想跳到细节，请前往`solution`部分。

* * *

# 申请

应用程序本身非常简单。

有登录、注销、注册三个功能。

表格看起来是这样的:([来源](https://gitlab.com/AntonyGarand/pwn_fix_repeat_size_does_matter/blob/master/setup.sql) )

```
CREATE TABLE `users` (
  `id` int(11) PRIMARY KEY,
  `username` varchar(20) NOT NULL,
  `password` varchar(255) NOT NULL,
  `description` varchar(255) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

INSERT INTO `users` (`id`, `username`, `password`, `description`) VALUES (1, 'admin', '$up3rS3cr3tP4$$w0rd!', 'You are admin!'); 
```

Enter fullscreen mode Exit fullscreen mode

所有这些功能都使用预准备语句，不易受到 SQL 注入攻击，例如使用以下登录功能:( [Source](https://gitlab.com/AntonyGarand/pwn_fix_repeat_size_does_matter/blob/master/functions.php) )

```
function login() {
    global $db;
    if ( /* user or pass empty or not string */ ) {
        return false;
    }
    $query = $db->prepare('SELECT * FROM users WHERE username = :username and password = :password');
    $query->bindParam(':username', $_POST['user']);
    $query->bindParam(':password', $_POST['pass']);
    $query->execute();
    $result = $query->fetch();
    if ($result) {
        $_SESSION['username'] = $result['username'];
        echo 'Logged in succesfully!';
    } else {
        echo 'Invalid credentials!';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果当前用户已经登录，我们会显示一条问候消息:

```
/* if current user is logged in */ 
{ ?>
    <div>
        Welcome back, <?= htmlspecialchars($currentUser['username']) ?>!<br/>
        <?= $currentUser['description'] ?>
    </div>
    <!-- ... -->
    <?php
} 
```

Enter fullscreen mode Exit fullscreen mode

# 分析

该漏洞不在 PHP 内部，而是由 SQL 本身以及易受攻击的应用程序的许多错误设计决策造成的。

下面是对用户表的一些观察:

*   用户名不是唯一的
*   用户名是长度为 20 的 varchar

以及应用程序本身:

*   登录的用户基于用户名

```
// If login success: 
$_SESSION['username'] = $result['username']; 
```

Enter fullscreen mode Exit fullscreen mode

*   如果用户名没有被占用，您只能注册一个用户

```
if (selectUser($_POST['user'])) {
    echo 'Username already taken!';
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 解

比较 SQL 中的字符串很有趣。

你认为下面的陈述会是什么？

```
SELECT * FROM (select 1) as t WHERE 'x' = 'x ' 
```

Enter fullscreen mode Exit fullscreen mode

与许多人的预期不同，`'x' = 'x '`条件是`true`！

在 SQL 中，比较两个字符串时，最短的一个用空格填充，直到它们长度相等。
这实际上意味着尾随空格在常规字符串比较中没有用！

这是在 [SQL 规范](http://www.contrib.andrew.cmu.edu/~shadow/sql/sql1992.txt)的比较运算符中定义的:

> a)如果 X 的字符长度不等于 Y 的字符长度，则为了比较的目的，较短的字符串被有效地替换为其自身的副本，该副本通过在一个或多个填充字符的右边连接而被扩展到较长字符串的长度，其中填充字符是基于 CS 选择的。如果 CS 具有 NO PAD 属性，那么 PAD 字符是一个依赖于实现的字符，它不同于 X 和 Y 字符集中的任何字符，它比 CS 下的任何字符串排序都少。否则，填充字符是一个`<space>`。

成功开发应用程序的关键就在于这种特殊性。

我们需要做的第一件事是创建一个新用户，与“admin”同名。

我们可以通过输入一个以`admin`开头的用户名，用空格填充直到列大小为`20`，以及一个非空格字符来实现。

这将通过`username exists`条件，因为给定的用户名将不存在。

插入新用户时，由于该列有`20`字符限制，该值将被截断为`20`字符，因此为`admin`，并带有尾随空格。
注意，如果列是唯一的，这是不可能的，因为我们会得到一个重复的条目消息。

第二步是登录，使用`admin`用户名和我们创建的用户的密码。

由于 sql 表现在有两个用户，普通管理员和我们的假管理员，登录查询将工作并选择我们的新用户。

最后，当应用程序根据用户名找到当前登录的用户时，它选择第一个用户名为`admin`的用户。这意味着它将选择管理员用户，而不是我们自己的，我们现在可以窃取管理机密！

# 结论

安全问题只是应用程序中的错误。

像常规的 bug 一样，你可以通过维护高质量的代码来限制它们的数量。
在这个应用程序中，将`username`列标记为唯一的，使用 id 来标识当前用户或预先执行长度验证本来可以避免这个问题。

# 参考文献

*   [https://git lab . com/Antony garand/pwn _ fix _ repeat _ size _ does _ matter](https://gitlab.com/AntonyGarand/pwn_fix_repeat_size_does_matter)
*   [https://stack overflow . com/questions/4166159/SQL-where-clause-matching-values-with-trailing-spaces](https://stackoverflow.com/questions/4166159/sql-where-clause-matching-values-with-trailing-spaces)
*   [http://www.contrib.andrew.cmu.edu/~shadow/sql/sql1992.txt](http://www.contrib.andrew.cmu.edu/%7Eshadow/sql/sql1992.txt)