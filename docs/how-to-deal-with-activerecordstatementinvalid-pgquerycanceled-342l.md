# 如何处理 active record::statement invalid PG::query canceled？

> 原文：<https://dev.to/ben/how-to-deal-with-activerecordstatementinvalid-pgquerycanceled-342l>

我们有时会遇到这种错误,“拔掉插头再插回去”确实能让我们重新开始，但是我想知道更多关于处理这种错误的一般方法。

它伴随着像`PG::ConnectionBad: FATAL: remaining connection slots are reserved for non-replication superuser connections`这样的错误

我知道这是一个资源限制的问题，以及 Postgres 如何处理它，但除了硬重置之外，我没有处理这个问题的好办法。

在处理这个问题时，我也调整了总的池大小，但我仍然对一些潜在的机制没有信心。

这里到底发生了什么，我应该如何处理这个问题？