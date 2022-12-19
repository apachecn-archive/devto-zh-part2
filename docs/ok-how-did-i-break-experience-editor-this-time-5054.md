# 好吧，这次我是怎么破经验编辑器的？

> 原文：<https://dev.to/jermdavis/ok-how-did-i-break-experience-editor-this-time-5054>

[体验编辑](https://doc.sitecore.net/sitecore_experience_platform/content_authoring/the_editing_tools/the_experience_editor/the_experience_editor)。一切正常的时候真的很棒。但是[在你如何设置你的网站上犯错误](https://jermdavis.wordpress.com/2015/03/02/notes-on-an-annoying-misconfiguration/)并不难，这导致了在所见即所得编辑器中难以诊断的故障。我最近遇到了一个这样的问题，这似乎正是谷歌需要了解的事情，以将未来的开发人员(可能也包括未来的我)从调试它的痛苦中拯救出来。

## Bug 报告…

我被分配了两份关于我正在建设的网站的 QA bug 报告。它们都与一种特定的呈现方式有关——这允许编辑通过注入页面的`<iframe/>`标签引入外部内容:

*   第一个错误报告说，试图将这种渲染添加到 QA 服务器上的页面会导致 Experience Editor 抛出一个模式对话框，并以神秘的“`An error occurred.`”作为其消息。渲染没有在页面上结束，但是体验编辑器在对话框关闭后继续工作: [![](../Images/55ca3aa47ee79e10e3a0c29d279eb1e3.png)](https://jermdavis.files.wordpress.com/2018/09/firsterror.png)
*   第二份报告称，在一个已经有该组件的页面上，Experience Editor 现在无法在编辑模式下呈现该组件，而是显示来自其父容器组件的异常: [![](../Images/1794335536cf7bf96a0ad621735a9143.png)](https://jermdavis.files.wordpress.com/2018/09/seconderror.png) 但是该页面在预览和发布模式下工作正常。对于谷歌的好处，这里的两个例外的关键部分是:

```
Error Rendering View: /Views/PageLayout/66-33.cshtml: Error while rendering view: '/Views/PageLayout/66-33.cshtml' (model: 'Sitecore.Mvc.Presentation.RenderingModel, Sitecore.Mvc').
   at Sitecore.Mvc.Presentation.ViewRenderer.Render(TextWriter writer)
   at Sitecore.Mvc.Pipelines.Response.RenderRendering.ExecuteRenderer.Render(Renderer renderer, TextWriter writer, RenderRenderingArgs args) 
```

Enter fullscreen mode Exit fullscreen mode

和

```
Inner Exception: Fields field cannot contain any of the following symbols: ():=
   at Sitecore.Diagnostics.Assert.IsFalse(Boolean condition, String message)
   at Sitecore.Pipelines.GetChromeData.GetChromeDataProcessor.GetFieldEditorButton(Item item)
   at System.Linq.Enumerable.WhereSelectArrayIterator`2.MoveNext()
   at System.Linq.Enumerable.WhereEnumerableIterator`1.MoveNext()
   at System.Collections.Generic.List`1.InsertRange(Int32 index, IEnumerable`1 collection)
   at Sitecore.Pipelines.GetChromeData.GetRenderingChromeData.GetCustomButtons(RenderingItem renderingItem)
   at Sitecore.Pipelines.GetChromeData.GetRenderingChromeData.Process(GetChromeDataArgs args)
   at (Object , Object[] ) 
```

Enter fullscreen mode Exit fullscreen mode

## 挖进去…

我对这些问题的初步阅读使我认为这两个错误可能是相关的，并且让编辑能够使用原始标记来添加`<iframe/>`元素是一种非常好的方式，允许他们以某种方式意外破坏页面的标记。我还注意到我不能在我的开发实例上重现这些问题。它们只发生在问答上。这让我开始思考，不可靠的标记可能会在 Experience Editor 中引起问题，因为它的很多行为都依赖于能够注入编辑器用来控制渲染的所有“chrome”。所以我进行了一些基本的检查，试图找出任何明显的错误:

*   我查看了该组件可用的 DataSource 项中的内容，以检查它们没有注入格式错误的 HTML。
*   我查看了 Razor 文件的渲染，以确保它不包含任何格式错误的 HTML。
*   我查看了占位符设置，以确保该组件允许出现在正在使用的占位符中
*   我检查了渲染图、占位符设置、数据源和页面上的项目安全性，以防它们被搞乱

这些人都没有出现任何问题…老实说，这并不奇怪，因为 Master 数据库中的相同代码和内容在我的开发机器上没有问题。

我调试之旅的下一站是古老的“把它全部拉出来，然后把它放回去，直到它坏掉”策略。所以:

*   我创建了一个指向原始 CSHTML 文件的新渲染定义，但是没有应用任何其他设置。当放在页面上时，效果很好。
*   我为新的渲染定义创建了一个新的占位符设置条目。我可以毫无问题地通过体验编辑器添加它。
*   我将原始渲染中的数据源模板设置添加到新的渲染中。新的还能用。
*   我将原始渲染中的数据源位置设置添加到新的渲染中。它仍然有效。
*   我将原始渲染中的缓存设置添加到新的渲染中。新的还能用。
*   我比较了原始渲染和新渲染的所有其他设置，发现只有一个不同之处:体验编辑器按钮。所以我把旧的自定义按钮加到了新的。还有[轰](https://media.giphy.com/media/d0NnEG1WnnXqg/giphy.gif)！新的和旧的一样坏了…

## 一个答案！

考虑到这一点，我重新阅读了我一直看到的异常消息，并注意到两件事:首先，堆栈跟踪包括类型`Sitecore.Pipelines.GetChromeData.GetChromeDataProcessor.GetFieldEditorButton`，因此它一直在给我关于这一点的提示；其次，主要错误是“`Fields field cannot contain any of the following symbols: ():=`”——这给了我一个很大的提示。

所以我切换到核心数据库，并深入到我添加到新渲染中的自定义体验编辑器按钮定义。透过田野，我发现:

[![](../Images/bb2766df5491ed12c193535bc066f0fb.png)T2】](https://jermdavis.files.wordpress.com/2018/09/fieldissue.png)

宾果:在 QA 服务器上，您可以指定由该按钮编辑的数据源字段的字段在它所指定的名称的末尾获得了一个流氓冒号。异常消息确实指出不允许使用冒号(以及括号和等号)。我猜是复制/粘贴错误造成的。

有趣的是，在我的开发机器上，我没有在核心中定制按钮项目(我有一段时间没有在核心中同步项目，因此没有其他人的更改)，并且没有出现“您缺少定义此按钮的项目”错误，我的开发实例只是愉快地继续进行…这解释了为什么我不能在本地重现错误报告。

一旦冒号被删除，项目被保存，一切都回到了正常工作的质量保证。