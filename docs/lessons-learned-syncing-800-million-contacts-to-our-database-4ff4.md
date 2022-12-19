# 将 8 亿联系人同步到我们的数据库的经验教训

> 原文：<https://dev.to/nylas/lessons-learned-syncing-800-million-contacts-to-our-database-4ff4>

Nylas APIs 允许开发人员将电子邮件、日历和联系人功能构建到他们的应用程序中。我们的目标是完全支持这三点，但是直到最近，我们的 Contacts API 还没有我们想要的全部功能。Nylas 是我的第一份全职工程工作，加入后不久，这种升级 Contacts API 的需求再次浮出水面。这是一个很大的任务，但我很高兴承担这个任务。在这篇博文中，我将详细介绍这个问题，我们提出的解决方案，以及我由此学到的一些东西。

## 问题

我们的 Contacts API 为客户(大多数是 B2B 软件公司)提供了只读功能，允许他们获得包含三个字段的联系人列表:姓名、电子邮件和电话号码。我们希望让他们能够访问更多的数据，这样他们就可以让他们的用户更好地与同事、同事、候选人等建立联系。为此，他们需要能够读取、同步、更新和自动完成数据，包括联系地址、多个电子邮件地址、生日、职位等。

除了我们正在构建的所有其他功能之外，改变我们的产品以同步和存储大量新数据而不影响现有客户是团队要应对的一个巨大挑战，但为了我们的客户，这是一个值得接受的挑战。

## 解

解决方案的第一个组成部分是扩展我们的联系模型来存储更多的字段。下面是旧的 Contact v1.0 版和新的 Contact 2.0 版的比较。这两个版本都有系统分配的字段`id`、`account_id`和`object`。除此之外，我们显著增加了新的和改进的联系模型的联系字段的数量和粒度。

**联系 v1.0:**

```
[  {  "account_id":  "x2x2x2x2x2x2x2x2x2x2x2",  "email":  "john@doe.com",  "id":  "z3z3z3z3z3z3z3z3z3z3z3",  "name":  "John Doe",  "object":  "contact",  "phone_numbers":  [  {  "number":  "1 800 123 4567",  "type":  "mobile"  }  ]  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

**联系 v2.0:**

```
[  {  "account_id":  "x2x2x2x2x2x2x2x2x2x2x2",  "birthday":  "1960-12-31",  "company_name":  "Nylas",  "emails":  [  {  "email":  "john@doe.com",  "type":  "work"  }  ],  "given_name":  "John",  "id":  "z3z3z3z3z3z3z3z3z3z3z3",  "im_addresses":  [  {  "im_address":  "myaimaddress",  "type":  "aim"  }  ],  "job_title":  "Software Engineer",  "manager_name":  "Bill the manager",  "middle_name":  "Jacob",  "nickname":  "JD",  "notes":  "Loves ramen",  "object":  "contact",  "office_location":  "123 North Pole Dr",  "phone_numbers":  [  {  "number":  "1 800 123 4567",  "type":  "mobile"  }  ],  "physical_addresses":  [  {  "format":  "structured",  "type":  "home",  "street_address":  "200 Santa Clause Ln",  "city":  "North Pole",  "postal_code":  "123123"  "state":  "CA",  "country":  "USA"  }  ],  "picture_url":  "https://api.nylas.com/contacts/427abc427abc427abc/picture",,  "suffix":  "Jr.",  "surname":  "Doe",  "web_pages":  [  {  "url":  "johndoeblog.com",  "type":  "blog"  }  ]  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

我们还为联系人扩展了 API 端点和底层功能。借助 Contacts v2.0，客户能够从自己的应用程序中创建和删除联系人。

以下是我们在联系人 2.0 版中新增的端点:
[![contacts v2 functionality](../Images/97528ee5ef28931dc7894c82d175feb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zljaad47--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nylas.com/hs-fs/hubfs/blog%2520images/Contacts%2520v2.0%2520-%2520Technical/contactsv2-functionality.png%3Ft%3D1518652965690%26width%3D558%26name%3Dcontactsv2-functionality.png)

这项新功能可以以多种不同的方式使用，每种方式都可以简化我们的客户日常面临的问题。例如，在 Contacts API 中自动创建新的联系人记录减少了销售、营销和招聘团队在 CRM 中手动创建新联系人的需要，因为这些数据会在后台从收件箱无缝同步到 CRM。

我们还增加了根据各种参数对联系人进行排序的功能。例如，通过邮政编码:我想知道所有在邮政编码 94105 工作的销售代表，通过电话号码:谁打电话给我？，通过电子邮件:显示此电子邮件的联系人，等等…

查看 Contacts v2.0 [文档](https://docs.nylas.com/reference#contacts-intro)了解更多关于新功能和可用端点的信息。

## 我的外卖

### 测试您的产品。

编写好的测试很重要，但是真正使用你的产品也很重要。当我认为我已经完成了我的 Contacts v2.0 工作时，我开始更新我们的 Nylas NodeJS SDK 以支持新功能。这个 SDK 是我们 API 的 NodeJS 包装器，它使得将 Nylas API 集成到 Javascript 应用程序中变得更加容易。对于 SDK，我必须添加使其能够访问新端点的功能，为这些功能编写测试，并扩展 SDK 中的示例应用程序以包括 Contacts v2.0 功能。

更新 SDK 的过程意味着我必须使用我刚刚添加到 Contacts 2.0 API 中的所有新特性和功能。这让我看到了以前未知的错误。例如，我试图用来自我们的 API 的联系数据填充 NodeJS SDK 的联系模型，除了 middle_name 字段之外，一切都正常。原来我忘记了为 contact GET 请求编码并返回 middle_name 字段。

实际上，使用该产品也让我看到了虽然没有损坏，但用户体验很差的流程。例如，我们的 API 曾经要求联系人生日的 JSON 格式为“生日”:{“日期”:“1995-01-13”，“对象”:“日期”}。将日期包装到这个对象中是不必要的，只是增加了更多的工作来将联系人的生日表示为 SDK 中的日期。我把生日字段简化为“生日”:“1995-01-13”。

> 不得不在 Contacts v2.0 API 之上进行开发暴露了一些缺陷，并让我站在客户的角度来看设计缺陷，否则我不会注意到这些缺陷。

### 你没有必要，也不应该一次完成大项目。

Contacts v2.0 是一个重要的全栈项目，它影响了我们代码库的很大一部分。

*   我们必须扩展数据库中以前的 contacts 表，并创建许多相关的表来支持我们现在要存储的大量新数据。
*   我们必须在提供商和我们的系统之间建立双向数据同步。
*   我们必须构建新的 API 端点和底层功能来支持新的创建、更新和删除特性。

因此，我们知道我们需要在生产中进行广泛的测试。遇到各种各样的边缘情况的唯一方法是在生产中同步真实的客户数据，这意味着我们必须在不影响现有客户的情况下将可能有错误的代码部署到 prod。为了实现这一点，我们在特性标志下发布了代码。这意味着我们可以定期以可管理的片段部署代码，而不会向客户暴露任何更改。一旦项目接近完成，我们举行了一次公开测试，让一些感兴趣的客户尝试和测试新功能。在正式发布之前，该代码已经在生产中运行了 4 个多月，一些客户已经测试了它并给了我们反馈。

> 在整个开发过程中，在一个特性标志下发布代码，并让客户进行 beta 测试，这让我在正式发布之前对代码有了信心。

### API 版本的权衡

我们处理这个特性标志的方式是对我们的 API 进行版本控制，并保持我们开发的新版本是私有的。对 API 进行版本化的决定，从而使我们能够做出突破性的改变，是具有挑战性和有趣的。

一方面，API 版本控制令客户望而生畏。这在客户第一次更新版本时尤其如此。他们可能会对新版本保持警惕，不确定更新版本是否值得付出额外的工程努力。

另一方面，对我们的 API 进行版本控制可以让我们在保持产品整洁的同时更好地满足客户的需求。让客户习惯于更新 API 版本将使我们的工程重点完全放在构建新功能上，而不是支持遗留功能。版本控制还为我们的客户提供了一个简单的升级途径，让他们能够控制应用程序的更新。

API 版本控制的利弊取决于您的产品以及新版本将给客户带来的好处。对这个决定的推理迫使我从几个不同的角度考虑 API 版本控制:

*   对我们产品的未来最有利的是什么？
*   什么最有利于客户体验？
*   最清洁、最可持续的工程解决方案是什么？

> 这次经历拓宽了我对如何做设计决策的理解。

这是我在 Nylas 的头六个月要解决的一个大项目，但我真的对它的结果感到兴奋。事实上，我能够做一些直接影响我们客户的事情，这让我在这个过程中了解了很多他们的需求。正如我将在未来的帖子中分享的那样，这一经历帮助我成长为一名工程师，并在此过程中更多地了解了自己。查看 [Contacts v2.0](https://docs.nylas.com/reference#contacts-intro) 并让我们知道您的想法！

* * *

这篇博文最初发布在 [Nylas 工程博客](https://www.nylas.com/blog/lessons-learned-syncing-800-million-contacts-to-our-database)上