# Azure vs GCP 第 6 部分:存储(GCP)

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-6-storage-gcp-4f50>

在[上一篇文章](https://dev.to/kenakamu/azure-vs-gcp-part-5-storage-options-4k8p)中，我解释了 Azure Storage 提供了什么，以及我如何将 Azure Storage 用于简单的 Web API。在本文中，我尝试 GCP 存储。

# GCP 储

与任何其他资源一样，GCP 存储与项目相关联。在 GCP，当我说“存储”时，它可能包括您可以存储结构化数据的 SQL 或 Datastore，但在这篇博客中，我只谈论存储非结构化数据的存储。

[![storage](img/13cdda4d736ed157ca554523300e4601.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZKpLmU00--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d815fo6i2pni0ogrm8qf.PNG)

它需要“桶”来存储数据，所以我需要创建一个桶。

### GCP 仓储类

有几个存储类别决定了服务级别和定价。

*   多区域存储:具有地理冗余的 99.95%可用性 SLA。存储世界各地频繁访问的对象。
*   区域存储:一个区域内 99.9%的可用性 SLA。将频繁访问对象存储在一个区域中。
*   近线存储:99%可用性 SLA，大约每月一次访问。
*   Coldline 存储:99%可用性 SLA，每年访问一次。

这一次，由于我需要频繁地访问我的数据，所以我使用多区域或区域。但是最好使用其他类来备份或归档数据，这需要更少的访问。

详见
[存储等级对比](https://cloud.google.com/storage/docs/storage-classes#comparison_of_storage_classes)。

### 铲斗位置

一旦决定了要使用哪个类，那么就需要决定要在哪里存储数据。更多细节见[铲斗位置](https://cloud.google.com/storage/docs/bucket-locations)。

# 咱们码！！

好了，说够了！！让我们写代码，因为这是我感兴趣的。

1.首先，我创建 bucket。有几种方法可以创建它，但是我在 portal 中使用 CLI。进入[谷歌云控制台](https://portal.azure.com)，选择“云壳”。
T3![portal](img/9bd6125162abba0814939581de138e98.png)T5】

2.运行代码来创建 bucket。我一直使用“g cloud”CLI，但是对于存储，我需要使用“gsutil”。这将创建多区域存储，因为我将位置指定为“US”。

```
gsutil mb -l us gs://cloudcomparestorage20180306 
```

Enter fullscreen mode Exit fullscreen mode

[![storage](img/880fad351f53e46167147009439bf2be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QDH7V2cK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ncw9d61pyoge7qwgl02i.PNG)

3.现在我需要向服务部门认证。似乎有几种方法可以做到这一点，但是我通过在本地运行以下命令来直接进行身份验证。如果你还没有 gcloud，从[这里](https://cloud.google.com/sdk/)安装。

它启动浏览器，所以只需认证。

```
gcloud auth application-default login 
```

Enter fullscreen mode Exit fullscreen mode

4.创建存储桶并完成身份验证后，打开 Visual Studio 并创建新项目。选择“ASP。NET 核心 Web 应用程序”并创建它。
[![code](img/4d70a3433beb334686830df748fe2651.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--VDKh95RY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gx6qz5gs705tig6ntqxc.PNG)

5.选择“Web API”并单击确定。
[![code](img/5821566eb997b9130d401ace6f9158d3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--JK14pkXN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hs7f1sokt46yoxoqnrvq.PNG)

6.添加“谷歌。Cloud.Storage.V1" NuGet 包。
[![code](img/e7298150261e5540601e379a734813b9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--WgUWZDZk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6q6n6juyfqgxcuag6u6e.PNG)

7.将现有的“ValueController.cs”重命名为“StorageController.cs”，然后粘贴代码。在第 20 行替换存储键。

```
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System;
using System.Threading.Tasks;
using Google.Cloud.Storage.V1;
using System.IO;

namespace Storage.Controllers
{
    [Route("api/[controller]")]
    public class StorageController : Controller
    {
        StorageClient storageClient = StorageClient.Create();
        static string bucketName = "cloudcomparestorage20180306";
        static string folderName = "images";

        public StorageController()
        {
        }

        // GET api/storage/filename
        [HttpGet("{filename}")]
        public async Task<IActionResult> Get(string filename)
        {
            try
            {
                MemoryStream image = new MemoryStream();
                await storageClient.DownloadObjectAsync(bucketName, $"{folderName}/{filename}", image);
                image.Seek(0, SeekOrigin.Begin);
                return new ObjectResult(image);
            }
            catch (Exception ex)
            {
                return NotFound();
            }
        }

        // POST api/storage
        [HttpPost]
        public async Task<IActionResult> Post(IFormFile file)
        {
            try
            {
                await storageClient.UploadObjectAsync(bucketName, $"{folderName}/{file.FileName}", file.ContentType, file.OpenReadStream());
                return Ok();
            }
            catch (Exception ex)
            {
                return StatusCode(503);
            }
        }

        // DELETE api/storage/filename
        [HttpDelete("{filename}")]
        public async Task<IActionResult> Delete(string filename)
        {
            try
            {
                await storageClient.DeleteObjectAsync(bucketName, $"{folderName}/{filename}");
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
T3![test](img/ca5cedcb10993adac23de62c1b7d56ba.png)T5】

3.从下拉键中选择“文件”。
[![test](img/8ed9bf9c49e519598daf45d80a4dafc1.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--TM1tmQYk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/apkmtwbv6sownxrd20pf.PNG)

4.输入“文件”键，从本地电脑中选择任何图像文件，然后点击“发送”。我选择了“apple-touch-icon.png”。
[![test](img/1db1d81697cff376ddc3d5ac6bbf4f4a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--AdQjPzfY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qnnvallbjcby88tn4nef.PNG)

5.将动词从“POST”改为“GET”，然后在地址前加上“/”。
[![test](img/3ed40eafe0fe7b1dd1c26fdc419fa4fb.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--GKiOTt-o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u5aqmge0pgczjtbtpux9.PNG)

6.转到 Google Cloud 控制台，导航到存储，查看文件是否存在。
[![test](img/dd33c9e2f9627c716cc3d5d951a25d1d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--W6FGb-Ao--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sgvde0x65sebh7z0oy0l.PNG)

7.点击“公开分享”按钮，你可以从任何地方访问它。
[![test](img/5fe1e7378ab54af93a84abe6093e1ccf.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--U8u-KNYm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q8o0orp0agnruqlbi2h1.PNG)

[https://storage.googleapis.com/](https://storage.googleapis.com/)//

8.在 Postman 中，将动词改为“删除”并点击发送。
[![test](img/1440b541b59a3ebb0bab2c619603eb6c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--6Fa9t4Dt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i9g9q7zv4zozxbpmpjzq.PNG)

# 部署到各个平台

让我们部署到 Azure Web 应用程序和 GCP 应用程序引擎。详见[第一部分](https://dev.to/kenakamu/azure-vs-gcp-part-1-aspnet-developer-experience-22i)。

## GCP App 引擎

是的，它像预期的那样工作，因为它是 GCP。

## Azure Web Apps

糟糕，由于内部服务器错误，所有请求都失败了。原因是认证。如果在 GCP 境内访问存储，GCP 会处理存储的身份验证，否则我需要创建服务帐户。

### 添加认证

目前，我只是用自己的凭证来测试应用程序。这在测试时是可以的，但是当我部署应用程序时，我需要启用身份验证。

1.运行以下命令创建服务帐户。我给了项目一个答案，但是您可以根据场景进一步限制它。

```
serviceName=storageserviceaccount
projectId=<your project id>
gcloud iam service-accounts create ${serviceName}
gcloud projects add-iam-policy-binding ${projectId} --member "serviceAccount:${serviceName}@${projectId}.iam.gserviceaccount.com" --role "roles/owner" 
```

Enter fullscreen mode Exit fullscreen mode

2.然后运行以下命令来生成密钥。

```
gcloud iam service-accounts keys create keys.json --iam-account ${serviceName}@${projectId}.iam.gserviceaccount.com 
```

Enter fullscreen mode Exit fullscreen mode

3.文件生成后，复制到本地，添加到 Visual Studio 2017 项目根目录。

[![key](img/262f60b921dc8dd1bd5bf1e9631844c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5tvvwngK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bw6lchvy60mikv55ulrz.PNG)

4.更改 StorageClient 初始化代码以传递密钥。

```
StorageClient storageClient = StorageClient.Create(GoogleCredential.FromFile("keys.json")); 
```

Enter fullscreen mode Exit fullscreen mode

5.重新部署到 Azure。

6.再次测试。这一次，它成功了。

当我们考虑安全性时，这肯定不是一个好主意。我们可能想把文件保存在安全的环境中，但是我把它留到以后的文章中。

# 总结

对比 Azure 和 GCP 存储，从开发体验上看，两者非常相似。身份验证略有不同，但大多数功能是相似的，包括 SLA、位置和类型。我应该更深入地研究认证部分。

肯恩(男名)