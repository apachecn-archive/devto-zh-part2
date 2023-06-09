# 如何直接从标签模型运行 SQL

> 原文：<https://dev.to/dala00/laravelmodelsql-2npl>

在 Laravel 上直接执行 SQL 的方法，一般是如下所示使用 DB 的 Facade 的方法。

```
DB::statement($query); 
```

Enter fullscreen mode Exit fullscreen mode

如果是 Laravel 的话，上述问题没有，但是因为 Eloquent 即使不是 Laravel 也可以使用，所以在项目中单独放入 Eloquent 的情况下，由于没有 DB 屏蔽，所以无法执行上述操作。

此时，因为有从模型本身中获取与 DB 种子相同的东西的方法，所以可以用同样的方法执行 SQL。

首先适当地制作模型的实例。

```
$user = new User; 
```

Enter fullscreen mode Exit fullscreen mode

剩下的也一样。

```
$user->getConnection()->statement($query); 
```

Enter fullscreen mode Exit fullscreen mode