# 溪流的快乐

> 原文：<https://dev.to/bertilmuth/the-joy-of-streams-2p0o>

最近，我写了一个大致如下的类:

```
public class Requirement { ...
    public Requirement(...) { ...
    }
    public When getWhen() { ...
    }
    public Event getEvent() {...
    }
    public SystemReaction getSystemReaction() {...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我有一个`Requirement`实例的列表。现在，我需要至少被一个`Requirement`引用的一组`Event`实例。一个`Event`实例也可以是`null`。

在 Java 8 之前的 Java 中，我会做以下事情:
a)创建一个空的`HashSet`
b)遍历`Requirement`实例
的列表 c)对于每个`Requirement`实例，检查`getEvent()`是否返回`null`
d)如果没有返回`null`，将事件添加到`HashSet`

从 Java 8 开始，您可以使用 streams 在一行代码中完成这项任务:

```
Set<Event> events = requirements.stream().map(req -> req.getEvent())
  .filter(event -> event != null).collect(Collectors.toSet()); 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于流的信息，请查看本教程。