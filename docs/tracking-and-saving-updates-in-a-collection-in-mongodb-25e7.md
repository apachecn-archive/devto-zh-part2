# 在 MongoDB 中跟踪和保存集合中的更新

> 原文：<https://dev.to/samuanv/tracking-and-saving-updates-in-a-collection-in-mongodb-25e7>

大家好！我叫 Samuel，是一名年轻的 web 开发人员。事实上，我在一个项目中工作，帮助我家的生意。该系统是关于使用 QR 码对车辆进行访问控制。它由一个连接到 noSQL DB (MongoDB)的 web 应用程序(Angular6)、移动应用程序(Ionic3)和 REST API (NodeJS)组成。

我的一个集合是 Reservations，文档的结构如下:

`{
_id: number,
serviceId: number,
datetimePaid: Date,
responsible: Person,
state: string,
tickets: Tickets[],
}`

门票就像是:

`{
price: number,
passenger: Person,
roundtrip: boolean
}`

具有不同角色(具有更高或更低权限)的用户可以修改预订，但我需要跟踪用户何时修改了文档的某些字段，以便为管理员提供解决可能问题的好方法。
可以更新的操作或字段有:

*   datetimePaid
*   负责任(换成另一个人)
*   状态
*   门票[我]。乘客
*   在票证数组中添加或删除元素

我还没有找到 MongoDB 提供的任何本地解决方案，我有一些想法，但我想听听你的策略或方法。

先谢过了