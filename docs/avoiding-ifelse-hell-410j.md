# 避免 IF_ELSE 地狱

> 原文：<https://dev.to/venkteshv/avoiding-ifelse-hell-410j>

所有的开发人员在某种程度上都面临着两难的境地。他们被要求适应各种场景，因此最终编写 if-else 条件语句，并达到代码变得混乱的程度。几周后，开发人员可能甚至不理解他写的代码。好吧，如果在某个时候你觉得你写的 if else 阶梯令人讨厌或难以忘怀，欢迎加入这个俱乐部。在这篇文章中，我想谦虚地展示一个例子，在这个例子中，我通过应用一个现有的模式和一个库避免了 if-else 地狱。

在我们的应用程序中有一个更新状态历史表的需求，在这个表中我们维护了文档处理的各种状态，比如上传、下载等等。我们维护各种状态，如提取，上传，下载，上传等。因此，我们需要一个验证逻辑来确保状态不会以错误的顺序更新，所以我们必须建立一个小的验证系统来确保其他开发人员不会破坏顺序，因为这将导致大环境中严重的级联故障。所以我决定这次不写条件包了。当我盯着接受标准和状态时，我意识到一个模式。在所有这些状态之后，一个文档通过，瞧，我得到了一个解决方案，状态机。既然这个应用已经上线了。net core 我决定使用“无状态”包来实现同样的功能。所以让我们跳到例子。

```
 public ProcessState Status
    {
        get
        {
            return _stateMachine.State;
        }
    }

         public StatelessStateMachine(ProcessState state)
    {
        _stateMachine = new StateMachine<ProcessState, ProcessStateTransitionTriggers>(state);
        _stateMachine.Configure(ProcessState.Extracted)
            .Permit(ProcessStateTransitionTriggers.moveToQueuedForDownload, ProcessState.QueuedForDownload);

        _stateMachine.Configure(ProcessState.QueuedForDownload)
            .Permit(ProcessStateTransitionTriggers.moveToDownloading, ProcessState.Downloading);

        _stateMachine.Configure(ProcessState.Downloading)
           .Permit(ProcessStateTransitionTriggers.moveToDownloaded, ProcessState.Downloaded);

        _stateMachine.Configure(ProcessState.Downloaded)
         .Permit(ProcessStateTransitionTriggers.moveToQueuedForUpload, ProcessState.QueuedForUpload);

    } 
```

解释:
_ 状态机。State 返回当前状态。
接下来，下面函数中的第一行用于实例化状态列表和触发器(ProcessState 和 ProcessStateTransitionTriggers 是分别包含状态和触发器列表的枚举)。因此，当根据许可证中指定的状态触发触发器时，将返回 true 或 false 来指示状态转换是否有效。

{
_ 状态机。配置(进程状态。提取)
。permit(processstatetransitiontriggers . movetoqueuedfordownload，ProcessState。QueuedForDownload)；
}

例如，在上面的代码中，如果提取了当前状态，那么如果转换触发器是 moveToQueuedForDownload，则允许转换到状态“QueuedForDownload”

{ _ 状态机。CanFire(触发器)}

上面的行将触发验证，如果它返回 true，则可以使用

{ _ 状态机。开火(触发)}

在上面的例子中，“触发器”将是“moveToQueuedForDownload”。

结论:

因此，我们强迫自己编写的许多常见的令人讨厌的代码可以通过与现有模式相关联来避免。