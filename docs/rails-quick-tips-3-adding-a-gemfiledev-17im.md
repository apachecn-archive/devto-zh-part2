# Rails 快速提示#3:添加 Gemfile.dev

> 原文：<https://dev.to/citizen428/rails-quick-tips-3-adding-a-gemfiledev-17im>

我们都知道这个问题:不同的开发人员喜欢不同的库，特别是对于他们的开发工作流。然而，由于各种原因，我们可能不只是想无条件地将它们添加到我们的应用程序的`Gemfile`:

*   添加了与实际应用不直接相关的依赖项(包括它们自己的依赖项)。
*   讨论 gem A 相对于 gem B 的优点或者为什么我们不应该使用 gem C🙄

出于这个原因，我通常会在我的`.gitignore`中添加一个`Gemfile.dev`条目，每个开发人员都可以根据自己的意愿定制一个适合他们工作流程的 gem 列表，但是同一个团队中的其他人可能不感兴趣/不想要。一个微不足道的例子是这样的:

```
eval_gemfile 'Gemfile'

gem 'awesome_print' 
```

Enter fullscreen mode Exit fullscreen mode

这里`eval_gemfile`确保所有主`Gemfile`的宝石都包含在本地开发包中，然后我们在上面添加 [`awesome_print`](https://github.com/awesome-print/awesome_print) 。

现在我们可以继续使用下面两个命令中的任何一个来安装我们的 gems 并创建一个`Gemfile.dev.lock` :

```
$ bundle install --gemfile=Gemfile.dev
# or
$ BUNDLE_GEMFILE=Gemfile.dev bundle 
```

Enter fullscreen mode Exit fullscreen mode

我通常更喜欢使用`BUNDLE_GEMFILE`环境变量的形式，因为它适用于所有用例(例如`BUNDLE_GEMFILE=Gemfile.dev rails c`)。当然这是很多额外的输入，所以你可以在 [`./bundle/config`](https://bundler.io/v1.16/bundle_config.html) 中使用别名、 [direnv](https://github.com/direnv/direnv) 或者甚至一个`gemfile`配置选项来使这个过程更容易。