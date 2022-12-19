# 如何最好地组织一个 Rails 应用程序文件夹？

> 原文：<https://dev.to/maestromac/how-to-best-organize-a-rails-app-folder-18of>

除了所有基本的文件夹，我们还有一些由我们使用的 gems 生成的文件夹，然后是我们创建的自定义文件夹。有时什么应该去哪里并不明显。我们有称为“劳动”和“服务”的文件夹，它们是为普通的老红宝石对象准备的；我们对这些模式有一些目标，但从未真正实现。现在只是很困惑。

```
app
├── assets
├── controllers
├── dashboards (created by administrate gem)
├── decorators (created by draper gem)
├── fields (created by administrate gem)
├── helpers
├── javascript (created by Webpacker gem)
├── labor [** we created **]
├── lib
├── liquid_tags [** we created **]
├── mailers
├── models
├── observers [** we created **]
├── policies (created by Pundit gem)
├── sanitizers [** we created **]
├── services [** we created **]
├── uploads (created by carrierwave gem)
└── views 
```

Enter fullscreen mode Exit fullscreen mode

或者[在 GitHub 上查看](https://github.com/thepracticaldev/dev.to/tree/master/app)

你如何组织你的 Rails `app`文件夹？欢迎任何和所有自以为是的建议！