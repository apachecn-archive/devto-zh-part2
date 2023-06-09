# Rails 快速提示#2:特定于项目。pryrc

> 原文：<https://dev.to/citizen428/rails-quick-tips-2-project-specific-pryrc-1526>

像许多 Ruby 和 Rails 开发人员一样，我在几乎所有的项目中都使用[proy](https://github.com/pry/pry)而不是 [IRB](http://ruby-doc.org/stdlib-2.5.1/libdoc/irb/rdoc/IRB.html) 。

最近，我意识到许多人不知道这样一个事实，即`pry`支持特定于项目的`.pryrc`文件，这非常方便，例如当在 Rails 控制台中尝试时。只需在应用程序的根目录下添加一个`.pryrc`文件，并添加您希望在每个控制台会话中都可用的代码。下面是我们的一个应用程序的修改示例:

```
def admin
  @admin ||= Admin.first
end

def user
  @user ||= User.first
end 
```

Enter fullscreen mode Exit fullscreen mode

这样，当我开始一个新的`rails console`会话时，我总是可以访问`admin`或`user`，缓存将确保数据库只在第一次调用方法时被命中:

```
[1] (rails_new) main: 0> user
  User Load (2.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT $1  [["LIMIT", 1]]
#=> #<User id: 1, email: "test@example.com", created_at: "2018-06-26 07:22:18", updated_at: "2018-06-26 07:22:18">
[2] (rails_new) main: 0> user
#=> #<User id: 1, email: "test@example.com", created_at: "2018-06-26 07:22:18", updated_at: "2018-06-26 07:22:18"> 
```

Enter fullscreen mode Exit fullscreen mode

我发现以这种方式使用特定于项目的`.pryrc`文件，在 Rails 控制台中进行尝试时，可以极大地提高工作效率，因为它们消除了大量的重复设置。