# 如何通过组策略部署电子邮件签名

> 原文：<https://dev.to/jcoelho/how-to-deploy-email-signatures-through-group-policies-4aj1>

几年前，我在一家大公司担任系统管理员，这家公司正在经历一个重塑 T2 形象的过程。品牌重塑的目标之一当然是**公司的电子邮件签名**，400 多名员工中的大多数**都在使用这个签名。**

那么，如何为所有员工部署新的电子邮件签名呢？你会给他们发邮件，让他们自己做吗？好吧，这是一个选择，但是你将不得不处理来自不懂技术的员工的不可阻挡的帮助请求。

一定有更好的方法。

## 组织政策救援

让我在这里设置一下场景，我在一个有大约 400 个 Windows 工作站的网络中工作，并且大部分基础设施也是基于 Windows 的。所以对我来说很清楚[组策略](https://en.wikipedia.org/wiki/Group_Policy)是正确的选择。

我很快想到一个计划，开发一个 PowerShell 脚本，可以将签名复制到用户的工作站，并在用户每次登录时运行。

### 1。创建签名模板

我得到了新的电子邮件签名，我必须创建一个模板，以便我的 PowerShell 脚本稍后可以填充信息。

这是我的签名模板(`.html`文件)，占位符存储在所有用户都可以访问的共享位置:

[![template](../Images/62b78b9754214fd70ca043528da90116.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4YKNugbz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uahgkygmndud9cct2sty.png)

### 2。PowerShell 脚本

我需要一个脚本来替换每个用户的用户特定信息，在这种情况下，替换用户的**姓名**、**职位**、**部门**、**电子邮件**、**办公室和电话号码**。

```
# Gets the path to the user appdata folder  $AppData  =  (Get-Item  env:appdata).value  # This is the default signature folder for Outlook  $localSignatureFolder  =  $AppData+'\Microsoft\Signatures'  # This is a shared folder on your network where the signature template should be  $templateFilePath  =  "\\server\signatures"  # Get the current logged in username  $userName  =  $env:username  # The following 5 lines will query AD and get an ADUser object with all information  $filter  =  "(&(objectCategory=User)(samAccountName=$userName))"  $searcher  =  New-Object  System.DirectoryServices.DirectorySearcher  $searcher.Filter  =  $filter  $ADUserPath  =  $searcher.FindOne()  $ADUser  =  $ADUserPath.GetDirectoryEntry()  # Now extract all the necessary information for the signature  $name  =  $ADUser.DisplayName  $email  =  $ADUser.mail  $job  =  $ADUser.description  $department  =  $ADUser.department  $phone  =  $ADUser.telephonenumber  $office  =  $ADUser.physicalDeliveryOfficeName 
```

Enter fullscreen mode Exit fullscreen mode

现在我已经获得了所需的关于用户的所有信息，我必须替换签名模板上的信息，并将其复制到用户的本地文件夹中。

```
 $namePlaceHolder  =  "[DISPLAY_NAME]"  $emailPlaceHolder  =  "[EMAIL]"  $jobPlaceHolder  =  "[JOB_TITLE]"  $departmentPlaceHolder  =  "[DEPARTMENT]"  $phonePlaceHolder  =  "[PHONE]"  $rawTemplate  =  get-content  $templateFilePath"\template.html"  $signature  =  $rawTemplate  -replace  $namePlaceHolder,$name  $rawTemplate  =  $signature  $signature  =  $rawTemplate  -replace  $emailPlaceHolder,$email  $rawTemplate  =  $signature  $signature  =  $rawTemplate  -replace  $phonePlaceHolder,$phone  $rawTemplate  =  $signature  $signature  =  $rawTemplate  -replace  $jobPlaceHolder,$job  $rawTemplate  =  $signature  $signature  =  $rawTemplate  -replace  $departmentPlaceHolder,$department  # Save it as <username>.htm  $fileName  =  $localSignatureFolder  +  "\"  +  $userName  +  ".htm" 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是将签名复制到用户的本地文件夹:

```
 # Gets the last update time of the template.  if(test-path  $templateFilePath){  $templateLastModifiedDate  =  [datetime](Get-ItemProperty  -Path  $templateFilePath  -Name  LastWriteTime).lastwritetime  }  # Checks if there is a signature and its last update time  if(test-path  $filename){  $signatureLastModifiedDate  =  [datetime](Get-ItemProperty  -Path  $filename  -Name  LastWriteTime).lastwritetime  if((get-date  $templateLastModifiedDate)  -gt  (get-date  $signatureLastModifiedDate)){  $signature  >  $fileName  }  }else{  $signature  >  $fileName  } 
```

Enter fullscreen mode Exit fullscreen mode

### 3。我们得到了一个签名

如果该脚本成功运行，您应该会在 AppData 文件夹中得到一个完整的签名，如下所示:

[![signature](../Images/cf306e77794281fae834d71678ed76ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5pZWeRRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t9wucevb5xxpj28649py.png)

### 4。设置组策略

最后，我设置了一个新的组策略，让这个脚本在每次用户登录时运行。

需要一段时间，直到所有用户都有了更新的签名，因为他们中的一些人在度假，一些人很少登录。

这就是我喜欢 PowerShell :D 的原因

这是我的第一篇帖子...如果你愿意，请给我留下一些反馈；)