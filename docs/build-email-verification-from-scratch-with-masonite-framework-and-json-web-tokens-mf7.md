# 使用 Masonite 框架和 JSON Web 令牌从头开始构建电子邮件验证

> 原文：<https://dev.to/nioperas06/build-email-verification-from-scratch-with-masonite-framework-and-json-web-tokens-mf7>

[Masonite 框架](https://github.com/MasoniteFramework/masonite)是一个现代的以开发者为中心的 Python web 框架。Masonite 的架构更类似于 Laravel 框架。

在本教程中，我将向您展示如何为您的 Masonite 应用程序从头开始构建电子邮件验证。

Masonite 附带了一个名为 Craft 的 CLI 工具。Craft 提供了一种简单的方法，使用一个简单的命令:
来搭建认证所需的东西

```
$ craft auth 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令生成了认证所需的一切:4 个新控制器、5 个新模板和 6 个新路由。因此，您希望处理用户电子邮件验证并验证电子邮件。我们走吧！

## 创建新的 Masonite 项目和脚手架认证

```
$ pipenv install masonite-cli
$ craft new masonite-app-with-user-verification 
$ cd masonite-app-with-user-verification 
$ craft install 
$ craft auth 
```

Enter fullscreen mode Exit fullscreen mode

## 设置数据库

为了注册用户，我们将需要一个数据库。让我们使用一个 MySQL 数据库。

```
$ pipenv install PyMySQL 
```

Enter fullscreen mode Exit fullscreen mode

## 创建新的数据库并将你的数据库凭证放入。环境文件:

```
DB_DRIVER=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=masonite
DB_USERNAME=root
DB_PASSWORD=root 
```

Enter fullscreen mode Exit fullscreen mode

## 为用户模型添加“活动”属性

让我们创建一个新的迁移:

```
$ craft migration add_active_to_users_table --table=users 
```

Enter fullscreen mode Exit fullscreen mode

然后，添加新的活动属性:

```
from orator.migrations import Migration
class AddActiveToUsersTable(Migration):
    def up(self):
         with self.schema.table('users') as table:
             table.boolean('active').default(False)

    def down(self):
         with self.schema.table('users') as table:
             table.drop_column('active') 
```

Enter fullscreen mode Exit fullscreen mode

应用您的迁移😄

```
$ craft migrate 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么使用 JWT 进行邮件验证？

JSON Web 令牌是一种在各方之间安全传输信息的好方法。

对于电子邮件验证，我们需要向注册用户发送一个随机散列。

## 注册时生成新的 jwt 令牌

我们需要使用 Python 中的 JSON Web Token 实现:

```
$ pipenv install PyJWT 
```

Enter fullscreen mode Exit fullscreen mode

然后

```
data = {'email': user.email}
encoded = jwt.encode(data, os.environ.get('KEY'), algorithm='HS256')
token = str(encoded, 'utf-8') 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要发送一封邮件。Masonite 提供了一个包调用[通知](https://docs.masoniteproject.com/official-packages/masonite-notifications)来帮助我们完成这个任务。

```
$ pipenv install masonite-notifications 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建一个电子邮件验证通知。

```
$ craft notification EmailVerificationNotification 
```

Enter fullscreen mode Exit fullscreen mode

我们的通知应该发送一封带有包含验证令牌的链接的电子邮件。

```
from notifications import Notifiable

class EmailVerificationNotification(Notifiable):
def mail(self):
    return self.subject('New account signup!') \
        .driver('smtp') \
        .heading('Masonite App With User Verification') \
        .line('In order to use your account, you have to validate your email address.') \
        .line('Please click on the link below.') \
        .action('Validate my account', href=self._link) 
```

Enter fullscreen mode Exit fullscreen mode

还不错。

然后，让我们将这封邮件发送给我们的用户:

```
if token:
    Notify.mail(EmailVerificationNotification, to=user.email, link='http://localhost:8000/activate/{0}'.format(token))
    Session.flash(‘success’, ‘Almost done! Please check your email to complete the registration process.’)
    return Request.redirect(‘/login’) 
```

Enter fullscreen mode Exit fullscreen mode

如果您的 smtp 凭证正确，您的邮件中将会包含以下内容:

[![Email verification](img/237275b4ebc7250ff1141668cf72a16f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yyFHZ-mz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p9mgktx5nsoh5uvkkof5.png)

## 创建邮件验证路径

```
get('/activate/@token', 'RegisterController@validate') 
```

Enter fullscreen mode Exit fullscreen mode

## 定义用户模型上的可填充属性‘激活’

```
class User(Model):
    __fillable__ = ['name', 'email', 'password', 'active'] 
```

Enter fullscreen mode Exit fullscreen mode

## 验证用户

```
def validate(self, Request):
    if Request.param('token'):
        data = jwt.decode(Request.param('token'), os.environ.get('KEY'), algorithms=[‘HS256’])
        user = User.where('email', data['email']).first()
        if user:
            user.active = True
            user.save()
            Session.flash('success', 'You\'re in! Let\'s login!')
    return Request.redirect('/login') 
```

Enter fullscreen mode Exit fullscreen mode

## 最后一件事！登录用户:

```
def store(self, Request, Session):
    user = User.where('email', Request.input('email')).first()
    if user.active:
        if Auth(Request).login(Request.input('email'), Request.input('password')):
            return Request.redirect('/home')
        return Request.redirect('/login')
    else:
        Session.flash('warning', 'Please check your email to complete the registration process.')
        return Request.back() 
```

Enter fullscreen mode Exit fullscreen mode

## 您的用户现已通过验证🔥

我会把这些东西放在一个 Masonite 包里。如果你想为软件包的开发做贡献或者对 Masonite 的开发感兴趣，那么一定要加入 [Slack channel](http://slack.masoniteproject.com/) 或者在 [GitHub](https://github.com/MasoniteFramework/masonite) 上启动回购。

请随意添加您的评论，以便进一步讨论。本教程的完整代码可在 Github 上获得。谢谢！