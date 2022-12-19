# 签署一份协议并上传到 Nuget.org

> 原文：<https://dev.to/jeikabu/signing-a-nupkg-and-uploading-to-nugetorg-2a98>

几天前，我详述了如何创建一个包含本地库的 nupkg。接下来我们将签名并上传到 nuget.org。

Nuget.org 也有一篇关于这个话题的精彩文章。

动机:

*   让我们自己的图书馆更容易被他人使用。以前我们提供了关于克隆 repo 并将其引入现有项目的说明——使用 nuget 会更容易。
*   简化我们的代码库。我们的主要。sln 有 55 个项目；其中可能有 10 个是第三方库，我们对它们做了一些小改动，但很少涉及。

## 组织

我们希望我们所有的包都归 [Subor 组织](https://www.nuget.org/profiles/subor)所有，这样一切都不会被捆绑到一个人身上。

创建组织后(档案 **- >管理组织- >新增**)，可以点击铅笔图标进入组织的设置页面，配置各项:

[![](../Images/c985d11f85890a688fea4a95282b1430.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---YmrLTdS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/nuget_edit_icon.png)

## 证书创建和注册

假设您有一个用于签名的 *PKCS #12* 文件(扩展名为. pfx ),您需要导出一个. cer 来注册 nuget.org。在 Windows 上，双击 pfx 应该会启动证书导入向导，或者您可以运行`certmgr.exe`并单击**导入…** 。

导入后，选择密钥，点击**导出…** :

[![](../Images/d6ba7ce8081c18e8a553c2da2db0cb83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hzb2qRkr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/certmgr_export.png)

出现提示时选择**否，不导出私钥** :

[![](../Images/126831acd99eb66300eff8894192f414.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xvMVBa3x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/certmgr_export_no_private.png)

以及 **DER 编码的 X.509** :

[![](../Images/4338f0d01b0282eb0bc2cc639d90bc1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qNrVDq1E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/certmgr_export_format.png)

然后点击创建. cer。现在，进入 nuget.org 的组织设置页面，选择**证书- >注册新的**。选择。您刚刚创建的 cer。

## Nuget.org API 密钥

当您访问 nuget.org 时，值得创建一个 API 键**配置文件- > API 键->+创建** :

[![](../Images/e5df59a8c53a63cb8f4a3568cb340a7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xgXhR5Tq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/nuget-org_apikey_create.png)

对于**包所有者**选择组织。上传的软件包将属于组织，而不是您的个人帐户。

对于**选择范围**,我已经得到了**只推新的包版本**,因为我打算在我们的构建机器上使用它，它真的没有理由创建新的包。

点击**创建**然后**复制**将 API 密匙保存到你的剪贴板。您以后不能再获得此密钥；如果你忘记了，你只需要**重新生成**它(使旧的失效):

[![](../Images/572214d53d492ed63e02d6a4ccd37f4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Pfa_2OW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/nuget-org_apikey_regenerate.png)

## 签署

我已经从前几天的那里得到了`Subor.NNanomsg.NETStandard.0.5.2.nupkg`和我们的私钥(pfx)。要签署包:

```
nuget.exe sign Subor.NNanomsg.NETStandard.0.5.2.nupkg -Timestamper http://sha256timestamp.ws.symantec.com/sha256/timestamp -CertificatePath path_to_private_key.pfx 
```

输出应该类似于:

```
Please provide password for: path_to_private_key.pfx
Password: ********************************

Signing package(s) with certificate:
  Subject Name: CN=???????????????????, O=???????????????????, L=??, C=CN
  SHA1 hash: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
  SHA256 hash: BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB
  Issued by: CN=DDDDDDDDDDDDDDD SHA256 Code Signing CA, OU=EEEEEEEEEEEEEEEEE, O=FFFFFFFFFFFFFFFFFF, C=US
  Valid from: 12/34/56 00:00:00 to 12/34/56 00:00:00

Timestamping package(s) with:
http://sha256timestamp.ws.symantec.com/sha256/timestamp
Package(s) signed successfully. 
```

签名后，nupkg 文件的大小应该会略有增加(在本例中为 10KB)。

如果您得到以`Key does not exist.`结尾的输出，请确保。pfx(私钥)跟在`-CertificatePath`后面，而不是。公共密钥。

您可以通过以下方式验证包:

```
nuget.exe verify Subor.NNanomsg.NETStandard.0.5.2.nupkg -All 
```

而且应该还有一堆类似的以`Successfully verified package 'Subor.NNanomsg.NETStandard.0.5.2'.`结尾的输出。

## 上传

我第一次上传这个包时使用了 nuget.org 的 web 界面:profile**->->+Add new**。

此后，我可以使用我创建的仅限“更新包版本”的 API 密钥来推送更新:

```
nuget.exe push Subor.NNanomsg.NETStandard.0.5.2.nupkg -Source "https://www.nuget.org" -ApiKey abcdef01234567890abcdef01234567890 
```

如果一切正常:

```
Pushing Subor.NNanomsg.NETStandard.0.5.2.nupkg to the NuGet gallery (https://www.nuget.org)...
  PUT https://www.nuget.org/api/v2/package/
  Created https://www.nuget.org/api/v2/package/ 3158ms
Your package was pushed. 
```

该包在通过 nuget.org 可用之前经过验证。对于像这样的小包裹来说，这似乎需要 5 分钟左右，尽管我想对于大包裹或者在高峰使用期间可能需要更长时间。你可以通过这个包的页面关注它的状态:[https://www.nuget.org/packages/Subor.NNanomsg.NETStandard/0.5.2](https://www.nuget.org/packages/Subor.NNanomsg.NETStandard/0.5.2) 。

## 这个圈现在完成了

回到 Visual Studio，您现在可以:

1.  右键一个项目 **- >管理 NuGet 包…- >浏览**。
2.  确保**包源**是`nuget.org`(以防你在包开发过程中改变了它)。并搜索`Subor.NNanomsg.NETStandard` :

    [![](../Images/b11248db60591ed2c508b9ceeba2fb9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9-DRMQ3_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/vs_nuget_browse.png)

3.  **安装** it 并构建项目(别忘了[设置**平台目标**](./nupkg-containing-native-libraries-1576#consuming-nupkg) ！).

4.  浏览到输出文件夹，`nanomsg.dll`应该在那里。

相当狡猾。