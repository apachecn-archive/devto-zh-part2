# 处理 ActiveStorage 直接上传和服务器端表单验证

> 原文：<https://dev.to/drbragg/handling-activestorage-direct-uploads-and-server-side-form-validations-opk>

好吧，这有点不太好。让我先说一下，如果您添加了一些客户端表单验证，可能就没有必要这样做了。尽管如此，有时提交可以通过，很高兴知道当我们重新提交视图时，您可以保持直接上传。

如果有人知道更好的方法来做*请*在下面的评论中告诉我。我找了一会儿，但什么也没找到，所以这就是我现在的处理方式。

如果您正在构建一个 Rails 5.2 应用程序，或者只是一般地关注 Rails，那么您很有可能熟悉 ActiveStorage。如果你通读了 [Rails 文档](https://edgeguides.rubyonrails.org/active_storage_overview.html)，你会发现设置起来真的很容易。甚至[直接上传](https://edgeguides.rubyonrails.org/active_storage_overview.html#direct-uploads)都是疯狂的容易。不太容易，也不太清楚的是，如果我们的提交被控制器踢回，如何处理直接上传。

假设我们有这样的事情发生:
[![ActiveStorage direct upload example](../Images/43a54ea0175acb325e1fc663a1959058.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a6rCp-RV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/soutxoz2nm7zqg9jl0ep.png)

相当标准的铁路材料。除了 ActiveStorage 和 validation 之外的所有东西都是您期望从典型的 scaffold 中得到的。

这一切都将如预期的那样工作，帖子图像的超级酷的特性被直接上传到我们正在使用的任何存储解决方案。

好吧，酷。那么问题是什么呢？

嗯，还记得我们的模型如何验证标题和正文的存在吗？如果我们提交的表格没有要求的信息，我们会陷入困境。

现在，事情变得有点棘手了。当我们的控制器调用`render :new`时，我们的表单将重新填充，除了我们附加的图像。更复杂的是，我们附上的图片已经通过直接上传上传了。这提出了两个问题，第一，如果我们简单地将文件再次附加到表单，它现在将被上传两次。第二，如果我们有任何形式的预览/变体/指示给我们的用户，有一个附加的图像，就像当使用表单进行编辑时，我们会让我们的用户感到困惑。为什么？因为如果我们有条件地渲染图像或改变标签，或者只是告诉用户，是的，帖子附有图像。

```
@post.image.attached?
# => true 
```

Enter fullscreen mode Exit fullscreen mode

等等，什么？

没错。因为是直接上传，所以在 *active_storage_blobs* 表上创建了一条记录，这是应该的。因为我们试图将图像附加到帖子上，所以在*active _ storage _ attachments*连接表上创建了一条记录。不幸的是，由于我们的帖子没有被保存， *record_id* 列为`nil`，因此该附件不会持续。至少在我写这篇文章的时候(或者因为我完全漏掉了一些东西), ActiveStorage 还不够智能，不足以处理无效的附件记录。因此，如果我们纠正表单问题并重新提交，我们将不会有与我们的帖子相关联的图像，在我们的云存储中留下一个孤立的文件，我们的用户会有点困惑(并且可能)感到沮丧。

解决方案？

就像我说的，更像是黑客。如果我们将下面一行添加到 posts_controller 的 else 块中:

```
@image = params[:post][:image] if params[:post][:image].present? 
```

Enter fullscreen mode Exit fullscreen mode

用下面的内容更新我们的视图表单:

```
<% if @image %>
    <%= f.hidden_field :image, value: @image %>
<% else %>
    <%= f.file_field :image, direct_upload: true %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

现在一切看起来像这样:

[![fixed it!](../Images/b76036ea89b3f1d47969c2736f6572bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A2fqa9ZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1qjnivqhizmodi30wb0u.png)

就像魔术一样，事情会比预期的多一点。下次我们尝试在帖子中附加图像时，提交没有标题或正文的表单，然后重新提交我们上传的图像，将会附加图像。

整洁！为什么？

原因原因！以及 Rails/ActiveStore 处理直接上传的方式。直接上传完成后，它发回一个【that Rails 用它在数据库中创建 blob 记录，并通过附件连接表附加到帖子上。sign_id 通过 params 传递给我们的控制器，ActiveStorage 处理所有的魔法。因此，如果我们更新我们的控制器以存储`params[:document][:file]`的值，并更新我们的视图以将该值(如果存在的话)放入表单上的隐藏字段中，我们将能够提交该文件，因为 signed_id 已经存在于 blob 表中，ActiveStorage 足够智能(这次)以仅创建一个附件记录。

如我所说，在表单到达我们的服务器之前，在表单中添加一点 JavaScript 来处理验证是一种可行的方法，但是说实话，如果没有必要，谁会想写 JavaScript 呢？这样，我们的服务器仍然可以作为第二道防线。