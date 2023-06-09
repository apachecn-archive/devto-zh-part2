# 单元测试:我们可以在 Salesforce Apex 中模拟关系吗？

> 原文：<https://dev.to/matheusgoncalves/unit-test-can-we-mock-relationships-in-salesforce-apex-4ohb>

我一直试图利用一些业余时间来帮助我们社区中的 Salesforce 开发人员。最好的地方之一是 [Salesforce Stack Exchange](https://salesforce.stackexchange.com) 。

而前几天有人贴出了[下面的问题](https://salesforce.stackexchange.com/questions/215326/can-we-mock-relationships-in-apex/):

> 如何在不实际执行查询的情况下编写测试方法(以加快测试运行时间)？据我所知，返回超过一层的关系的唯一方法是查询它们。我知道 ApexMock 有 IDGenerator，但不知道如何在这个用例中使用它？

那么，我们可以在 Apex 中模拟关系来提高测试的性能吗？

而答案是**是的，有可能**。我将解释如何逐步实现这一点，尽量简化这个概念。

如果使用模拟数据层模式，几乎可以模拟任何关系。

想象一下开发者[在问题](https://salesforce.stackexchange.com/questions/215326/can-we-mock-relationships-in-apex/)中提出的场景，你可以给你的主类添加一个接口。姑且称之为 IDataLayer 吧。将这个新接口添加到主类中(这里称为 **DataLayerHandler.cls**