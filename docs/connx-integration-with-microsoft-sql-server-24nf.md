# CONNX 与 Microsoft SQL server 的集成

> 原文：<https://dev.to/techcommunity/connx-integration-with-microsoft-sql-server-24nf>

| 2018 年第二期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue2/SAG_CONNX_Integration_With_SQL_TECHniques_Apr18_WEB.pdf) |
| --- | --- |

## SQL Server 链接的服务器

SQL Server 的链接服务器功能使 SQL Server 数据和非 SQL Server 数据能够直接在 SQL Server 引擎中快速轻松地集成。它有许多技术特点。

在 SQL Server 文档或知识库文章的不同位置对每一个都有单独的描述。本指南试图提供这些项目的背景。此外，我们还描述了某些选项存在的原因及其衍生结果。

## 建立链接服务器连接

建立链接服务器连接需要几个要素:

1.  SQL Server
2.  用于数据库与 SQL Server 集成的第三方驱动程序 OLE DB 或 ODBC，如 CONNX

使用 CONNX，SQL Server 可以轻松地与所有支持的数据源集成，包括 Adabas。可以通过两种方式创建链接服务器:通过 SQL Server Management Studio 或者通过名为 sp_addlinkedserver 的存储过程。如果需要通过脚本动态创建链接服务器，那么存储过程路线更好。如果需要，请参考有关此存储过程的现有详细 SQL Server 文档。使用 SQL Server Management Studio，可以在“安全对象”下找到链接服务器。通过右键单击“链接服务器”来添加新的链接服务器然后选择“新建链接服务器…”

[![](img/7858ca7deab8eab7ad4149ce9e1462f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_b_1GOyP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx1.jpg/58f46e4e-0ed9-4e10-bf3f-b69978b4e20e%3Ft%3D1524042656832)

*图 1:从 Management Studio 创建一个新的链接服务器*

选择链接服务器连接的名称后，必须决定使用哪种类型的驱动程序来访问非 SQL Server 数据库。

微软正在放弃 OLE DB，并再次在 ODBC 上标准化以访问外部数据源。选择“Microsoft OLE DB Provider for ODBC Driver”

选择驱动因素后，填写剩余的字段。“产品名称”字段不用于任何技术目的。但这是完成链接服务器连接所必需的。请参考第三方驱动程序的文档，了解为其余字段提供什么的详细信息。在 provider string 字段中，指定 CONNX 驱动程序、CDD 的名称以及任何所需的应用程序过滤器。

[![](img/85f870aef4a028e6e29ec89f87e8f7c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a_GcFG33--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx2.jpg/5e9c451d-d2b3-44b9-b330-e712b93cc76e%3Ft%3D1524042662764)

*图 2:指定链接服务器驱动程序连接属性*

先不要按“确定”按钮。重要的身份验证和安全选项位于“安全”页面。这可以通过单击对话框左上角的安全来选择。有些数据库不需要用户名和密码就可以连接。在这些情况下，请选择“不使用安全上下文进行制作”选项然而，大多数数据库需要用户名和密码。这使得通过 SQL server 连接到它们的过程有点困难。一个典型的例子是:SQL Server 被配置为使用 Microsoft Active Directory 登录。但是另一个数据库不支持这种类型的登录。如果所需的数据源不支持 active directory 身份验证，身份验证凭据不匹配将导致无法连接。以下对话框提供了问题的解决方案。这里，另一个数据库的一组静态凭据将用于所有链接服务器连接。选择“使用此安全上下文”然后为另一个数据库提供适当的用户名和密码。

[![](img/f8e8daab337cf9836b9539a81f72d0f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e3ivoZBv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx3.jpg/abb81ead-b2d0-4173-bb45-bcf8774cff96%3Ft%3D1524042667084)

*图 3:指定链接服务器连接的认证方法*

如果可能，不要对所有链接服务器连接使用一组身份验证凭据。相反，通过按下安全页面上的“添加”按钮，为每次 SQL Server 登录建立一个单独的映射。“模拟”选项只是对另一个数据库使用 Active Directory 身份验证的另一种说法，不要选中此复选框。点击“新链接服务器”对话框左上角的“服务器选项”可以找到其他重要选项。

[![](img/b6b84fdc1b76fc5d6064f27a06428a68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RT06cBBu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx4.jpg/8f17e94d-73ef-4af9-a867-a846082f30a3%3Ft%3D1524042671605) 

*图 4:指定链接服务器“服务器选项”*

下表列出了每个选项及其功能:

|  |  |
| --- | --- |
| 排序规则兼容 | 如果另一个数据库的代码页和排序顺序与 SQL Server 相同，请启用此选项以获得更好的性能。默认情况下，此选项不启用。这意味着 SQL Server 将对第三方驱动程序返回的所有数据进行重新排序。应该打开该选项。 |
| 数据存取 | 启用或禁用第三方驱动程序的查询。显然，这需要打开。 |
| RPC & RPC OUT | 允许使用存储在另一个数据库中的存储过程。应该打开该选项。 |
| 使用远程排序规则 | 如果另一个数据库中的列排序规则不同于 SQL Server 上的默认排序规则，SQL Server 将遵循链接服务器指定的列排序规则，而不是在启用此选项的情况下使用默认 SQL Server 排序规则对数据进行重新排序。 |
| 排序规则名称 | 如果其他数据库不同于 SQL Server，则可以在此处指定该数据库的排序规则。 |
| 连接超时和查询超时 | 超时选项 |
| 分销商、发行商、订户 | 这些选项通常用于 SQL server 到 SQL Server 或 Oracle 到 SQL Server 的复制。 |
| 惰性模式验证 | 如果设置为 true:当设置为 false 时，模式验证发生在执行时而不是准备时。对于大多数第三方驱动程序，这不会影响性能。 |
| 为 RPC 启用分布式事务的升级 | 启用后，当通过第三方驱动程序执行存储过程时，SQL Server 将自动启动分布式事务。 |

这里重要的选项是“排序规则兼容”和“数据访问”“排序规则兼容”提高了性能。应该启用它。链接服务器必须启用“数据访问”才能处理查询。

一旦为新链接服务器设置了所有选项，请按“确定”创建链接服务器。若要验证与链接服务器的连接，请在 SQL Server Management Studio 中双击链接服务器名称。然后，展开目录，直到可以看到表。

## 访问您的非 SQL server 数据

使用 SQL Server 的 CONNX 访问数据有四种方式:两种方式使用链接服务器，两种方式不使用。

[![](img/b5b69b1b3d7084130f9ac2bcf3664a3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2epERWJd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx5.jpg/9db5345a-79b0-432d-94c7-6933c079f355%3Ft%3D1524042676040) 

*图 5:通过链接服务器连接可用的表格的树形视图*

使用 OPENDATASOURCE 和 OPENROWSET 可以跳过创建链接服务器的整个过程。这两个选项都支持通过 SQL 直接访问 CONNX 驱动程序，而无需事先创建任何链接服务器。使用这两种方法时，有许多驱动程序配置选项不可用于兼容配置，如排序规则。

此外，这两种即席查询方法在 SQL Server 中默认都是禁用的。要启用这些特别方法，请执行以下命令:

```
sp\_configure 'show advanced options', 1; RECONFIGURE; Go sp\_configure 'Ad Hoc Distributed Queries', 1; RECONFIGURE; Go 
```

启用此选项的潜在危险是，现在任何用户都可以从 SQL Server 上的任何 OLE DB 访问接口或 ODBC 驱动程序中访问数据…而不仅仅是那些被明确定义为链接服务器的访问接口或驱动程序。但是，该选项是可用的，由每个管理员根据项目和安全需求来确定这些功能的风险。

下面是一个使用 OPENDATASOURCE 的例子:

```
SELECT \* FROM OPENDATASOURCE('MSDASQL', 'driver=connx32;DD=c:\cdd\demo4.cdd;application =adabas;UID=xxxxx;PWD=xxxxx') .adalocal.connx75.employees 
```

[![](img/0d0a353975f2ab243a6a70b5ccbafc94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7M8BepUS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx6.jpg/14ea51d7-17f8-4a8b-830b-65e661c5407d%3Ft%3D1524042680690) 

*图 OpenDatasource 语句示例*

以及相应的 OPENROWSET 使用示例:

```
SELECT a.\* FROM OPENROWSET('MSDASQL', 'driver=connx32;DD=c:\cdd\demo4.cdd;APPLICATION= ADABAS;UID=XXX;PWD=XXX;', 'SELECT e.first\_name, e.name, en.LANG from adalocal..employees e inner join adalocal..employees\_lang en on (e.isn = en.isn) where e.city = ''PARIS'' ORDER BY e.name DESC') AS a; 
```

[![](img/80036ea9028a2533bf34563355f9e66c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bg56IdUS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx7.jpg/d2258d21-a044-4466-a28d-417c62e29e2c%3Ft%3D1524042685154) 

*图 OpenRowset 语句示例*

## 通过链接的服务器对象访问数据

其余两种通过第三方驱动程序查询数据的方法涉及到使用链接的服务器对象。第一种方法包括使用由四部分组成的名称，语法如下:

```
\<Linked Server Name\>.\< Object Catalog\>.\< Object Owner\>.\<Object Name\> 
```

如果您的第三方驱动程序不支持目录或所有者名称，请将这些部分留空。例如，如果一个提供者不支持目录名，但支持所有者名，则语法如下:

```
Select \* from MyLinkedServer..richard.products 
```

对于一个既不支持目录名也不支持所有者名的驱动程序，它应该是这样的:

```
Select \* from MyLinkedServer…products 
```

对于一个支持目录名和所有者名的完全兼容的驱动程序，比如 CONNX，应该是这样的:

```
Select \* from MyLinkedServer.oregon.richard.products 
```

由四部分组成的名称语法的优点是它最容易编写，也最简洁。用户可以利用他们现有的 SQL Server 语法知识，轻松地在一条 SQL 语句中混合 SQL Server 和非 SQL Server 表。使用四部分名称语法的巨大缺点是性能。当对链接服务器连接中的多个表执行任何类型的复杂 SQL 操作时，SQL server 通常会将每个表的快照下载到 SQL Server 中。然后，它对这些快照执行筛选和连接操作。对于大型表来说，这是一个性能瓶颈。避免这种情况的方法是使用数据访问的第四个也是最后一个选项 OPENQUERY 语法。

使用 OPENQUERY 语法，可以将较大 SQL 语句的复杂子组件直接发送给第三方驱动程序。这提高了性能。这里显示了一个语法示例:

```
SELECT a.\* FROM OPENQUERY(ADABAS, 'SELECT e.first\_name, e.name, en.LANG from adalocal..employees e inner join adalocal..employees\_lang en on (e.isn = en.isn) where e.city = ''PARIS'' ORDER BY e.name DESC ') AS a; 
```

[![](img/d67806248cc3ed4d8465d7abe7306517.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m8h5YJY6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx8.jpg/b7723b4a-a241-431d-8042-8dd90ed75469%3Ft%3D1524042688992) 

*图 OpenQuery 语句示例*

或者，可以使用 exec 语法，提供指定参数的能力:

```
exec ('SELECT e.first\_name, e.name, en.LANG from adalocal..employees e inner join adalocal..employees\_lang en on (e.isn = en.isn) where e.city = ? ORDER BY e.name DESC ', 'PARIS') at ADABAS; 
```

## 全局 OLE DB 提供程序选项

每个 OLE DB 访问接口都有八个全局选项，可以为该访问接口的所有链接服务器进行配置。

[![](img/5cefc91736b95cec8b58bcaa28c54a80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--znvnGPqr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx9.jpg/783b2a0f-7aa3-4ae2-b9d3-b8175e350325%3Ft%3D1524042692857) 

*图 9:访问 OLE DB 提供程序的全局属性*

选择 MSDASQL，因为这是 ODBC 驱动程序的 OLEDB 提供程序的简称。

[![](img/73a12df99312e63c1ee499bd65693c65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Lh4OhMo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/connx10.jpg/c9206311-611f-4a84-b822-4114126b82b8%3Ft%3D1524042697227) 

*图 10:OLE DB 提供程序的全局属性*

|  |  |
| --- | --- |
| 动态参数 | 允许 SQL server 向 OLEDB 访问接口发送参数化查询。它可以带来更好的性能。应该选择这个选项。 |
| 嵌套查询 | 允许 SQL Server 发送“嵌套”查询。表名实际上是一个子选择，将查询处理交给了第三方提供者。许多第三方提供程序不支持嵌套查询。因此，通常情况下，该选项是关闭的。 |
| 仅零级 | 零级是指“基本”OLE DB 查询接口。大多数 OLE DB 提供程序只实现这些基本的查询接口。不应选择此选项。 |
| 允许进行中 | 该选项在很大程度上影响内存使用和性能。“进程内”意味着 OLE DB 访问接口将在与 SQL Server 引擎相同的进程中加载。过程中的优势是性能。缺点是 SQL Server 和 OLE DB 访问接口争用同一个进程虚拟内存。SQL Server 通常为自己保留除少量内存之外的所有内存。如果 OLE DB 访问接口内存不足，您可能需要添加或调整"-g" SQL Server 命令行选项。"-g" memory_to_reserve 选项以兆字节为单位表示 SQL Server 将留出多少内存供第三方驱动程序和组件使用。默认金额为 2.56 亿。根据链接服务器的使用情况，可能需要增加链接服务器的数量。如果未启用“允许进程内”，则 SQL Server 将创建一个代理进程，并在该进程中加载第三方驱动程序。这为第三方驱动程序提供了自己独立的内存空间。但是，由于将数据从代理进程传输到主 SQL Server 进程需要时间，以及代理进程中驱动程序的加载时间，这也会导致性能下降。代理进程提供了第三方驱动程序和 SQL Server 数据库引擎的完全隔离。因此，驱动程序中的任何不稳定性都不会影响 SQL Server 本身。此外，从 Windows 安全角度来看，在代理进程下加载的驱动程序具有模拟的 SQL server 用户安全上下文。驱动程序可能无法访问驱动程序所需的 SQL Server 计算机上的物理资源。进程中加载的驱动程序具有 SQL Server 引擎本身的 Windows 安全上下文。这通常足以访问驱动程序所需的任何资源。 |
| 非事务处理更新 | 默认情况下，SQL Server 使用 Microsoft 分布式事务处理协调器(MSTDS)来确保第三方驱动程序中的任何最新更新都与 SQL Server 事务正确同步。这通常称为两阶段提交。如果您的第三方不支持分布式事务(此选项仅适用于 OLE DB 访问接口，不适用于 ODBC 驱动程序)，并且您仍然希望允许更新并承担 SQL Server 提交与第三方驱动程序提交不同步的风险，请启用此选项。 |
| 索引作为访问路径 | 该选项通常用于不支持 SQL 的非常基本的 OLE DB 提供程序。但是它们支持通过索引访问数据。该选项通常不启用。 |
| 禁止即席访问 | 如果要阻止对链接服务器的访问，请启用此选项。 |
| 支持“Like”运算符 | 如果您的第三方驱动程序支持 like 运算符，请启用此选项。大多数现代数据库提供商都支持这一点。应该选择这个选项。 |

总之，SQL Server 提供了许多将 CONNX 数据源(如 Adabas)和 SQL Server 数据集成在一起的方法。每一种都有自己的优点和缺点。每个项目的安全和业务需求应该根据可用选项进行评估，以确定最适合特定用例的选项。

要了解更多信息，请访问 www.connx.com。