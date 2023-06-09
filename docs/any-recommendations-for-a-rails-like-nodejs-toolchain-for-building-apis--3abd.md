# 对于构建 API 的类似 Rails 的 NodeJS 工具链有什么建议吗？

> 原文：<https://dev.to/mful/any-recommendations-for-a-rails-like-nodejs-toolchain-for-building-apis--3abd>

回顾 NodeJS 生态系统，似乎有许多类似功能的竞争工具——Knex vs water line vs Sequelize for ORMs；sails vs Express vs Trails vs for base 框架；d b-migrate vs sails-migrations vs Knex migration 支持数据库迁移；等等。

对于 Rails 世界来说，在工具上存在(相对)共识，这有点让人不知所措。

基本上，我在寻找一个支持以下功能的工具链:

*   数据库迁移
*   良好的 PostgreSQL 支持
*   ORM
*   支持构建 JSON APIs
*   秘密管理(nconf)似乎很有前途...)

从人体工程学的角度来看，我寻求以下几点:

*   集成—各个部分应该很容易一起播放
*   MVC-ish(这里的“V”是 JSON，但是仍然...)
*   生产安全(特别是对于 SQL 数据库——似乎很多 NodeJS tuts 都假定是 NoSQL 数据库，因此忽略了模式版本控制和迁移之类的东西)

我一直在摆弄 SailJS，喜欢它背后的一些想法，但是发现它缺乏 SQL 特有的一切(或者至少，在如何处理迁移等问题上似乎没有达成一致意见)。

鉴于上述情况，你会推荐哪种工具链呢？任何和所有的帮助在这里将不胜感激！