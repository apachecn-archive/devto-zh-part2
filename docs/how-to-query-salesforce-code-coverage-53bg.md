# 如何查询 Salesforce 代码覆盖率

> 原文：<https://dev.to/matheusgoncalves/how-to-query-salesforce-code-coverage-53bg>

在 Salesforce 组织中，代码覆盖率百分比是已覆盖行数除以已覆盖行数和未覆盖行数之和的计算结果。

除了确保代码质量之外，单元测试使您能够满足部署或打包 Apex 的代码覆盖率要求。要为 Salesforce AppExchange 部署 Apex 或打包 Apex，单元测试必须覆盖至少 75%的 Apex 代码，并且这些测试必须通过。

运行测试后，可以在开发人员控制台的“测试”选项卡中查看代码覆盖率信息。代码覆盖率窗格包括每个 Apex 类的覆盖率信息以及组织中所有 Apex 代码的总覆盖率。

然而，您也可以将 SOQL 查询与工具 API 一起使用，作为检查代码覆盖率的一种替代方法，以及获得更多细节的一种快速方法。让我们看看它是如何工作的:

## 检查代码覆盖率

### 步骤 1 -获取贵组织的基本 URL:

如果您知道您的组织的基本 URL 是什么(*您也可以从您的浏览器获得它*)，请跳到步骤 2。

*   从开发人员控制台，打开调试菜单，然后选择选项“打开执行匿名窗口(CTRL+E)”
*   输入以下 Apex 代码:

```
 String baseURL =  'https://' + System.URL.getSalesforceBaseUrl().getHost();
    System.debug(baseURL); 
```

Enter fullscreen mode Exit fullscreen mode

*   点击【执行】按钮
*   转到页面底部的日志选项卡
*   复制完整的基础网址，包括 ***https://***

### 步骤 2 -现在你知道了你的基本 URL，创建一个远程站点设置

*   从“设置”中，在快速查找框中输入远程站点设置，然后选择远程站点设置。
*   单击新建远程站点。
*   输入远程站点名称的描述性术语。
*   输入远程站点的 URL。
*   或者，输入站点的描述。

[![](../Images/5dd82e58332a272f511da17c655d54ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fJggGGUu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://matheusgoncalves.com/wp-content/uploads/2018/09/Screen-Shot-2018-09-17-at-10.13.45-AM.png)

*   单击保存。
*   为以防万一，请为“Visual.force.com”格式的 URL 创建第二个远程站点设置:
    *   https://c .**xx99**. visual . force . com-其中 xx99 是您的组织实例的代码。

### 步骤 3 -获取代码覆盖包装的 JSON 文件

*   回到开发人员控制台，打开调试菜单，然后选择选项“打开执行匿名窗口(CTRL+E)”
*   输入以下 Apex 代码: