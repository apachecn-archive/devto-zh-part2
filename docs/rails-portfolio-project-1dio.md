# Rails 投资组合项目

> 原文：<https://dev.to/ddhogan/rails-portfolio-project-1dio>

这个 Rails portfolio 项目借用了我的 Sinatra 项目的主题，但是从头开始重新构建，拥有嵌套的资源、更复杂的关系和更多的特性。我的辛纳特拉计划只是为了改造星际飞船。这个项目有两个模型(`Crew`和`Ships`)，它们通过赋值有一个双向的`has_many_through`关联，它充当连接表，并且可以被认为是一个特定船上的一个特定船员的角色。这个模式让我们可以很容易地列出分配到船上的所有船员，或者船员目前被分配到哪里。听起来很有道理！

快速提示:通过 BCrypt 用`has_secure_password`为用户生成种子数据时，让`password_digest`属性等于`BCrypt::Password.create('someString')`，然后你就可以在测试你的应用时使用`someString`登录了！

此外，形式不仅仅是一张漂亮的脸！Rails 表单帮助程序有时会让我忘记表单规定了 params 散列的结构。起初，我在新的任务表单中有船员的复选框，但是一直得到一个关于`crew_id`不存在的错误消息(它确实存在)，但是当我切换到下拉菜单时，问题就解决了。回想起来有意义，因为一个赋值只有一个`crew_id`的空间，所以复选框在那个上下文中没有意义，并且错误地设置了参数散列。

查看一下 [GitHub 回购](https://github.com/ddhogan/starfleeter)，或者我的[视频](https://youtu.be/STRKKznhV9Y)走查！

*原载于 2017 年 12 月 2 日*[*【donnacodes.com】*](http://donnacodes.com/rails_portfolio_project)*。*