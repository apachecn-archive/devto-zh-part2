# 转移 iOS 应用程序的副作用

> 原文：<https://dev.to/brightdevs/side-effects-of-transferring-ios-application-n7n>

当你出售你长期开发的应用程序的时候，或者你将要接管一个应用程序的时候，你可能不关心这个过渡过程。虽然苹果公司[已经详细描述了这些步骤](https://help.apple.com/itunes-connect/developer/#/deved688524f)，但是你应该在开始过渡之前考虑潜在的后果。

你还应该确保申请[符合所有标准](https://help.apple.com/itunes-connect/developer/#/devaf27784ff)并且**有资格转让**。最重要的是，该应用程序必须至少有一个版本已经发布到 App Store，并且该应用程序的任何版本都没有使用 **iCloud 授权**或 **Passbook 授权**。

## 备份数据

一旦应用程序被移动到一个新的帐户**，它在旧的帐户**上就不再可用。这意味着发起者将丢失关于应用程序的所有信息，包括元数据、在应用程序商店上可用的日期、定价、销售或下载统计数据。您应该备份所有这些信息作为记录，因为在转换后不可能再恢复。

## 准备好自己

[![image](../Images/caa087c91a9175d9382d78c2abdda28a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I5aoylsU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2olaoznffjiqxo6pyl66.jpg)

如果您的应用程序使用这些特性中的任何一个，您需要采取一些额外的操作:

*   **自动续费订阅** -如果一个应用程序使用这种订阅，那么可能会有[验证它们的有效性](https://developer.apple.com/library/content/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html#//apple_ref/doc/uid/TP40010573-CH104-SW1)(例如后端)。为了确保接收者能够在转移期间验证订阅，发起者需要在发起转移之前生成特定于应用的共享秘密**，并与接收者共享代码，以便他们可以设置自己的验证。应用程序传输完成后，接收方应生成一个新的共享密钥，这样组织外的用户就无法再访问它。**

*   **Apple Pay** -商家 ID 不随 app 一起转移。只要原始证书有效，交易就会继续成功。提交更新时，收件人需要生成一个新的商家 ID。

*   **钥匙链** -在提交过渡后的第一次更新时，您将收到一封关于**可能丢失钥匙链访问权限**的电子邮件警告。应用程序转换后，团队 ID 发生变化，因此应用程序标识符从`[old-team].pl.brightinventions.app`变为`[new-team].pl.brightinventions.app`，这导致钥匙串访问丢失。用户更新他们的应用程序后，它不会找到认证令牌并要求重新登录。钥匙串共享还会继续工作，直到应用程序更新并需要替换为收件人创建的钥匙串群组。

*   **推送通知** -推送通知的客户端 SSL 证书不会被传输，因此接收方需要创建自己的证书并上传到他们的推送通知服务(后端、Firebase 等)。).

此外，如果一个应用程序是跨应用程序**多人兼容矩阵**的一部分，它将不再与其他应用程序兼容或出现在其他应用程序的矩阵中。同样，如果某个应用程序是**应用程序捆绑包**的一部分，您将无法再查看该应用程序捆绑包的历史。

## 这需要时间

[![image](../Images/9347ff27d98290e0ed5d3571a48dec89.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wtZFIvLz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vl4ihh0uyjkmfrjo4lji.jpg)

处理应用程序转移可能需要两个工作日。在此期间，你将无法编辑应用元数据、权限、定价和应用内购买，因此你应该确保在此期间不需要任何紧急更改。

转移应用程序对用户来说**是不可察觉的**，适当的准备不应该以任何方式影响他们。但是，您应该更新**服务条款**和**数据保护**条件，以便它们符合新的所有者政策。

将应用程序转移到另一个帐户并不困难，但需要了解流程并做一些准备，特别是如果你使用推送通知、钥匙链、Apple Pay、自动续订订阅或当你的应用程序是跨应用程序多人兼容性矩阵或应用程序捆绑包的一部分时。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Mateusz Klimczak,《推动事物前进》@光明的发明

[推特](https://twitter.com/Klimczak_M)，[邮件](//mateusz.klimczak@brightinventions.pl)