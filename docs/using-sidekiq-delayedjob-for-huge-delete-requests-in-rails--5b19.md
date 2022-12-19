# 使用 sidekiq delayed_job 处理 Rails 中的大量删除请求

> 原文：<https://dev.to/nodefiend/using-sidekiq-delayedjob-for-huge-delete-requests-in-rails--5b19>

## 问题:

> 当您的 api 中有很多依赖销毁模型挂钩时，删除会变得很昂贵，特别是当您删除 1000 条记录时。

[由 mélanie 十字架拍摄的照片](https://unsplash.com/@lionipan)

*本文不是关于安装 sidekiq 的，如果你对安装感兴趣，点击[这里](https://github.com/mperham/sidekiq/wiki/Getting-Started)T3】*

### 上下文:

*   我们有一个前端 redux react 应用程序发送删除请求，并相应地更新前端存储
*   我们的后端接受一组对象来运行批量删除。
*   Sidekiq 已经在我们的服务器上运行了(有许多其他的延迟任务选项，看看哪一个最适合你)

### 开始了:

因此，我们遇到了这样一种情况，用户登录应用程序，执行一个常见的操作，每次他这样做都会使我们的服务器崩溃。

**尴尬对吗？**

我们后来发现他做的是执行删除操作，但我们不知道他使用我们应用程序的规模，我们肯定没有计划处理这样的情况，即有人会在有大量真实数据的情况下使用我们的应用程序...

## 解

经过一番挖掘之后，显然延迟删除数据库记录这样的工作是很常见的，因为在达到一定规模之后，对于服务器来说，这变得非常耗时和昂贵。

这实际上完美地结束了工作，因为我们的 redux 前端可以假设用户试图删除这个模型，我们可以通过从 reducer 中取出`id`来拼接对象。

## 让我们开始真正的比赛:

告诉您的 rails 应用程序使用 sidekiq 进行作业:

`config/application.rb`

```
#use sidekiq for delayed jobs:
config.active_job.queue_adapter = :sidekiq 
```

Enter fullscreen mode Exit fullscreen mode

为 sidekiq 创建队列文件:

`config/sidekiq.yml`

```
:queues:
  - [default, 1] 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个为我们的服务器运行命令的 [procfile](http://www.dailysmarty.com/posts/how-to-use-multiple-procfiles-in-a-rails-application-and-automate-process-workflow-with-foreman) 。

我们需要告诉 sidekiq 使用我们新的`sidekiq.yml`文件。

`procfile`

```
sidekiq: bundle exec sidekiq -t 25 -C config/sidekiq.yml 
```

Enter fullscreen mode Exit fullscreen mode

现在，这就是我在我们的环境中启动 rails 所需要做的一切，你可能需要做更多或更少的事情，如果你需要任何帮助，请在评论中告诉我，我非常乐意帮助你解决问题。

## 太棒了！

既然我们已经设置好了问题，我们可以在控制器中实现一个延迟的作业...

让我们使用作业生成命令来创建一个作业

在您的目录中运行:

`rails g job delete_some_model`

这将为您生成几个测试文件，加上我们实际使用的文件(因为谁编写测试？，哈哈哈`good developers thats who`

导航到我们的工作([更多关于工作](http://edgeguides.rubyonrails.org/active_job_basics.html)):

### 作业类有哪些？

作业类是放置将由队列执行的代码的地方。有一个 perform 方法，当作业第一次排队时(当您调用 perform_later 方法时)，无论发送什么参数，都会调用并发送该方法。

`app/jobs/delete_some_model_job.rb`

```
class DeleteSomeModel < ApplicationJob

  queue_as :default

  def perform(model_id)

    some_model = Model.find(model_id)
    some_model.destroy

  end
end 
```

Enter fullscreen mode Exit fullscreen mode

好极了。现在我们已经有了删除模型的延迟作业，我们只需要给它发送一个 ID。

### 注意:不要将对象作为参数传入:

Put instance id 可以减少 Redis DB 而不是一个实例的大小开销。
Sidekiq 客户端 API 使用 JSON.dump 将数据发送到 Redis。

Sidekiq 服务器从 Redis 提取 JSON 数据，并使用 JSON.load 将数据转换回 Ruby 类型，以传递给 perform 方法。

不要传递符号、命名参数或复杂的 Ruby 对象(如日期或时间！)因为它们将不能正确地经受转储/装载往返。

[来源](https://medium.com/@et3216/9-ways-to-boost-sidekiq-performance-correctly-in-practical-experiences-bfebe9ee0f28)

现在我们有了自己的作业，我们可以在控制器中调用它:

`some_controller.rb`

```
def destroy
    @some_model = SomeModel.find(params[:id])
    if DeleteSomeModel.perform_later(@some_model[:id])
      render json: { status: :ok }
    else
      common_error(@some_model.errors.full_messages)
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

## 够简单了吧？！

请在下面留下任何问题或评论！

我还引用了一些非常有用的文章！

## 来源:

[https://blog . botree technologies . com/migrate-from-delayed-job-to-sidekiq-772 ACC 8e 1c DD](https://blog.botreetechnologies.com/migrate-from-delayed-job-to-sidekiq-772acc8e1cdd)

[https://blog.codeship.com/how-to-use-rails-active-job/](https://blog.codeship.com/how-to-use-rails-active-job/)