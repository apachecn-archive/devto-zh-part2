# 如何修复 Heroku 推送错误！

> 原文：<https://dev.to/emma_odia/how-to-fix-that-heroku-push-error-17o>

你犯了一个错误，把你的代码推到工作帐户，而不是你的个人帐户！？！！恐怖啊！痛苦！！我的天啊...痛苦！！！

显然，你在电脑上登录了工作账户，最后一次推送时，你已经创建了一个个人 heroku 应用程序，很可能是一个周末爱好项目，是的，你推送了办公室仪表板...嗯。让我们来解决这个问题，好吗？

以下是解决这个问题的方法。

1.  通过 CLI 在 Heroku 上登录您的帐户

```
heroku login 
```

Enter fullscreen mode Exit fullscreen mode

输入您的凭据。显然这就是为什么你一开始就推错了账户！咄！

1.  检查托管您的应用程序的远程 URL

```
// Check for the current url 
git remote -v 
```

Enter fullscreen mode Exit fullscreen mode

1.  鉴于你当前的用户帐号不能推送至远程 heroku 网址，我们必须删除它！

```
// remove remote url
git remote rm heroku 
```

Enter fullscreen mode Exit fullscreen mode

1.  现在，我们必须通过 CLI 创建一个新的 Heroku 应用程序，因为我们现在已登录到您的用户帐户

```
heroku create 
```

Enter fullscreen mode Exit fullscreen mode

1.  此时您会得到一个 url，它也是在您的 heroku 仪表板上创建的。现在将此 url 设置为您的应用程序的远程 url

```
git remote add https://git.heroku.com/<unique-heroku-app-name>.git 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们再小心也不为过！只是为了确保运行

```
git remote -v 
```

Enter fullscreen mode Exit fullscreen mode

1.  现在，我们有信心将我们的应用推向 heroku

```
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

唷！真是千钧一发，伙计！