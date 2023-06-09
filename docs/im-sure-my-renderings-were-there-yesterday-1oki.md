# 我确定我的效果图昨天还在？

> 原文：<https://dev.to/jermdavis/im-sure-my-renderings-were-there-yesterday-1oki>

随着时间的推移，我注意到，随着 Sitecore 的发展，它越来越依赖于搜索集成，使得 Solr 这样的东西变得越来越重要。如果出于某种原因，您的搜索服务不可用，这将导致您遇到一系列令人兴奋的新问题。

由于这个原因，当我的一些渲染图从发布的页面上消失时，我浪费了一些非常好的开发时间。

## 我最初的困惑

我当时正在为一个客户的网站开发“门控内容”功能。我一直在为此做一些后端渲染工作，编辑可以选择一个体验表单进行数据捕获，网站用户可以通过下载来完成它。一两天前，我已经准备好了所有的后端代码，并计划在合并代码之前再花一个小时左右的时间进行一些重构。但是，当我打开我的开发网站开始工作时，我的门控内容组件消失了:

[![](img/b8c895a63fde53c41870c74132b7115c.png)T2】](https://jermdavis.files.wordpress.com/2018/08/missingrenderings.png)

困惑之余，我查看了演示细节。我检查了我仍然在寻找正确的源分支。我检查了“网络”数据库，以确保我发表了一切。我开始担心我昨晚的成功是某种非常真实的梦。

## 寻找答案

我继续挖掘，试图找出发生了什么，并在网站日志中发现了这个:

```
13184 07:14:12 WARN  '{183620B4-31CB-4FB5-9DB5-9D67414E7104}' is not valid datasource for web or user does not have permissions to access.
13184 07:14:12 WARN  Failed to execute datasource query System.NullReferenceException: Object reference not set to an instance of an object.
   at Sitecore.ContentSearch.SitecoreItemCrawler.IsExcludedFromIndex(SitecoreIndexableItem indexable, Boolean checkLocation)
   at Sitecore.ContentSearch.SitecoreItemCrawler.GetContextIndexRanking(IIndexable indexable)
   at System.Linq.Enumerable.WhereSelectListIterator`2.MoveNext()
   at System.Linq.Enumerable.Min(IEnumerable`1 source)
   at Sitecore.ContentSearch.AbstractSearchIndex.Sitecore.ContentSearch.Pipelines.GetContextIndex.IContextIndexRankable.GetContextIndexRanking(IIndexable indexable)
   at Sitecore.ContentSearch.Pipelines.GetContextIndex.FetchIndex.<>c__DisplayClass6_0.<RankContextIndexes>b__0(ISearchIndex i)
   at System.Linq.Enumerable.WhereSelectEnumerableIterator`2.MoveNext()
   at System.Linq.Buffer`1..ctor(IEnumerable`1 source)
   at System.Linq.OrderedEnumerable`1.<GetEnumerator>d__1.MoveNext()
   at System.Linq.Buffer`1..ctor(IEnumerable`1 source)
   at System.Linq.Enumerable.ToArray[TSource](IEnumerable`1 source)
   at Sitecore.ContentSearch.Pipelines.GetContextIndex.FetchIndex.GetContextIndex(IIndexable indexable, GetContextIndexArgs args)
   at Sitecore.ContentSearch.Pipelines.GetContextIndex.FetchIndex.Process(GetContextIndexArgs args)
   at (Object , Object[] )
   at Sitecore.Pipelines.CorePipeline.Run(PipelineArgs args)
   at Sitecore.Pipelines.DefaultCorePipelineManager.Run(String pipelineName, PipelineArgs args, String pipelineDomain)
   at Sitecore.Abstractions.CorePipelineWrapper.Run(String pipelineName, PipelineArgs args)
   at Sitecore.ContentSearch.Pipelines.GetContextIndex.GetContextIndexPipeline.Run(ICorePipeline pipeline, GetContextIndexArgs args)
   at Sitecore.Mvc.ExperienceEditor.DatasourceValidator.DatasourceValidator.IsDatasourceValid(String dataSource, Database database) 
```

Enter fullscreen mode Exit fullscreen mode

我读到第一行的第一个想法是，我不小心对我的数据源项做了一些奇怪的事情。因此，我重新检查了它是否存在，我的组件是否正确地指向它，它是否有适合我的渲染的模板，以及它是否应用了任何意外的安全性。但所有这些都是正确的。

这让我想到了错误的第二部分——`NullReferenceException`。

浏览日志时，我看到了“空引用”,并假设我的代码抛出是因为数据源项不可访问。但是当我第二次阅读日志条目时，我意识到实际上我的代码并没有在堆栈跟踪中提到。都是 Sitecore 代码，讲的是 ContentSearch APIs 和“数据源查询”。

现在我知道在我的组件上的 datasource 集合中没有查询，因为它只是使用了目标项的 ID。但是这让我开始思考搜索索引——在这种情况下是由 Solr 提供的。所以我试着浏览 Solr UI 来检查它是否开心，结果:

[![](img/276bafac7b8be656747af16992c18e17.png)T2】](https://jermdavis.files.wordpress.com/2018/08/nosolr.png)

没有索尔。我将它作为 NSSM 服务安装，所以在 Windows 中检查服务管理工具:

[![](img/dacf6cc677dcfea86d9ad41c534fff78.png)T2】](https://jermdavis.files.wordpress.com/2018/08/servicestopped.png)

我重启了那个服务，刷新了我的页面——没什么不同。但是在 iisreset 之后:

[![](img/712f31dc48675b6fdfd877be1305ec0c.png)T2】](https://jermdavis.files.wordpress.com/2018/08/renderingback.png)

答对了。我的工作回来了。

这时，我想起来前一天晚上我已经停止了一系列“非必要”的服务，以使我的笔记本电脑电池能够支持长途火车旅行…

* [epic facepalm](https://media.giphy.com/media/27EhcDHnlkw1O/giphy.gif) *

## 结论

如今，越来越多的 Sitecore 依赖于 Solr。xConnect 服务需要它。[体验表单编辑 UI 需要它](https://dev.to/jermdavis/the-language-of-your-sitecore-forms-is-more-important-than-you-thought-1p06-temp-slug-2752972)。似乎还有一个数据源验证管道组件也依赖于它。

所以，如果你不想像我一样觉得自己很傻，你需要确保它已经启动并运行了…