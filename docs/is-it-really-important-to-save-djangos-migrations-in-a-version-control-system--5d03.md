# 在版本控制系统中保存 django 的迁移真的很重要吗？

> 原文：<https://dev.to/simo97/is-it-really-important-to-save-djangos-migrations-in-a-version-control-system--5d03>

大家好，我和我的团队(我们一共 4 个人)在一个 django 项目上工作，任何开发人员都可以对模型进行修改，然后生成他的迁移，但有时在合并后部署应用程序真的很困难(即使是为了测试)，一些原因是: ***XXX_migration_XXX 已经在他的依赖 XXX_migration_XXX*** 之前应用，这是因为不同数据库和不同迁移之间的不一致。

所以我开始思考，为了避免这种情况，我们应该停止迁移版本化。你怎么想呢?你有没有遇到过这种迁移混乱的情况，(意大利面条式的迁移；-) )?

PS:对不起，我的英语不好