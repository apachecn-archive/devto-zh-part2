# 对大型机数据的 SQL 访问

> 原文：<https://dev.to/techcommunity/sql-access-to-mainframe-data-3mna>

# CONNX

| 2018 年第三期 | [![](../Images/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://bit.ly/2msfoqz) |
| --- | --- |

## 大型机数据

几十年来，许多人预测大型机将会消亡，但所有这些预测都是错误的。大型机行业的领导者 IBM 在 1997 年的年收入接近 800 亿美元。20 年后，IBM 仍有 800 亿美元的年收入(不含通胀)。考虑到通货膨胀因素，大型机的收入随着时间的推移有所下降。然而，收入数字的巨大规模表明，在未来几十年里，大型机将成为大公司的一部分。大型机软件工程师和管理员的平均年龄正在增长，并达到退休的临界点。

此外，大学毕业后学习大型机技术(COBOL、VSAM、IMS、Adabas)的软件工程师人数也创下了历史新低。这使得可用资源与维护和增强大型机上运行的这些关键系统所需的资源数量之间的差距越来越大。

## 使用 SQL 弥合差距

SQL 本身是一种非常成熟的技术，但是它仍然在大学里教授，并且是现代软件开发的一部分。它是访问现代关系型企业数据库(如 Oracle、SQL Server 和 DB2)的主要方式。SQL 中的概念简单易懂，这些概念可以应用于从不同供应商的各种数据库中访问数据。有几种基于 SQL 的数据访问 API。对于基于 Java 的应用程序，有 JDBC。对于其他编程语言，有 ODBC、OLE DB 和 Microsoft .NET 与这些数据访问 API 的结合使整个业务集成市场蓬勃发展。成千上万的应用程序通过 ODBC 和 JDBC 使用 SQL 访问数据。

CONNX 弥合了这些现代数据访问方法与大型机中既不支持 SQL 也不支持 ODBC/JDBC 的遗留数据源(如 Adabas、VSAM 和 IMS)之间的差距。

## 使用 CONNX 通过 SQL 虚拟化大型机数据

所有基于 SQL 的数据库都有一个信息模式。这是一个元数据存储库，包含关于数据库中所有对象及其属性的信息。在 SQL 数据库中，有一个表的概念，它包含具有相同信息结构的记录。该结构由一组列定义，其中每一列都指定了一段数据的确切属性。(是约会吗？它是一个文本字段吗？最大长度是多少？等等。)表和列的结构定义在所有 SQL 数据库中(通常)是统一的，与供应商无关。正是这种一致性使应用程序开发人员能够使用 SQL 来访问数据，而无需提前了解数据库供应商的具体细节。当然也有例外，每个供应商都有自己的 ANSI SQL 语法扩展集。但是所有企业关系数据库供应商都支持相同的核心 ANSI SQL 语法。为了让 CONNX 提供对大型机数据的 SQL 访问，必须创建一个等效的 SQL 目录来描述被访问的大型机数据。在 CONNX 中，该目录被称为 CONNX 数据字典(CDD)。

[![](../Images/514440db046ca69c13b0c4ba94090495.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SQwp3Zd7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/connx1.jpg/cf5fa4c0-9bcd-474a-9457-05ad483a4e8f%3Ft%3D1531384393563) 

**图 1:** CONNX 数据字典条目为 Adabas 文件

CDD 包含访问大型机数据源所需的所有信息，以及将数据表示为关系表所需的所有元数据。它包含 SQL 数据类型、SQL 表名和 SQL 列名之间的映射，以及访问大型机数据所需的物理字段/偏移量/数据类型信息。遗留的大型机应用程序，比如 COBOL 应用程序，实际上有一个非常相似的、更古老的概念，叫做 COBOL copybook。这个副本使 COBOL 应用程序能够使用有意义的名称来引用遗留数据，并且还描述了被存储或检索的数据的格式。CONNX 利用了这些字帖的存在，并允许直接从大型机字帖导入 CDD。大型机 Adabas 也有类似的概念，即自然 DDM，它为 Adabas 文件中的字段提供了更长的描述性名称。CONNX 从这种格式和许多其他格式中导入元数据。

[![](../Images/40269fd59a73a5f7b66c1e9bdee19e8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BJwMHKnp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/connx2.jpg/443e1d05-8129-4b12-8a84-6a2d2179654d%3Ft%3D1531384397644) 

**图 2:** 从 COBOL 字帖导入数据

虽然许多公司的数据都在大型机上，但是集成点可能不在大型机上。一个集成点可以是一个数据湖/集市/仓库或者一个基于 web 的应用程序。如果集成点不在大型机上，那么就会出现额外的挑战。Linux、UNIX 和 Windows (LUW)系统存储数据的方式不同于大型机系统。例如，大多数基于主机文本的数据都是用一种叫做扩展二进制编码十进制交换码(简称 EBCDIC)的东西编码的。在 LUW 系统上，Unicode

或 ASCII 更常用于文本数据。与大多数 LUW 系统相比，整数字节在大型机中的存储顺序相反。此外，平台之间还有许多其他差异。CONNX 通过存储以本机大型机格式正确读写数字、字符和日期数据所需的所有信息，同时还以标准 SQL 格式提供数据，从而消除了所有这些差异和复杂性。

应用程序开发人员不必担心或关心数据在大型机中以不同的物理方式存储的事实；一旦定义了 CDD，对他们来说就完全透明了。

[![](../Images/a5e32d8480068a99e6800f0c8fad11c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X1XUSUoF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/connx3.jpg/713ea79d-503f-42b7-957f-b80f38e46b83%3Ft%3D1531384400623) 

**图 3:** 查询生成器加入 SQL 表

CONNX 不仅提供了对特定大型机数据源的单一访问，而且还使所有大型机数据源看起来就像在一个虚拟数据库中一样。例如，在 VSAM 系统和 IMS 中都有数据的组织可以使用一个连接来查询、合并或移动来自数据源的数据。

## 保护对大型机数据的访问

公司最担心的是有人会未经授权访问大型机上的数据。CONNX 用几个安全特性和概念正面解决了这一问题。身份验证是第一道防线。为了通过 CONNX 访问大型机中的数据，您必须首先提供正确的大型机身份验证凭据。换句话说，如果您没有通过绿屏/终端仿真器登录到大型机的凭证，您也不能用 CONNX 这样做。这确保了大型机中任何现有的基于用户的安全限制都得到遵守。第二道防线是 CDD 的 CONNX 全球安全设置。默认情况下，所有 cdd 都是只读的，除非明确授予，否则不允许写权限。这可以在 CDD 级别进行配置。第三道防线是 CONNX SQL 安全层。关系数据库有安全性的概念——您可以指定哪些用户可以对哪个表进行选择、插入、更新和删除访问。CONNX 为所有大型机数据源提供了相同的 SQL 安全机制。CDD 管理员可以为单个用户或用户组定义确切的访问权限。CONNX 观点是第四道防线。一些组织不仅希望严格控制单个用户可以看到的数据列，还希望严格控制可见的行。这在 CONNX 视图中是可能的，在 CONNX 视图中，当前登录用户的名称/ID 可以连接到表中的一个字段，以过滤返回哪些行。这种类型的视图将根据登录用户返回不同的行，确保登录用户只能看到属于他们的行。第五道防线是通过 TCP/IP 传输敏感数据时的加密。所有 CONNX TCP/IP 连接都可以用最先进的 TLS/SSL 加密。即使没有启用 TLS/SSL，任何敏感信息(如用户名和密码)也会一直加密。

## 来自基于 SQL 的应用程序的统一访问

建立并保护 CONNX CDD 后，它可以查询大型机数据。Java 应用程序和框架使用 JDBC 接口来访问数据。建立到 CONNX 的连接只需要认证信息、数据源名称(CDD)和 JDBC 服务器的名称。JDBC 服务器是用于 Java 的 CONNX 服务，它完成所有的数据访问和 SQL 查询处理。这项服务可以在任何 LUW 平台上运行。下面代码片段显示了 Java 应用程序连接到 CONNX 的一种方式(其他连接方法也是可能的):

```
try { Class.forName("com.Connx.jdbc.TCJdbc. TCJdbcDriver").newInstance(); } catch (Exception ex) { ex.printStackTrace(); } Properties info = new Properties(); String connString = "jdbc:connx:DD=\<connx\_datasource\_name\>;port=7500;GATEWAY=\<connx\_jdbcserver\_name\_ or\_ip\>"; info.put("user", "my\_userid"); info.put("password", "my\_password"); connectionObj = DriverManager. getConnection(connString, info); 
```

一旦建立了连接，应用程序就可以使用标准 SQL 查询数据，或者在权限允许的情况下更新数据。CONNX 向所有支持回滚功能的大型机数据源提供事务功能(Adabas、CICS 下的 VSAM 和 IMS 都是如此)。

CONNX 还支持 ODBC 访问来自所有 LUW 平台的数据，通过这个数据访问 API 可以获得相同的认证和加密特性。

CONNX 提供了一个名为 Infonaut 的查询工具，它将 SQL 语句返回的数据可视化。

[![](../Images/c3532f41e13223b787873b79763d68e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BQGNtw5g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9808473/connx4.jpg/2ac3de30-2c40-4c3f-ba1c-63981414ccc2%3Ft%3D1531384403779) 

**图 4:** 显示查询结果

## 弥合差距

CONNX 使正在接受培训并熟悉 SQL 的新一代软件工程师能够访问大型机数据。无论公司是在构建新的应用程序、扩展现有的应用程序，还是将大型机数据集成到数据湖中，CONNX 都能安全、轻松、快速地完成任务。

要了解更多关于 CONNX 的信息，请联系您的软件公司销售代表并访问[www.softwareag.com/connx](http://www.softwareag.com/connx)。