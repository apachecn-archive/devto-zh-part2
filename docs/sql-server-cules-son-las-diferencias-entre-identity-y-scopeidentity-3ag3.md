# SQL server:@ @ identity 与 Scope_identity 有何不同

> 原文：<https://dev.to/campusmvp/sql-server-cules-son-las-diferencias-entre-identity-y-scopeidentity-3ag3>

[![](../Images/1da4b29fc85ca0213d8eab00c5625287.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4qA5VIba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2018/3T/Identidad.jpg) 

凯尔·格伦在[中的照片](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在数据库表中插入记录时，通常会自动为新记录生成一个新的唯一标识符，通常是一个独立标识符。这意味着我们从 1 开始，并且对于我们添加的每个新记录，它将自动增加到 2，3，4-我...。诸如此类。这听起来很愚蠢，它具有许多含义，因为稍后我们将使用此标识符将新记录与数据库中需要使用它的任何其他表相关联。例如，商业发票与其商业发票明细行是由表头识别码所连结。就像这样-我...。

情况是**这种看似无稽之谈的自治式**的产生，比复杂得多，因为必须考虑一件非常重要的事情:**同时性**。当你在你的开发团队里做测试时，添加新的记录很容易:只有你。但是，在一个可能同时输入成千上万用户并向同一表中添加记录的实际系统中，系统必须确保序列得到满足而不会留下任何空白，并且不会发生重大锁定。

从刚插入的记录中获得自动编号的一种天真方法就是在做相应的‘t0’之后，简单地获取这些编号的最大值。但是，在生产系统中，除非您导致锁定，否则它不会对您提供保修服务，因为许多人可能同时输入数据，并且在您插入自己的数据和读取数据之间，可能插入了另一个数据。这是个棘手的问题。

市场上的每个关系数据库管理器系统(RDBMS)都使用自己的理念来解决这个问题。我们已经在这里向您解释了 **[【如何获取最新插入的](https://www.campusmvp.es/recursos/post/Auto-numericos-como-obtener-el-ultimo-insertado-en-Oracle-MySQL-y-SQL-Server.aspx)** 【最常用的部分】:Oracle 、 **MySQL** 和 **SQL Server**

对于 SQL Server，我们解释了如何使用语句“`SCOPE_IDENTITY()`”。你可以在上面的链接上查阅。但是，**SQL server 至少提供了 3 种方法来获取插入的最后一个 id**。虽然看起来可能是一样的，**并不等同于**，知道它们的不同点是非常重要的:

*   `@@Identity`:返回同一连接中的最后一个 ID。
*   ****:我们推荐的，返回在**相同连接和上下文**中创建的最后一个 ID(因此其名称)。在本例中，上下文是指当前查询或存储过程。****
***   **`ident_current(nombre)`** :返回我们给你的表的最后一个 ID(当时的那个)**

 **在大多数情况下`@@identity`和`scope_identity()`的行为相同，但并不总是如此。

例如，如果我们的表中有一个触发器(*trigger*)并启动了一个插入查询，该查询将生成一个新记录，从而生成一个新 ID，如果*触发器*又在另一个表中生成另一个记录(可能在此表中生成另一个 ID)，则在这种情况下 不是我们更新的表的 ID(这几乎不是我们想要的)，因为它会返回上次在该连接中生成的表的 id，如上表中所述。 但是，在本例中，`scope_identity()`返回我们期望的标识符(在我们的表中插入的标识符)，因为它始终返回当前上下文的标识符，在本例中是我们的插入查询。

这就是通常推荐使用`scope_identity()`的原因。

直到结果出来！**