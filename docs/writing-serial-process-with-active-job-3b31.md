# 使用活动工单编写串行流程

> 原文：<https://dev.to/masashisalvador57f/writing-serial-process-with-active-job-3b31>

我使用 Rails 中的活动作业来写作业队列，我写了一些发出 SQL 并在临时表中写数据的作业，然后我想写一个完整的处理器作业来连续执行作业 1、作业 2、作业 3....，和 jobN 并完成了一个计算任务。

那么，在 ActiveJob 中，我们有两个方法:

```
job.perform_later() # asynchronous
job.perform_now() # wait for finishing. 
```

Enter fullscreen mode Exit fullscreen mode

所以，对我来说,`perform_now`是一个解。

# 用 ActiveJob 序列化流程

```
# this Job is sleeper
class Job1Job < ApplicationJob
  queue_as :default

  def perform()
    puts "hello1"
    sleep(5)
    puts "after sleep"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
# this Job won't sleep
class Job2Job < ApplicationJob
  queue_as :default

  def perform()
    puts "hello2"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
# this job is a manager
class Job3Job < ApplicationJob
  queue_as :default

  def perform()
    Job1Job.perform_now()
    Job2Job.perform_now()
    puts "hoge"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在 rails 控制台中执行的结果。

```
> Job3Job.perform_later() 
```

Enter fullscreen mode Exit fullscreen mode

然后我们得到

```
# bundle exec rake jobs:work
hello1
after sleep
hello2
hoge 
```

Enter fullscreen mode Exit fullscreen mode

:)