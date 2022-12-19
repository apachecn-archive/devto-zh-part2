# 文件解析在 DDD 是域操作吗？

> 原文：<https://dev.to/piczmar_0/is-file-parsing-a-domain-operation-in-ddd-27ko>

我在写一个关于 DDD 的应用程序，对下面的场景感到困惑。

我通过上传一个 CSV 文件批量创建了域对象，其中每一行都有逗号分隔的域对象属性值，例如

```
#name,surname,age
John,Smith,35
Betty,Johnson,56 
```

我是否应该有一个应用程序服务来解析文件，并且只将域对象的集合传递给域服务，或者我是否可以在域服务中完成整个解析？

你有什么建议？考虑到 DDD 方法，什么是最合适的？