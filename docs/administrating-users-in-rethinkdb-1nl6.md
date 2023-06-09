# 在 RethinkDB 中管理用户

> 原文：<https://dev.to/ulisesac4/administrating-users-in-rethinkdb-1nl6>

## 简介

在管理数据库时，需要配置认证机制，这种机制可以扩展到授权机制。RethinkDB 有一个简单明了的用户机制来控制用户创建、权限授予和操作范围。

在本教程中，您将对三个不同的用户应用几种权限配置。

## Requeriments

*   让一台服务器运行 RethinkDB。

## 设置事物

这项管理任务是通过 web 面板完成的。因此，在 ssh 登录到您的 dopplet 后，运行`rethinkdb`或`rethinkdb --bind all`，这取决于您是否正在进行隧道操作，因此您可以通过`domain_name:8080`或`dopplet_url:8080`访问您的面板。一切都在导航栏的`Data Explorer`部分完成。

通过 web panel 访问 RethinkDB 时，RethinkDB 使您成为`admin`用户，因此您可以完全控制节点。

### 添加用户

为了避免例子之间的混淆，我们将有几个用户，每个例子一个。用户存储在数据库`rethinkdb`的表`users`中。要创建它们，你需要使用`insert`函数传递一个 JSON 对象，要完成这一步，你需要传递一个由 3 个成员组成的 JSON 数组，如下所示:

```
r.db('rethinkdb').table('users').insert([
    {id: 'john', password: 'pass1'},
    {id: 'jack', password: 'pass2'},
    {id: 'joe', password: 'pass3'},
    ]) 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，你将得到这样一个对象作为响应:

```
{
    "deleted": 0 ,
    "errors": 0 ,
    "inserted": 3 ,
    "replaced": 0 ,
    "skipped": 0 ,
    "unchanged": 0
} 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，一旦创建，您就不能修改用户的`id`,您必须删除整个用户，以便您可以使用所需的名称再次添加它。

### 添加表格

为了封装测试，您将两个表添加到`test`数据库中，`CompactCameras`和`DslrCameras`，您将执行两次 create 方法:

```
r.db('test').tableCreate('CompactCameras')
r.db('test').tableCreate('DslrCameras') 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，您将得到一个中等大小的 JSON 对象作为响应，告诉您每个表的详细信息。

有了这些，你就可以开始看例子了。

## 例 1

在这个例子中，`john`在`grant`方法:
的帮助下从表`CompactCameras`中读取和写入

```
r.db('test').table('CompactCameras').grant('john', {read: true, write: true}); 
```

Enter fullscreen mode Exit fullscreen mode

执行该命令后，您将得到如下答案:

```
"granted": 1 ,
"permissions_changes": [
    {
        "new_val": {
            "read": true ,
            "write": true
        } ,
        "old_val": null
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

权限已经发生了变化，这很说明问题。它还报告`old_val`和`new_val`之间的配置状态。

为这个用户配置权限已经没有什么可做的了，您可以在您的应用程序的认证中使用这个用户，一切都将正常工作。现在你可以回顾第二个例子。

## 例 2

在本例中，`jack`从表`CompactCameras`中读取和写入数据，从`DslrCameras`中读取数据，看到最后一个例子，您可以推断出您必须执行两次命令，每次都针对要修改的表:

```
r.db('test').table('CompactCameras').grant('jack', {read: true, write: true});
r.db('test').table('DslrCameras').grant('jack', {read: true}); 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一个来自示例 1 的响应，告诉您已经完成的更改。本例中重要的一点是，一个用户可以同时拥有不同元素的活动权限。

## 例 3

在本例中，`joe`可以访问除`DslrCameras`之外的`test`数据库的所有配置，同样，您必须单独执行每个命令:

```
r.db('test').grant('joe', {read: true, write: true});
r.db('test').table('DslrCameras').grant('joe', {read: false, write: false}); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中首先要注意的是，`grant`命令可以应用于结构的任何级别，其中级别是:`r`作为所有数据库，`r.db`作为一个数据库，`r.db.table`作为一个数据库中的一个表。

要注意的第二件事是，较大范围内的权限会被较小范围继承，使用示例 3 会导致说`joe`可以读写`test`数据库中的任何元素，直到在表`DslrCameras`中指定他不能这样做。还有一个例子。

## 例 4

任何全局权限都必须直接写入到`r`中，比如让`joe`通过以下命令完全访问服务器:

```
r.grant('joe', {read: true, write: true, config: true, connect: true}); 
```

Enter fullscreen mode Exit fullscreen mode

最后要注意的是`connect`选项只适用于全局范围。

## 结论

当添加一个用户时，它没有任何权限，但是您可以用`grant`方法设置值，在此之后，未设置的权限被认为是假的。RethinkDB 的简单性不允许我们像本教程中创建的任何用户一样登录到管理面板，然后下一步是创建一个连接到您的服务器的应用程序，以便您可以测试您的用户权限。你可以去官方的 RethinkDB 看看你选择的编程语言的驱动程序。