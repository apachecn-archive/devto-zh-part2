# 修复 Rails 迁移的混乱

> 原文：<https://dev.to/savfischer/fixing-a-rails-migration-snafu-1fm6>

我和现有客户一起为一个 Rails 应用程序开发了一个巨大的新功能，已经有两个月了。这一新功能有大约 10 个新的数据库迁移，包括回滚和编辑最后一次推向生产环境的迁移，以及重命名这些表。那是个错误。如果两个月后，我记得在执行新的迁移之前回滚最近的生产迁移，一切都会好的。

唉，我没有。显然，任何要求你记住从现在起两个月后做某事的编程更改都是不好的，尤其是如果你在部署之前没有系统提醒你自己。当我将该特性推向生产时，我的新迁移运行得很好，两天后，当我的代码假设一个数据库表存在时，我开始收到关于它不存在的错误。我很快意识到，大约 10 个迁移文件的迁移是错误的根源，我在开发中回滚并重命名了表，但在生产中没有回滚。

更糟糕的是，我现在生产中有一堆数据，这些数据依赖于在问题迁移文件之后的迁移中创建的表。我不能接受丢失这些数据，但是我所知道的解决问题的唯一方法需要这些数据。基本上，我通过 [Rails ActiveRecord 迁移指南](http://edgeguides.rubyonrails.org/active_record_migrations.html)熟悉了迁移的基础知识。我想我必须将旧版本的迁移文件推送到生产环境中，回滚 10 次迁移(丢失所有新数据)，推新版本的迁移文件并再次运行 db:migrate。

谢天谢地，在我绝望地搜索解决方案时，我遇到了[这个问题和答案](https://stackoverflow.com/questions/753919/run-a-single-migration-file)，它提示了一条前进的道路。当我意识到您可以使用 version 选项向上或向下运行单个迁移时，事情就变得很清楚了。我必须将旧版本的迁移推向生产，然后运行:

```
rails db:migrate:down VERSION=20180506181554 
```

Enter fullscreen mode Exit fullscreen mode

然后推送该迁移的新版本并运行:

```
rails db:migrate:up VERSION=20180506181554 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，版本只是 rails 在使用`rails generate migration`时自动生成的文件名中的时间戳。知道如何撤销和重新运行特定的迁移文件是一个巨大的解脱。令人欣慰的是，让生产中的迁移不同步并不一定会破坏一切，而是有可能作为一个孤立的问题来处理。感谢铁路！