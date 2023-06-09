# Nylas API 联系人 CRUD 现在支持 iCloud

> 原文：<https://dev.to/nylas/nylas-api-contacts-crud-now-supports-icloud-2ch3>

[![Contacts API](img/f6fd1c420740433d4cc18bdf669051f9.png)T2】](https://www.nylas.com/blog/nylas-api-contacts-crud-now-supports-icloud)

之前，我们 <u>[写过](https://www.nylas.com/blog/lessons-learned-syncing-800-million-contacts-to-our-database)</u> 关于我们发布 Contacts v2.0 升级的开发过程，该升级允许开发人员将通讯录(如 Gmail 联系人)中的联系人资料同步到具有完整 CRUD 功能的 SaaS 应用程序中。最初的版本允许开发者从 Gmail、微软 Exchange、Outlook.com 和 Office 365 账户同步联系人。

我们正在继续构建这些功能，以服务更多类型的帐户，我们最近发布了 iCloud 联系人的双向同步和 CRUD。现在有了对 iCloud 联系人的支持，开发人员可以与更多 CRUD 支持的信息密集型联系人进行交互:

**在**之前

[![null](img/a5d5d352449534db48dec57486acfd90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mvwD2kVx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_179522ABF38A809781EC8AE8FF03D3C967C450F67739F5BA854697C9629F4D7A_1532631164280_Screen%2BShot%2B2018-07-26%2Bat%2B11.52.27%2BAM.png)

之后

 **[![null](img/3ff4ebb08ced8dc5357a06f3423443bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BRZIv8uL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_179522ABF38A809781EC8AE8FF03D3C967C450F67739F5BA854697C9629F4D7A_1532631041086_Screen%2BShot%2B2018-07-26%2Bat%2B11.50.17%2BAM.png)

在为 iCloud 集成 Contacts v2.0 时，我们遇到了一些挑战，包括多部分格式、类型映射以及一次性适应多部分格式和类型映射的代码库复杂性管理:

**多部分格式**

在构建过程中，我们面临的一个重大障碍是不同联系人提供商和我们的联系人模型之间的转换。每个联系人提供商都有自己收集联系人数据的系统和格式。谷歌为其名为`gdata`的联系人 API 提供了一个简单明了的接口，Exchange Active Sync 通过“设备”使用标记化的 XML，iCloud 遵循 IETF 标准 vCard。

iCloud 实现的 <u>[vCard](https://en.wikipedia.org/wiki/VCard)</u> 格式是这样的:

`BEGIN:VCARD`

`VERSION:3.0`

`UID:123456789`

`N:Appleseed;John;;;`

`FN:John Appleseed`

`NOTE:Good notes`

`PHOTO;VALUE=uri;X-ABCROP-RECTANGLE=ABClipRect_1&0&205&1115&1115&hP6x6elkonMVk3k9ibig1g==:[https://p41-contacts.icloud.com:000/1234567/wbs/abc123](https://p41-contacts.icloud.com:000/1234567/wbs/abc123)`

`item1.ADR;TYPE=WORK;TYPE=pref:;;695 Minna St;San Francisco;CA;;United States`

`item1.X-ABADR:us`

`TEL;TYPE=CELL;TYPE=pref;TYPE=VOICE:(415) 604-9500`

`item2.URL;TYPE=pref:[http://nylas.com](http://nylas.com)`

`item2.X-ABLABEL:*$!<HomePage>!$*`

`X-AIM;TYPE=HOME;TYPE=pref:aimaddress`

`IMPP;X-SERVICE-TYPE=aim;TYPE=HOME;TYPE=pref:aim:aimaddress`

`EMAIL;TYPE=WORK;TYPE=pref;TYPE=INTERNET:[info@nylas.co](mailto:info@nylas.co)m`

`REV:2018-01-01T00:00:00Z`

`END:VCARD`

此外，单个 vCard 中的各个部分在格式上有细微的不同。以`ADR`为例:它遵循{邮局地址}的格式；{扩展地址}；{ Street }；{位置}；{ Region }；{邮政编码}；{Country}，”但当 iCloud 与 vCard 格式接口时，前两部分，邮局地址和扩展地址，总体上被忽略。

`item1.ADR;TYPE=WORK;TYPE=pref:;;695 Minna St;San Francisco;CA;;United States`

当直接与 vCard 格式交互时，有许多其他关于多部分格式的微小细节会显著减慢开发过程。我们了解到，界面文档(例如 vCard 格式)只能带你走这么远，因为实现(例如 iCloud)遵循其他提供商没有的细节，而补救这一点需要测试尽可能多类型的联系信息。通过测试过程，我们还发现每个联系人中包含分号的任何姓名或地址总是不正确的。

**类型映射**

作为另一个增加翻译复杂性的因素，我们了解到除了奇怪的格式细微差别之外，iCloud 字段还具有有些不直观的类型映射。

还记得之前的 IMAP 例子吗？

`IMPP;X-SERVICE-TYPE=aim;TYPE=HOME;TYPE=pref:aim:aimaddress`

您会注意到 URI 值`aim:aimaddress`也在前面加上了一个协议和冒号；在这种情况下，它是`aim.`而不是从 IMAP 服务到协议的一对一映射，这种映射有时是多对一的:AIM 和 ICQ 都使用 OSCAR 协议，iCloud 将此协议标记为`aim`。同样，QQ 和 Gadu-Gadu 都贴有`x-apple`协议标签。

虽然 vCard 字段像 TEL 一样用类型标记类型，但 IM 地址不同，因为它们有更多的“类型”最重要的是提供者，它标有 X-SERVICE-TYPE。

`IMPP;X-SERVICE-TYPE=aim;TYPE=HOME;TYPE=pref:aim:aimaddress`

另一个例子是电话类型映射:在 iCloud 联系人上标记为“家庭传真”的电话被表示为 `TYPE=HOME;TYPE=FAX` 而不是`TYPE=HOME FAX`。一部标有“工作传真”的电话也是如此:它被标为`TYPE=WORK;TYPE=FAX`，而不是`TYPE=WORK FAX`。

确定这些复杂的映射需要对拥有多种不同类型的电话号码和 IM 地址的单个联系人进行测试。这使得绕过这些障碍变得更加容易。

**管理代码库复杂性**

我们对不同领域和类型进行一次性调整的代码库的增长是接口和实现之间脱节的二阶效应。这使得我们的代码审查更加困难，因为 iCloud 联系人同步与 vCard 和 contact 之间的翻译混合在一起，所以没有明确的任务授权。

大约 2/3 的 iCloud v2.0 upgrade 的开发就是这样翻译的。前半部分是从 iCloud 中提取联系人，当反过来翻译 CRUD 功能时，这种复杂性增加了一倍。我们意识到我们需要一个单独的适配器来满足双向翻译的需要。

通过创建一个专门用于 iCloud translation 的适配器来分离同步和翻译功能，减少了 60%的同步逻辑代码，使我们的系统更加模块化。这也让我们的代码审查变得更加容易，这是我们喜欢的！

但是，为我们的客户降低所有这些复杂性也意味着使用 Nylas API 消除了与大型协议的特定实现细节进行交互的噩梦，并代之以简单愉快的体验。

我们很乐意让您尝试一下:前往我们的 <u>[API 文档](https://docs.nylas.com/reference#contacts-intro)</u> 或<u>[one](https://www.npmjs.com/package/nylas)</u><u>[of](https://www.npmjs.com/package/nylas)</u><u>[我们的](https://www.npmjs.com/package/nylas)</u>SDK 开始使用。

[![](img/f7cf2b66a69fcf80649c37c5f5a7f166.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E-SsZnGo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://track.hubspot.com/__ptq.gif%3Fa%3D3314308%26k%3D14%26r%3Dhttps%253A%252F%252Fwww.nylas.com%252Fblog%252Fnylas-api-contacts-crud-now-supports-icloud%26bu%3Dhttps%25253A%25252F%25252Fwww.nylas.com%25252Fblog%26bvt%3Drss)**