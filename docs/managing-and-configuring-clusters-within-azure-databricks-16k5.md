# 在 Azure 数据块中管理和配置集群

> 原文：<https://dev.to/willvelida/managing-and-configuring-clusters-within-azure-databricks-16k5>

[![](../Images/a3150d9962ff6fabec1103866635a8c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o5L1DVJk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZOfeaPs3Ux8lAPszHFYH5A.png)

Azure Databricks 中的集群可以为我们这些数据工程师做很多令人敬畏的事情，比如流、生产 ETL 管道、机器学习等等。

在 Azure Databricks 中，集群执行两种类型的角色:

1.  **交互式，**用于与交互式笔记本协同分析数据。
2.  **作业，**用于使用 UI 或 API 运行自动化工作负载。

我们可以使用 UI、Databricks CLI 或 Databricks Clusters API 在 data brick 中创建集群。

**创建集群**

在 Azure Databricks 中，我们可以创建两种不同类型的集群。

1.  标准，这些是默认的集群，可以用于 Python、R、Scala 和 SQL
2.  高并发性，这些被调整为提供最有效的资源利用、隔离、安全性和性能，以供多个并发活动用户共享。然而，这些类型集群只支持 SQL、Python 和 R 语言。

高并发集群是一种托管的云资源。使用这种类型的集群的好处是，它们提供 Spark-native 细粒度共享，以实现最大的资源利用率和最小的查询延迟。这是通过以下方式实现的:

*   **抢占**，主动抢占过度使用的用户的 Spark 任务，以确保每个人都能获得公平的集群份额，并且即使在与其他用户争夺时间的情况下，他们所执行的任务也能及时完成。
*   **故障隔离**，为每台笔记本电脑创建一个环境，将它们相互隔离。

使用 UI 创建集群是一件非常简单的事情。在侧边栏中，单击集群图标。然后点击**创建集群**按钮。然后，您可以为该群集提供以下配置设置:

[![](../Images/75ca16485b1c46731561e5ca5c327435.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SGpLjge6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AldptCaTdaP6IaH7kcKtXOQ.png)

为了降低成本，我选择了一个非常小的集群大小，但正如您从上面的图片中看到的，我们可以为我们的新集群选择以下设置:

*   名字
*   集群模式(高并发或标准)
*   数据块运行时版本
*   Python 版本
*   驱动程序类型
*   工人类型
*   自动终止
*   配置设置

稍后我们将详细介绍这些设置。

**在数据块中显示集群**

通过单击侧栏中的集群图标，可以在 Databricks 工作区中显示集群

[![](../Images/75e6f2d835875b01cf945f74713b7c9d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6737Sw_F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AESXOVT6VauU2McOxQPjYLw.png)

从上图中可以看出，我们可以在集群页面中看到两个列表。一个用于交互式集群，另一个用于作业集群。每个列表包括以下信息:

*   集群名称
*   集群的状态
*   群集拥有的节点数
*   群集中驱动程序和工作节点的类型
*   集群拥有的 Databricks 运行时版本是什么
*   谁创建了集群或集群的作业所有者。

对于交互式集群，我们可以看到连接到集群的笔记本电脑和图书馆的数量。我们还可以从列表中查看 Spark UI 和日志，并可以选择终止、重启、克隆或删除集群。我们还可以从该列表中设置群集的权限。我们还可以进行一些过滤来查看某些集群。

**基本集群选项**

在集群 UI 中，我们有许多基本选项可用于集群:

*   我们可以克隆一个集群。这使得我们几乎可以复制和粘贴我们现有的集群。当集群创建表单打开时，它会预先填入我们要克隆的集群的配置。但是，群集权限、安装的库和连接的笔记本不包括在其克隆中。
*   **我们可以编辑集群的配置**。连接到群集的任何库、作业和笔记本将保持连接到群集。但是，如果我们编辑群集，我们必须重新启动群集，这些更改才会生效。如果用户当前正在使用群集，这将会影响他们。
*   我们可以启动和终止我们的集群(打开和关闭它们)。这让我们在不使用集群时节省了资金，并且避免了每次启动集群时都必须进行配置。当您重新启动已终止的群集时，Databricks 会使用先前分配给该群集的相同 ID 重新创建该群集，安装该群集上的所有库，并重新连接笔记本。我们可以指定一段活动时间，当通过后，集群将自动终止。
*   Databricks 保留最近 30 天内终止的多达 70 个交互式集群的配置，以及由作业调度程序终止的多达 30 个作业集群的配置。为了更长时间地保存这些信息，我们可以在集群列表中锁定一个集群。我们最多可以锁定 20 个集群。
*   **如果我们不再需要使用某个集群**，我们也可以将其删除。您不能撤消此操作，也不能删除固定的集群。

这些操作既可以通过 UI 执行，也可以使用 Databricks API 以编程方式执行。

**事件日志**

我们可以使用集群事件日志来跟踪集群生命周期事件。这些事件可以手动触发，也可以由数据块自动触发。日志会捕获以下事件:

*   创造
*   开始
*   重新启动
*   使终止
*   编辑
*   运转
*   调整大小(包括我们手动执行的调整大小和通过自动缩放执行的自动调整大小)
*   NODES_LOST(包括 Azure 终止工作线程的情况)

事件存储 60 天。

让我们看看我们集群的日志。为此，我们可以在群集列表中单击它，然后单击 Event Log 选项卡。

[![](../Images/caccfecc6a906a28a7c7ff5b25d4592d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pxbs2gXH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwJ1IjUbHZ-zbr7LPB91nQA.png)

正如您所看到的，我没有对这个集群做很多工作。我们可以通过单击某个事件，然后单击 JSON 选项卡来进一步了解更多信息。

[![](../Images/1a6b1438aeceff780d41770c24c67dfa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---RgbsLS2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/568/1%2ASD--jRHS3dgz2er2eGio3g.png)

**配置我们的集群**

让我们更深入地了解一下集群的配置。群集由一个驱动节点和工作节点组成。我们可以根据业务案例选择内存密集型或计算密集型工作负载。在 Azure Databricks 中，集群节点实例被映射到被称为 DBU 的计算单元，这些计算单元根据其大小有不同的定价选项。

驱动程序节点维护连接到该群集的所有笔记本的状态信息。它还维护 SparkContext 并解释我们从集群上的笔记本或库中运行的所有命令。它还运行与 Spark 执行器协调的 Spark master。当我们停止使用笔记本电脑时，我们应该将它从驱动器上取下。

工作节点运行 Spark 执行器和集群正常运行所需的其他服务。如果一个集群没有任何工作者，Spark 命令将会失败

**调整集群规模和自动扩展**

当我们创建集群时，我们可以提供固定数量的工人或者提供最小和最大范围。当我们确定工作线程的数量时，Azure Databricks 会确保集群拥有这个数量的可用工作线程。如果我们提供一个范围，Databricks 会根据运行作业所需的内容来选择数字。

这是通过自动缩放实现的。有些时候，有些工作比其他工作要求更高，需要更多的资源。Databricks 会在这些作业中自动添加工人，并在不再需要他们时删除他们。自动缩放为我们提供了两个好处:

1.  与配置不足的集群相比，工作负载运行速度更快。
2.  与静态规模的集群相比，自动扩展集群可以降低总体成本。

Databricks 将监控我们集群上的负载，并决定增加和减少它们以及增加多少。如果我们有未完成的 Spark 任务，那么当这些未完成的任务完成后，集群将扩大规模，并缩小规模。

**集群标签和日志位置**

我们可以监控团队和组织中不同团队使用的资源的成本(当实习生想要旋转一些大规模 GPU 集群以寻求刺激时，这非常有用)。我们在创建集群时将标签指定为键值对，Azure Databricks 会将这些标签应用到云资源。

我们可以在创建集群时指定集群日志的位置。这是每五分钟送到选定的目的地。

**支持 GPU 的集群**

Azure Databricks 还支持使用图形处理单元(GPU)加速的集群。当我们想要打破一些深度学习时，这是非常有用的。

当我们创建任何 Spark 集群时，创建 GPU 集群几乎是相同的。在创建它们时，我们只需要记住以下几点:

*   集群的 Databricks 运行时版本必须支持 GPU。
*   Worker 和 Driver 类型都必须是 GPU 实例类型。

Azure Databricks 安装在 Spark 驱动程序和工作实例上使用 GPU 所需的 NVIDA 软件。当您在 Databricks 中选择支持 GPU 的 Databricks 运行时版本时，您默认同意 NVIDA EULA。

**数据块运行时 ML 集群**

如果你需要一个机器学习和数据科学的环境，Databricks Runtime ML 是一个很好的选择。它带有多个库，如 Tensorflow。与使用 init 脚本相比，它确保了集群中包含的库的兼容性，并减少了集群的启动时间。

你可以点击这里查看 Databricks Runtime [中包含的库的完整列表。](https://docs.azuredatabricks.net/release-notes/cluster-images/index.html)

但是请记住，Databricks Runtime 4.1 ML 集群仅在高级实例中可用。

**确定我们集群的访问控制**

在 Azure Databricks 中，我们可以使用访问控制来允许管理员和用户向其他用户授予对集群的访问权限。有两种类型的集群访问控制:

1.  群集创建权限。如果您是管理员，您可以选择哪些用户可以创建集群。
2.  单个群集权限。可以管理群集的用户可以选择哪些用户可以在给定的群集上执行某些操作。

[![](../Images/b3f53e626e24b3c0a52a0b0f32386c81.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PjcD8u_Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVSIKrWIIGN7Av2e6JUt8hw.png)

我们可以强制实施集群配置，这样用户就不会乱用它们。如果您确实需要将其锁定，您可以禁用为所有用户创建群集的功能，然后在您根据需要配置群集后，您可以授予需要访问给定群集的用户**可以重新启动**的权限。这允许这些用户启动和停止集群，而不必手动设置配置。

**使用 Spark UI 获取集群信息**

如果我们在笔记本上运行 Spark 作业，我们可以使用 Spark UI 显示这些作业的信息。我们还可以对终止的集群使用 Spark UI:

[![](../Images/167eb57596496613000ffcf57a4be061.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FyTYg2b8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2As5qWFbfg5DGPaPhRBnDyjQ.png)

如果我们重启集群，Spark UI 将被新的替换。

**使用初始化脚本**

我们可以使用初始化脚本，这些脚本在 Spark 驱动程序或 worker JVM 启动之前在每个集群节点的启动过程中运行。这些脚本适用于手动创建的集群和由作业创建的集群。您只需创建一次脚本，它将在集群启动时运行。

Databricks 支持两种类型的 init 脚本:全局的和特定于集群的。启动时，全局初始化脚本将在每个集群上运行，而特定于集群的脚本仅限于特定的集群(如果这还不够明显的话)。

这些脚本的输出将保存到 DBFS 的一个文件中。

创建全局 init 脚本相当容易。但是，要小心在其中放入什么，因为它们在集群启动时在每个集群上运行。

首先我们创建文件目录，如果它不存在的话

```
dbutils.fs.mkdirs("dbfs:/databricks/init/") 
```

Enter fullscreen mode Exit fullscreen mode

然后我们显示现有全局初始化脚本的列表

```
display(dbutils.fs.ls("dbfs:/databricks/init/")) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创建脚本。对于本地 init 脚本，我们将配置一个集群名变量，然后创建一个目录，并将该变量名附加到该目录的路径中。

要删除一个脚本，我们可以运行下面的命令

```
dbutils.fs.rm("/databricks/init/my-echo.sh") 
```

Enter fullscreen mode Exit fullscreen mode

**图书馆和笔记本**

如前所述，我们可以使用 UI 查看集群上安装的库和连接的笔记本。理解库如何在集群上工作需要一篇自己的文章，所以我不会在这里做太多的描述。

我们可以看到连接到集群的笔记本电脑，以及它们在集群详细信息页面上的状态。当您停止使用笔记本电脑时，应该将其从集群中分离出来，以释放驱动程序上的内存空间。

[![](../Images/1aa4c3de99195d5d76f9fbfb90e95ddb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rr8qiFe1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUVyrpBXZ7V_6pMHN9w8SVQ.png)

**鳍**

在这篇博文中，我概述了一些在 Azure Databricks 中创建集群时应该记住的事情。

只是一个一般性的提醒，如果你正在尝试一些东西，记得在你完成一段时间后关闭你的集群。你不想把钱花在你用不到的东西上！

记得查看 [Azure Databricks 文档](https://docs.azuredatabricks.net)，了解更多关于集群的最新信息。