# 安装、配置和使用 Azure Databricks CLI

> 原文：<https://dev.to/willvelida/installing-configuring-and-using-the-azure-databricks-cli-4nb1>

[![Image result for azure databricks](../Images/ce32227591ab9b923d1c6046d1964310.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b0FviL0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2AdKTeyeT9Slg4jf5M.jpg)

Databricks 附带了一个 [CLI 工具](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)，它提供了一种与 Azure Databricks 中的资源进行交互的方式。它建立在数据块 [REST API](https://docs.azuredatabricks.net/api/index.html) 之上，可以与工作区、DBFS、作业、集群、库和秘密 API 一起使用

为了安装 CLI，如果您使用的是 Python 2 或 Python 3.6，则需要 Python 2 . 7 . 9 及更高版本，如果您使用的是 Python 3，则需要更高版本。

要安装 CLI，请使用以下 pip 命令:

```
pip install databricks-cli 
```

或者如果你使用的是 Python 3:

```
pip3 install databricks-cli 
```

您可以通过运行以下命令来确认一切正常:

```
databricks --version 
```

在我们实际使用 CLI 及其命令之前，我们需要为它设置身份验证。为此，我们使用 Databricks 个人访问令牌。要生成令牌，请进入 Azure Databricks 个人资料中的**用户设置**，然后前往**访问令牌。**

点击**生成新令牌**并输入描述和有效期。

[![](../Images/6e82b36136c93e711a042eb6bf09542d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bUve_GRT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/750/1%2AFdqbjDoFlrkHRIS1dkpnnw.png)

一旦你这样做了，你会得到一个令牌。复制并粘贴到某个安全的地方，因为这将是你唯一一次看到它。创建令牌后，您应该会在访问令牌列表中看到它。

现在我们有了一个令牌，我们可以设置身份验证来使用 Databricks CLI。为此，打开命令提示符并键入以下命令:

```
databricks configure --token 
```

您需要提供主机和令牌来对其进行身份验证。主机应该只是 https://.azuredatabricks.net，不需要包含 0 = whatevernumberhererubbish。如果这样做，在尝试调用命令时会得到 JSONDecodeErrors。

[![](../Images/dae2435a30200767269217ef7a54d870.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G4_WbHfu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/818/1%2APZaJ_OvrSs_ovW_dQU1Nhg.png)

设置好一切后，我们现在可以使用 Databricks CLI 与 Databricks 中的资源进行交互了！为了测试它，我们可以像在本地机器上一样，通过运行下面的命令来查看我们的工作空间目录中有什么:

```
databricks workspace ls /Users/<your\_username> 
```

您应该会得到用户名文件夹中所有资源的列表。

如果您想获得关于在 CLI 中可以使用哪些命令参数的帮助，可以运行以下命令:

```
databricks <resource> -h 
```

例如，如果我想知道在使用 DBFS CLI 时可以使用哪些命令，我可以运行以下命令:

```
databricks fs -h 
```

我会得到以下输出:

[![](../Images/bdb3c4fe826e96a97c6755b7b14a9599.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--moDyXvTP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/737/1%2AYVUoOaw6PzmRxrWBhw5DdQ.png)

在这篇短文中，我们已经设置了 Databricks CLI 工具，因此我们可以与我们的 Databricks 工作区进行交互。请记住，您的访问令牌将有一个有限的生命周期，一旦它用完，您将需要重新配置您的数据块工作空间。

但是如果你爱用 CLI 而不是 UI(谁不爱呢？)那么这是一个针对您的数据块实例使用的便利工具。

另外，CLI 项目托管在 [GitHub](https://github.com/databricks/databricks-cli) 上！所以如果你想为它开发更多的特性，那就来吧:)

希望这篇文章对你有所帮助。如果你有什么问题，就在下面评论，我会尽力回答的！