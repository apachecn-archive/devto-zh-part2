# 。网络核心–下拉列表(选择)不会填充

> 原文：<https://dev.to/catriname/net-core--drop-down-select-wont-populate-2d07>

我最近有一个 bug，花了我很长时间才解决，而问题的根源是我自己。我删除的时候有点高兴，删除了一个重要的文件，这个文件允许我使用 Razor 标签助手将一个列表作为下拉菜单(Select)带入视图。

# 错误

错误是这样出现的:

*   选择列表不会填充
*   选择或下拉列表为空
*   价值=微软。AspNetCore . MVC . rendering . select list
*   价值=系统。linq . orderedenumerable 2[微软。AspNetCore . MVC . rendering . selectlistitem，System。字符串]

# 我的代码

这就是我通常如何将一个列表放入我的视图中，并且一旦实现了解决方案，代码就工作了。

### 控制器:

```
 ViewBag.ProductLines = productLineRepository.GetAll().Select(r => new SelectListItem {
         Text = r.Name, 
         Value = r.Id.ToString() 
    }).OrderBy(x => x.Text).ToList(); 
```

Enter fullscreen mode Exit fullscreen mode

### 查看:

```
 <select name="ProductLine" id="ProductLine" asp-for="ProductLine" asp-items="@ViewBag.ProductLines">
    <option value="0">Select One...</option>
    </select> 
```

Enter fullscreen mode Exit fullscreen mode

# 解

这个解决方案有点尴尬:我删除了 _ViewImports.cshtml 视图，其中包含一个启用标记助手的导入。列表未呈现，因为未启用标记帮助程序。

要解决这个问题:

*   在视图文件夹下直接创建一个名为“_ViewImports.cshtml”的文件
*   添加以下内容:

```
@using MyNameSpace @addTagHelper \*, Microsoft.AspNetCore.Mvc.TagHelpers 
```

Enter fullscreen mode Exit fullscreen mode

*   保存文件并刷新。

相当简单的修复！