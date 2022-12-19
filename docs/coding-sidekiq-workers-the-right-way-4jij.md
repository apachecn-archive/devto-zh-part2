# 以正确的方式编写 Sidekiq Workers

> 原文：<https://dev.to/raphael_jambalos/coding-sidekiq-workers-the-right-way-4jij>

像 Rails 控制器一样，将逻辑转储到 Sidekiq workers 中很容易。就像好的 ol' *命令式*风格一样都在一个地方。但是随着时间的推移，事情变得一团糟，*你会发现自己在制造新的 Sidekiq 工人，而不仅仅是修理或使用旧的。*

所以，在你在 Sidekiq 的后台任务变得更加混乱之前，这里有一些我在我们团队学到的小技巧。

# 1。不要给你的员工灌输逻辑。

你会**从 10 行开始**，然后想嘿，*也许这段代码放在这里*是没问题的。六个月后，您发现需要对代码进行额外的检查。**你再加 20 行**。然后，你的队友发现了一个 bug，于是**又增加了十行**。

然后，一个新手认定你的逻辑很棒。她想重复使用它。*但是，嘿，这个逻辑适合工人用例*。他没有在第一次部署中冒大惊小怪的风险，而是将您的代码复制粘贴到他的 worker 中。*呸*。现在你有了两个相同代码的不同版本。

重点是，**逻辑几乎总是增长**。要负责任。花点时间考虑将逻辑放在哪里——在交互器、服务对象或模型中，它属于哪里。

# 2。不要让你的工人太大。

Sidekiq 是为*小任务*制作的。小而轻的任务。它不是为长时间工作的员工设计的。

那么，你怎么知道你的工人“太大”了呢？

有一个**循环**。例如，我们有一个附加到订单的发票模型。每次我们发出订单，我们都会给发票，并为所订购的商品预留库存。发票在开具 5 天后过期。所以，这让我们想到了这个工人...

```
class InvoiceExpirerWorker
  include Sidekiq::Worker
  include Sidetiq::Schedulable

  def perform
    expired_invoices = Invoices.expirable

    expired_invoices.each do |invoice|
      invoice.expire!
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个代码片段可能看起来对您无害，但是当您查看发票模型时，*有 100 行代码涉及到终止发票*:取消订单、退回商品库存、给客户发电子邮件、更新记录等。

想象一下，为今天到期的 *10，000 张发票*做这件事。想象每张发票需要 *20 秒*才能完全过期，乘以 10，000 张发票。

重点是，这项工作对一个工人来说太多了。

在我们的团队中，大约有 12 名员工没有完成执行。为什么？确切地说，这是因为它比单个 Sidekiq 工作器的设计要多得多。工作人员刚刚用完内存，Linux 就终止了这个任务，我们只剩下几百张本该过期的发票。太好了。

那么，我们现在该怎么办？

使用一个**主工人**来产生较小的工人。主工人的任务是构建一个将要过期的发票列表。它逐个检查它们，并对每个调用一个单独的工作线程。如果列表中有 10，000 张发票，那么将创建 10，000 个轻量级工人。

这样，完成的机会更大，因为*每个工人不会花太长时间*。

这里有一个更好的模式:

```
class BatchInvoiceExpirerWorker
  include Sidekiq::Worker
  include Sidetiq::Schedulable

  def perform
    expired_invoices = Invoices.expirable

    expired_invoices.each do |invoice|
      InvoiceExpirerWorker.perform_async(invoice.id)
    end
  end
end

class InvoiceExpirerWorker
  include Sidekiq::Worker

  def perform(invoice_id)
    invoice = Invoice.find(invoice_id)
    invoice.expire!
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

不再是一个大工人花 200，000 秒完成(每天大约 55 小时)，我们现在有 10，000 个小工人排队每人跑 20 秒。好多了。

# 3。将代码组织到目录中。

我们开始时只有 10 名工人。后来变成了 20，然后 40，然后 50。现在，它在`apps/workers`有 *100 名工人*。

帮你自己一个忙。请将您的代码组织到目录中。

# 4。规划您的 Sidekiq 执行时间表和优先级方案。

如果你认为凌晨 3 点是增加工人的安全时间…好吧，再想想。也许其他三个开发人员也想到了这一点，并会在凌晨 3 点添加他们自己的工作人员。

让我们回到前面的例子，在#2 中提到的 10，000 名小型发票过期工作人员，我们称之为工作人员 1。例如，您决定在凌晨 3 点添加工人 1。然后，另一个工人——姑且称之为工人 2——与工人 1 几乎同时排队。 *Yikes* 。如果工人 2 是一个对时间要求很高的工人，你可能要等*一个多小时*才能拿到。

为了避免这种情况，创建一个跟踪器。它可以像 Google 电子表格一样简单，或者更好，是一个自动化系统，这样你就不必在每个开发人员添加/更改 Sidekiq 计划后清理。追踪器给你一个更新的视图，什么工人被安排在什么时间。这样，您可以避免工作人员 1 妨碍时间更紧迫的工作人员 2。

现在，不要就此打住。有些情况下，某个员工必须比另一个员工优先**，你也必须考虑到这一点。**

 **假设您计划让 Worker 2 以更高的优先级运行。这样，当它在凌晨 3:05 左右运行时，它将在 Worker 1 之前运行。

但是，如果在将来，其他人添加了另一个工作人员(例如，工作人员 3、工作人员 4 等)怎么办。)的优先级高于或等于当前调度的优先级？这将在您的员工完成工作之前增加一些延迟。

Sidekiq 中的优先级是通过将您的工人组织成队列来完成的。每个队列在 Sidekiq 中都有相应的优先级。在这里阅读更多信息:[https://github.com/mperham/sidekiq/wiki/Advanced-Options](https://github.com/mperham/sidekiq/wiki/Advanced-Options)

因此，最好同时计划优先级和调度工人计时*以便有更好的机会获得更快的 Sidekiq 执行时间，并降低 Sidekiq 实例的低利用率。*

# 5。定期检查你的 Sidekiq 任务。

也许之前的任务只需要几秒钟。现在订单增加了 10 倍，任务可能需要 5 分钟。快进到今年，它甚至可能无法完成执行。现在是时候根据上述原则重构 worker 了。

# 结论

我希望这篇博文可以节省你整理 Sidekiq 工作器的时间和精力。通过建立一个标准的实践，你拯救了你自己(和所有人！)试图检查您的代码的麻烦，或者仅仅是找出一个工人失败的原因。

*特别感谢我的编辑艾伦，他让这篇文章更加连贯***