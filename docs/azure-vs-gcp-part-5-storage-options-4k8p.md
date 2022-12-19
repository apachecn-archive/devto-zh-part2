# Azure vs GCP 第 5 部分:存储(Azure)

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-5-storage-options-4k8p>

任何应用程序都需要一些地方来存储数据，如 SQL、NoSQL、存储等。在这篇文章中，我研究了 Azure 存储服务，并为 GCP 存储写了单独文章。

# 天蓝色储物

Azure 存储在 Azure 存储帐户下链接。所以我需要先创建帐户，但我需要了解有哪些选项可用。

### Azure 存储类型

Azure 提供几种类型的存储和服务。

*   Blob:为虚拟机存储图像、文档或 VHD 等文件。它为许多语言提供了 REST 端点和 SDK。
*   文件:高度可用的网络共享，您可以通过 SMB 访问。它还提供了 REST 端点。
*   磁盘:作为虚拟机的本地文件系统。
*   表格:表格数据存储。
*   队列:队列对象。

哦，前三个听起来差不多？是的，它是。所以有很好的矩阵来理解差异。

[比较:文件和 blob](https://docs.microsoft.com/en-us/azure/storage/common/storage-decide-blobs-files-disks#comparison-files-and-blobs)
[比较:文件和磁盘](https://docs.microsoft.com/en-us/azure/storage/common/storage-decide-blobs-files-disks#comparison-files-and-disks)

在本文中，由于从 Web 应用程序的角度来看我需要存储，所以我使用 blob 存储。

### 账户选项

除了存储选项，我还需要选择帐户选项。

*   通用 V2 (GPV2)
*   通用 V1 (GPV1)
*   Blob 存储

你可以在这里阅读细节但是简单来说，GPV2 = GPV1 + Blob 存储。查看[推荐](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-options#recommendations)部分，了解你想使用哪个账户的更多信息。

### 表现

Azure 存储提供两种存储类型。

*   高级:固态硬盘底座。适合虚拟机。
*   标准:普通硬盘底座。

它还提供了两个访问层。

*   热:适用于频繁访问的存储。更低的接入成本。
*   冷:适用于较少访问的存储。降低存储成本。

### 其他事物

关于这项服务，你还应该了解更多的东西，比如安全性、冗余、访问方法。您可以在[规划 Azure 文件部署](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-planning)中找到这些信息

它还提供与其他服务相同的 Azure 平台级功能，例如:

*   前十字韧带
*   防火墙
*   自定义 DNS
*   监视
*   加拿大
*   报告
*   与其他服务集成等。

集成是一个有趣的话题，因为它与 Azure 搜索、事件网格等一起工作。这个我留着以后话题用。

## 天蓝色斑点存储

详情见[此处](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction)。我只是在这里分享结构概念。

Blob 服务具有以下结构。
[![concept](../Images/5df16587e266dc4a7f9ebda9adb4d7a5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s---WVkkoNG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.microsoft.com/en-us/azure/storage/blobs/media/storage-blobs-introduction/blob1.png)

*   帐户:上面解释的存储帐户。
*   容器:作为分组或类似的文件夹。
*   Blob:实际内容。

# 咱们码！！

好了，说够了！！让我们写代码，因为这是我感兴趣的。

1.首先，我创建存储帐户。有几种方法可以创建它，但是我在 portal 中使用 CLI。进入 [Azure 门户](https://portal.azure.com)，选择“云壳”。
T3![portal](../Images/4211e82931082a772333bb5dcb5741d8.png)T5】

2.运行代码来创建帐户。我在美国东部调配我的帐户，使用地理冗余，普通存储作为热层，仅支持 https 访问。第二个命令显示我在应用程序中使用的连接字符串。

```
az storage account create -n cloudcomparestorage -g CloudCompareRG --kind storageV2 --sku Standard_GRS --access-tier Hot --location eastus --https-only
az storage account show-connection-string -n cloudcomparestorage -g cloudcomparerg 
```

Enter fullscreen mode Exit fullscreen mode

3.为 blob 创建一个图像容器。我将它设置为公共可访问。

```
az storage container create --name images --public-access blob --account-name cloudcomparestorage 
```

Enter fullscreen mode Exit fullscreen mode

4.blob 存储就绪后，打开 Visual Studio 并创建新项目。选择“ASP。NET 核心 Web 应用程序”并创建它。
[![code](../Images/4d70a3433beb334686830df748fe2651.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--VDKh95RY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gx6qz5gs705tig6ntqxc.PNG)

5.选择“Web API”并单击确定。
[![code](../Images/5821566eb997b9130d401ace6f9158d3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--JK14pkXN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hs7f1sokt46yoxoqnrvq.PNG)

6.加上“WindowsAzure。存储“NuGet 包。
[![code](../Images/9f2f2ab059211e2380eb624db5bac445.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--FeWjYTh9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/97mvr09xuqsypg4vxn56.PNG)

7.将现有的“ValueController.cs”重命名为“StorageController.cs”，然后粘贴代码。在第 20 行替换存储键。

```
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Threading.Tasks;

namespace Storage.Controllers
{
    [Route("api/[controller]")]
    public class StorageController : Controller
    {
        static CloudBlobClient blobClient;
        const string blobContainerName = "images";
        static CloudBlobContainer blobContainer;

        public StorageController()
        {
            CloudStorageAccount storageAccount = 
                CloudStorageAccount.Parse("<Your connection string>");

            // Create a blob client for interacting with the blob service.
            blobClient = storageAccount.CreateCloudBlobClient();
            blobContainer = blobClient.GetContainerReference(blobContainerName);
        }

        // GET api/storage/filename
        [HttpGet("{filename}")]
        public async Task<IActionResult> Get(string filename)
        {
            try
            {
                CloudBlockBlob blob = blobContainer.GetBlockBlobReference(filename);
                var image = await blob.OpenReadAsync();
                return new ObjectResult(image);
            }
            catch (Exception ex)
            {
                return NotFound();
            }
        }

        // POST api/storage
        [HttpPost]
        public async Task Post(IFormFile file)
        {
            try
            {
                CloudBlockBlob blob = blobContainer.GetBlockBlobReference(file.FileName);
                await blob.UploadFromStreamAsync(file.OpenReadStream());
            }
            catch (Exception ex)
            {               
            }
        }

        // DELETE api/storage/filename
        [HttpDelete("{filename}")]
        public async Task<IActionResult> Delete(string filename)
        {
            try
            {
                CloudBlockBlob blob = blobContainer.GetBlockBlobReference(filename);
                await blob.DeleteAsync();
                return Ok();
            }
            catch (Exception ex)
            {
                return NotFound();
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试一下

1.按 F5 调试应用程序。检查地址和端口。

2.打开 [Postman](https://www.getpostman.com/) 选择“POST”作为动词，输入端点地址，然后点击“Body”。
T3![test](../Images/ca5cedcb10993adac23de62c1b7d56ba.png)T5】

3.从下拉键中选择“文件”。
[![test](../Images/8ed9bf9c49e519598daf45d80a4dafc1.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--TM1tmQYk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/apkmtwbv6sownxrd20pf.PNG)

4.输入“文件”键，从本地电脑中选择任何图像文件，然后点击“发送”。我选择了“apple-touch-icon.png”。
[![test](../Images/1db1d81697cff376ddc3d5ac6bbf4f4a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--AdQjPzfY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qnnvallbjcby88tn4nef.PNG)

5.将动词从“POST”改为“GET”，然后在地址前加上“/”。
[![test](../Images/3ed40eafe0fe7b1dd1c26fdc419fa4fb.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--GKiOTt-o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u5aqmge0pgczjtbtpux9.PNG)

6.转到 Azure Portal Cloud shell 并运行命令以确认存在 blob 文件。

```
az storage blob list --account-name cloudcomparestorage -c images --query '[].{name:name}' 
```

Enter fullscreen mode Exit fullscreen mode

7.当我设置 blob 公共访问时，你也可以从任何地方通过浏览器访问图像。

https://. blob . core . windows . net//

8.在 Postman 中，将动词改为“删除”并点击发送。
[![test](../Images/1440b541b59a3ebb0bab2c619603eb6c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--6Fa9t4Dt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i9g9q7zv4zozxbpmpjzq.PNG)

# 部署到各个平台

现在代码工作了。让我们部署到 Azure Web 应用程序和 GCP 应用程序引擎。详见[第一部分](https://dev.to/kenakamu/azure-vs-gcp-part-1-aspnet-developer-experience-22i)。

## Azure Web Apps

是的，它像预期的那样工作。这里没什么可写的。但是我用的是 Windows 版本。
[![azure](../Images/468750429f07e032a7b2a2b1bf99215b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--O4-Zv3o_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y2yqldn5hrb2ihnu639e.PNG)

## GCP App 引擎

这也是预期的效果。虽然存储仍然在 Azure 上，但 web api 住在 GCP :)
[![gcp](../Images/3e6825778b996ab14b0db389233bd6ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RIsUewdc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e5dbbiuvezumnwcckbqs.PNG)

# 总结

当我开发一个 Web API 时，使用 SDK 很容易，我不必关心存储在哪里，以及它们是如何备份、扩展或保护的。但是，当我考虑服务计划和选项时，有很多事情需要考虑。然而，通过几次点击，我可以调整性能、弹性、安全性等等:)

我打赌 GCP 应该有类似的能力，但让我们在下一篇文章中看到它。

肯恩(男名)