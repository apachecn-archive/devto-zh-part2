# Django:修正了用 on_commit 钩子排队时的竞争情况

> 原文：<https://dev.to/k4ml/django-fixing-race-condition-when-queuing-with-oncommit-hook-7ae>

这已经在[芹菜文档](http://docs.celeryproject.org/en/latest/userguide/tasks.html#database-transactions)中解释过了。

在最近 django-q 投入生产后，我们遇到了这个问题。假设我们有这样一个函数

```
@transaction.atomic
def send_msg(recipient, text, msgid=None, queue=False):
    msg = Message.objects.get_or_create(msgid=msgid)
    if queue:
        async('send_msg', recipient, text, msg.msgid, queue=False)
        msg.status = 'QUEUED'
        msg.save()
        return msg

    # pass msg to backend gateway
    send_to_gateway(recipient, msg, ....) 
```

一旦我们运行了 qcluster，我们开始在日志中看到“重复条目错误”。看起来就像当我们调用 async()时，集群在事务提交之前获得它，所以 get_or_create()将使用相同的 msgid 创建新的对象，而不是获取现有的对象。所以我们在 django 1.9 中使用`transaction.on_commit()`解决了这个问题。

```
@transaction.atomic
def send_msg(recipient, text, msgid=None, queue=False):
    msg = Message.objects.get_or_create(msgid=msgid)
    if queue:
        def _add_to_queue():
            async('send_msg', recipient, text, msg.msgid, queue=False)

        msg.status = 'QUEUED'
        msg.save()
        transaction.on_commit(_add_to_queue)
        return msg

    # pass msg to backend gateway
    send_to_gateway(recipient, msg, ....) 
```

我们用 SQS，但你用什么经纪人并不重要。这取决于你的任务是否依赖于数据库中的某些东西。在我们上面的例子中，流程基本上是

Web 请求

1.  用户发送消息
2.  我们在数据库中创建一个记录，用唯一的 id 表示发送的消息
3.  async()发送消息的函数，将消息 id 作为参数之一。
4.  提交事务。

排队工人

1.  从 qcluster 调用的发送消息功能
2.  尝试通过消息 id 获取消息，如果不存在，则创建新的消息。
3.  发送消息并更新发送状态。
4.  提交事务。

此问题发生在 web 请求中的 3 号和队列工作器中的 2 号之间。基本上经验法则是，如果你使用 transaction， **async 应该从事务外部调用，而不是在**内部。

* * *

最初发布于 Github 的[期。](https://github.com/Koed00/django-q/issues/154)