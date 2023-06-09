# Umbraco 格栅布局车间

> 原文：<https://dev.to/cgcgames/-----umbraco-grid-layout-workshop-16e>

这篇文章最初发表在[jordanbradleyward.com](http://www.jordanbradleyward.com/umbraco/umbraco-grid-layout-workshop/)

# **Umbraco 网格布局指南**

# 概述

在本指南中，我们将介绍使用网格布局属性的基础知识，配置我们的第一个网格布局，设置自定义设置和样式，如何在内容部分使用它们，以及如何配置内容模板。内容模板在网格布局中特别有用。我们还将看到一个名为“文档类型网格编辑器”的自定义插件，它允许使用文档类型来填充网格单元。从这个位置开始克隆存储库:[https://github.com/cgcgames/InitialUmbracoSite](https://github.com/cgcgames/InitialUmbracoSite)这个项目附带一个基本的乌姆拉科安装和一个已经配置好的主页。

# 设置网格编辑器

在全新安装的 Umbraco 上，没有现成的预配置网格布局。然而，属性编辑器是存在的，它有一个很好的 GUI 设置，可以毫不费力地配置设置。要开始使用，请导航到 Umbraco 后台(如果您需要设置 Umbraco 的帮助，请参考初始开发人员指南)

## 配置网格编辑器

让我们从配置开始，我还将介绍所有不同的设置以及如何使用它们。

1.  从 Umbraco back office 导航到开发人员部分，将鼠标悬停在数据类型文件夹上，然后单击三个点。这将打开一个对话框窗口，选择新的数据类型:![](img/1a32661ee06c0b0f60863466a1c8be7a.png)
2.  从下拉菜单![](img/f6f1b9d249a84eb2a30f2663fd16c225.png)的属性编辑器中选择“网格布局”
3.  您将看到的第一个配置是网格布局，当还没有添加任何内容时，编辑器将有一个布局选择。你通常只需要其中的一个。然而，一些设计/网站可能需要侧边栏布局。为此，我们将保留已经定义的默认布局，但是我们将在配置一些行之后对允许的行进行更改。
4.  让我们添加两个新的行配置，我们希望添加由相等部分组成的两列配置和三列配置。我们使用的列数是 12，这意味着我们需要一个 6，6 行和一个 4，4，4 行。点击'添加行配置'，给它一个两列的名称然后点击'+': ![](img/d785c9d3fe46c8bad649b0f47c65f1d1.png)
5.  将宽度设置为“6”，然后再次点击加号图标:![](img/12084d9321b8d9d7b6babeacd3ae9af9.png)
6.  其他设置可以保留为默认值:![](img/43807f5b3ea7710c9017965e6ef31016.png) **最大项目:**这定义了内容编辑器可以添加到该单元格的最大编辑数量。**删除:**删除可以删除当前选中的单元格。**允许所有编辑:**取消选中该字段允许您定义允许内容编辑者向该单元格添加哪些编辑。
7.  重复上面的设置来创建一个三列的行配置，你现在应该有类似这样的东西:![](img/b7d67ef98ac393e701a8f72b9bf8943d.png)
8.  现在让我们回到“网格布局”部分，我们希望 2 列布局只能将“标题”(全宽)行添加到 4 列单元格，并且只能将“标题”和“两列”行添加到 8 列单元格。单击 2 列布局，选择 4 列单元格并取消选中“允许所有行配置”:![](img/91eb2af75aaf32ffb18fb64587a9f9c9.png)
9.  仅检查标题行配置:![](img/87a0c7a5248d262117d139c403403ed4.png)
10.  选择 8 列单元格，取消选中所有行配置。然后检查标题和两栏配置。完成后，单击提交。

在这个例子中，我们使用 12 列网格；但是，如果需要，可以更改列的大小。如果这样做了，css 和类将需要更新。默认网格适用于 Bootstrap 3，Bootstraps 默认为 12 列网格。所以，我们将离开设置为 12 列网格: [![](img/35c3dc6822a18b92351fa23f50d4b407.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jC_b4kll--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-140.png) 下一节我们将看看设置自定义样式和设置。

## 设置自定义设置和样式

自定义设置和样式允许我们将类、属性和样式注入到网格单元周围的包含 div 中(例如，col-md-4 周围的容器 div)。Umbraco 在这里有一些很好的文档，关于哪些配置设置可用:[https://our . um braco . org/documentation/getting-started/back office/property-editors/built-in-property-editors/Grid-Layout/Settings-and-styles](https://our.umbraco.org/documentation/getting-started/backoffice/property-editors/built-in-property-editors/Grid-Layout/Settings-and-styles)

### 编辑设置

默认情况下，网格布局已经带有一个预定义的设置，该设置允许内容编辑器输入自定义类，这些类将在包装 div 中添加到行或单元格中。要查看这个，点击编辑按钮: [![](img/ebc2c1b098b704ae0c1a2f906951cb44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vE64zmUe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-141.png) 然后你会看到这个 JSON 配置: [![](img/63ec9c681187aa27279a487107a248f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MdvyG-a7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-142.png) **标签:**标签属性是当内容编辑者想要编辑它时会出现的内容。**描述:**这是将出现在内容编辑器中的描述字段。 **Key:** 这是您希望添加到 div 容器的属性(即类、数据定制、aria 标签等) **View:** 这是您希望内容编辑器能够使用的视图(编辑器)，示例:

*   text string–允许用户添加纯文本
*   单选按钮列表–允许用户从单选选项中进行选择
*   image picker–允许用户选择图像

让我们创建一个单选按钮列表，其中包含一些可用于填充数据自定义属性的值。**创建自定义设置**从编辑设置对话框窗口，创建一个新的 JSON 设置块，并用以下代码填充它:

```
[
  {
    "label": "Class",
    "description": "Set a css class",
    "key": "class",
    "view": "textstring"
  },
  {
    "label": "Custom Data",
    "description": "Set a random custom data field",
    "key": "data-custom",
    "view": "radiobuttonlist",
    "prevalues": [
      {
        "label": "Important Value 1",
        "value": "value_1"
      },
      {
        "label": "Important Value 2",
        "value": "value_2"
      },
      {
        "label": "Important Value 3",
        "value": "value_3"
      }
    ]
  }
] 
```

**重要提示:**在添加新的自定义设置之前，请务必在“}”的末尾添加一个逗号。 **Key:** 你可以看到这个键已经被设置为“data-custom”，这意味着它将在 div 容器中生成一个属性，如下所示

. **Prevalues:** The other thing that is different is the addition of prevalues, this is used when certain values need to be predefined. In our case we are using the ‘radiobuttonlist’ which needs the options the content editor can select from pre-defined. After adding in the new setting click submit, if there are no errors in your JSON it should submit successfully, and you will then see the new custom setting: ![](img/d352478b62c87edf56a6f91f5a4b125c.png)

### 编辑样式

默认情况下，Umbraco 带有一个已经预先定义好的样式。这种样式允许内容编辑器将背景图像添加到行或单元格中。样式配置允许您向包含 div 元素添加内联样式。要查看此配置，请单击 Styles 部分中的 Edit 按钮: [![](img/7518310dc66e560f9b3656ebc88bdb0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--__JkIdv2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-144.png) 然后您将看到 JSON 配置: [![](img/86ac119f34c5a2ab48b9e78434164814.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--951ame8G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-145.png) **标签:**标签属性是当内容编辑者想要编辑它时将显示给他们的内容。**描述:**这是将出现在内容编辑器中的描述字段。**键:**这是你要添加的 css 属性(即背景、填充、底部填充、边距等)。**视图:**这是您希望内容编辑器能够使用的视图(编辑器)**修饰符:**修饰符是一个字符串格式化程序，用于修改编辑器的输出，以预先计划或追加额外的值。让我们创建一个底部填充选项，它将允许用户只为行选择一个填充选项。**创建自定义样式**从编辑样式对话框窗口，创建一个新的 JSON 样式块，并用以下代码填充它:

```
[
  {
    "label": "Set a background image",
    "description": "Set a row background",
    "key": "background-image",
    "view": "imagepicker",
    "modifier": "url({0})"
  },
  {
    "label": "Set Bottom Padding",
    "description": "Set the bottom padding for a row",
    "key": "padding-bottom",
    "view": "radiobuttonlist",
    "applyTo": "row",
    "prevalues": [
      {
        "label": "Small",
        "value": "10px"
      },
      {
        "label": "Medium",
        "value": "25px"
      },
      {
        "label": "Large",
        "value": "50px"
      }
    ]
  }
] 
```

你会注意到上面的代码与我们创建的类设置非常相似。我不会深入这个问题的所有细节。唯一的区别是我们更新了标签和描述，使之相关。我们将键设置为 padding-bottom，并为 prevalue 列表设置 create 值，最后，我们将 applyTo 设置为。**apply to:**apply to 用于表示您希望在何处管理此设置，因为我们只希望在行级别添加此设置，所以我们在设置中只定义了行。添加上述代码后，单击 submit。如果所有的 JSON 格式都正确，你将会看到新的样式设置，我们添加了: [![](img/00fe7b912072eeb2550da0124646ece6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L9mwS6CZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-146.png)

### 富文本编辑器设置

最后是富文本编辑器设置，这允许你编辑 RTE 添加到网格编辑器时的样子，我将把这些都保留为默认设置。值得注意的是，这些可以在以后更改和更新。不过，我会将 RTE 的高度调整到 300 像素，我发现这提供了一个更好的编辑体验。[![](img/819fdee276f608bb90bb86118dc049cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fWdo7Qjn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-147.png)

## 内容编辑

为了能够使用网格布局进行内容编辑，我们首先需要将它添加到文档类型中。在这一节中，我们将了解如何向内容页面添加网格布局，以及如何在内容编辑部分使用它。我们将使用概述中列出的项目向 Home 文档类型添加网格布局。

### 给文档类型添加网格

让我们从向文档类型添加网格布局开始。首先导航到 Umbraco 中的设置部分。

1.  从设置部分，点击主页文档类型:![](img/e2c5ba106aa0c87816cfe2ec317e9eee.png)
2.  滚动到底部并点击添加另一个选项卡:![](img/39d3e663bf9e9bcd38681481d55820b8.png)
3.  给标签起你喜欢的名字，在我的例子中是“网格布局”。然后点击“添加属性”:![](img/e266b6d3b2d6aa2e855b07c1c3821402.png)
4.  随便你给这个属性起什么名字，我把我的命名为“网格内容”。然后点击‘添加编辑器’:![](img/b3be7b3735753ff1654902af9a753e27.png)
5.  从“选择编辑器”对话框中，搜索我们刚刚创建的网格布局并选择它:![](img/aa5ab62b3a27ce99fb2d1e5a158c66dc.png)
6.  点击提交，然后点击保存:![](img/49123851ee976c7e141beaabab9909ce.png)
7.  最后，让我们重新排列选项卡，使网格布局选项卡成为第二个选项卡。点击顶部的重新排序:![](img/5318c5ad868b65d339f7712ce43e052c.png)
8.  在网格布局选项卡:![](img/3db1d354df1bac16e2590309f8b17969.png)上将顺序号设置为 10
9.  点击“我已完成重新排序”并保存文档类型。

### 向网格中添加内容

在上一节中，我们向 home 文档类型添加了 Grid Layout 属性，在这一节中，我们将了解如何使用 Grid Layout 向我们的网站添加内容。首先导航到内容部分**，添加第一位内容:**

1.  从内容部分点击主页:![](img/e04c1345828747b3c11d5161e3cc1a93.png)
2.  然后点击网格布局选项卡:![](img/c42c28450e5c354b2d866ee8b55ded06.png)
3.  现在您将看到添加内容的初始屏幕，这允许您选择布局。为此，我们将查看 1 列布局，单击该布局并继续:![](img/4402209ae410a4e59c4d439e11ca0fb9.png)
4.  下一个屏幕是选择一行，选择标题并继续:![](img/f7b311c06fcbb463dcf54b75a05a647c.png)
5.  你现在会看到“添加内容”选项，点击它会显示内容选项:![](img/384c9dab42dc515a2854c98ea5bfea4e.png)

**内容类型概述:**

*   **嵌入:**
    *   这种内容类型允许你粘贴一个 YouTube URL 和嵌入 YouTube 视频，它也可以使用 Vimeo 链接。
*   **标题:**
    *   这种内容类型允许您在布局中放置标题，标题块呈现出其内容具有 h1。这可以在 grid.editors.config.js 文件中更改，稍后将详细介绍。
*   **图像:**
    *   这允许从媒体部分选取一个图像并放在页面上。
*   **宏:**
    *   这允许在网格中添加宏，宏是带有参数的局部视图。它们是在 Umbraco 的“开发人员”部分创建的。关于这些的更多信息可以在这里找到:[https://our . um braco . org/documentation/reference/templating/macros/](https://our.umbraco.org/documentation/reference/templating/macros/)
*   **报价:**
    *   这允许将块引用添加到页面中。
*   **富文本编辑器:**
    *   这将添加一个 RTE 编辑器，允许您添加丰富的内容。与 RTE 数据类型不同，此 RTE 的配置可在网格布局数据类型配置中找到。

对于这个例子，让我们添加一个标题和一个丰富的内容到一个全宽的行。

1.  点击“添加内容”后，选择标题内容类型:![](img/48b527af8ea0f89dcaf5bb36ae0317a3.png)
2.  用你喜欢的任何文本填充标题，我已经在我的标题中放置了“标题 1”。
3.  再次点击“添加内容”，这次选择“富文本编辑器”:![](img/8e5982e7758676fc0850ce1e7447475b.png)
4.  用一些虚拟 lorem ipsum([https://www.lipsum.com/feed/html](https://www.lipsum.com/feed/html))填充 RTE
5.  最后，保存并发布页面。

您现在应该有类似于下面这样的内容: [![](img/e32a890ea8596c26329c6ac772bfd6e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tjN1UaTQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-163.png) **添加新行**现在可以添加新行了，接下来让我们添加 3 列行，为此，单击标题行下面的加号图标: [![](img/1b550dba8038a5510b6af9b37119b3d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kHNhFn3i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-164.png) 然后选择“三列”行: [![](img/5463a91a7ac0890cf5527f2cc6d34f54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HJl7lFkk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-165.png) 用您喜欢的任何内容填充三列，完成后您应该有类似于下面这样的内容: [![](img/1b04426437856f70cc36feb18e406fd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--suy7hMis--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-166.png) 添加后保存并发布内容**注意:**内容还不可见，因为我们需要为主页视图添加一点代码来呈现网格内容。

### 添加设置和样式

在网格布局配置过程中，我们添加了一些自定义设置和样式，让我们快速了解一下如何在内容编辑部分设置和使用它们。**向行中添加样式或设置**要向行中添加样式或设置，首先点击您想要添加这些设置的行。然后你会看到一个齿轮出现在该行的右上角: [![](img/bb674a4c7cdc94e792c5380fb159fba7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0yf1JD2X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-167.png) 点击齿轮，这将打开设置对话框。然后，您将看到我们配置的所有自定义设置和样式。您应该会看到类似这样的内容:[![](img/07ab61527ca7bc38d49f62a363658c7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HI_6JIf8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-168.png)**Class:**Class 属性允许您添加一个将错误换行的结束符。您可以添加任意数量的类，一定要用空格将它们隔开。**自定义日期:**这是我们创建的自定义数据属性，它为您提供了一个单选选项列表。选择其中的一个会将值添加到包含 div 的数据自定义属性中。**Style:**Style 属性允许你设置一个带有背景图片的内嵌样式。这将为行周围的包含 div 设置背景图像。**设置底部填充:**这是我们在前面的步骤中配置的自定义填充样式。这是一个单选选项列表，它允许您选择要添加到包含该行的 div 中的填充量。输入一些随机值，然后单击提交。**向单元格添加样式和设置**要向单元格添加样式或设置，首先点击您想要添加这些设置的单元格。然后你会看到一个齿轮出现在单元格的右上角: [![](img/c1b7ec587129a7da619676d1b5794d70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zM_Bt7Ff--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-169.png) 点击齿轮，这将打开设置对话框。然后，您将看到我们配置的所有自定义设置和样式。您应该看到类似这样的内容: [![](img/e920082a9b410bd12b0268b92fdcf447.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aWq31FnH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-170.png) 注意，您不能设置底部填充，因为我们将其定义为只允许按行填充。添加一些随机设置，保存页面。

## 渲染网格

在这一节中，我们将看看如何渲染我们已经配置好并添加了内容的网格。幸运的是，这很容易，只需要一行代码就可以将网格呈现到页面上。由于我们已经将网格添加到主页中，您需要将代码添加到 Home.cshtml 视图中。导航到 Visual Studio 解决方案中的 Home.cshtml 文件，该文件位于 views 文件夹中: [![](img/00960784b1879b0ca0377b275ecfe3f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uNB8TBne--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-171.png) 您需要添加来呈现网格的代码行如下:@Html。GetGridHtml(模型。Content，“grid content”)“grid content”——这是属性别名的字符串。这可以通过查看文档类型部分中的属性找到: [![](img/afab523285f87462e0d2ec4ea6471254.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jDJpF3Q---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-172.png) 模板的完整代码如下所示:

```
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage<ContentModels.Home>
@using ContentModels = Umbraco.Web.PublishedContentModels;
@{
    Layout = "_Layout.cshtml";
}
<section class="@Model.Content.StyleSelector.ToString().ToLower().Replace(" ", "-")">
    <h1>@(Model.Content.Title ?? Model.Content.Name)</h1>
    @Html.Raw(Model.Content.Content)
    @Html.GetGridHtml(Model.Content, "gridContent")
</section> 
```

最后，因为 Umbraco 网格编辑器使用的是 bootstrap 3，所以您会希望将 CSS 包含在

section of the site. Go to the _Layout.cshtml view file and add this code within the tag:

```
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous"> 
```

保存视图并导航至主页。你现在应该在主页上看到这样的内容: [![](img/2e3e3a055488e0b6fc14444f5cbd8ed5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q13niRq---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-173.png) 如果你检查标题 1 的元素，在 chrome 中右键点击> inspect。您应该看到您针对该行设置的自定义样式和设置: [![](img/998f4e7357e15415abaec2d90c2932d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8_7oHxvb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-174.png)

## 编辑基本加价

在这一节中，我们将看看网格布局的标记在哪里，以及我们如何对代码进行修改。默认情况下，渲染网格编辑器会使用 bootstrap3.cshtml 视图文件，这是在视图->分部视图->网格: [![](img/023280ec103e43ec1e48c158d65d1c13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IGjtrnpk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-175.png) 编辑器分部视图都存在于 Editors 文件夹中。在这一部分可以做很多事情，我将在另一个指南中介绍这一点。然而，在一个更简单的层面上，可以很容易地进行一些小的调整(例如，向一个项目添加一个类或者添加一个包装器 div)。我们来看看 Media.cshtml 编辑器的例子: [![](img/829cb782bdd43b192be3c84cdb8579c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QvaUQDsM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-176.png) 从上图可以看出，局部视图在做几件事。最上面的部分是计算是否配置了任何裁剪设置，如果需要，将它们添加到图像 URL，然后图像在  标签中呈现。如果需要，您可以很容易地将 class= " "添加到 image 标签中，或者将整个  标签包装在一个包含 div 中。当一个图像被添加到网格编辑器中时，这将被渲染。我将制作一个关于编辑网格编辑器的标记的更深入的指南，并调整网格编辑器以很好地与 bootstrap 4 一起工作。

# 网格编辑器文档类型插件

网格编辑器有一个很棒的插件，这个插件允许你添加一个文档类型作为单元格的内容。这允许你制作灵活的内容项目(如图像传送带，手风琴，自定义列表等)。在这一节中，我们将看看如何安装和配置这种文档类型。你可以在这里找到 Umbraco 包页面:[https://our . um braco . org/projects/back office-extensions/doc-type-grid-editor/](https://our.umbraco.org/projects/backoffice-extensions/doc-type-grid-editor/)

## 安装自定义插件

这个包可以通过 NuGet 安装，打开解决方案的包管理控制台(工具-> NuGet 包管理器->包管理器控制台): [![http://www.jordancowley.co.uk/wp-content/uploads/2018/06/word-image-1.png](img/d161cfe48657705ac8afd774b4e820fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wD7OQcC2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/http-www-jordancowley-co-uk-wp-content-uploads-2.png) 在控制台运行以下命令:Install-Package Our。um braco . DocTypeGridEditor[![](img/6bdcd8d5752cd24463011706ecb3cf78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EpbjczKt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-177.png)安装完成后，您应该会看到这个 readme.txt 打开: [![](img/402c426aa8f950e6690bb85bae7cf2ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Blnge1j2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-178.png) 您需要从上面复制代码并粘贴到 grid . editors . config . js(config->grid . editors . config . js):[![](img/9896d73fc134349a4bd9cc4d23cb97de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OsT7rRn7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-179.png)

## 设置说明

我们粘贴在 grid.editors.config 中的上述设置告诉网格编辑器在哪里可以找到插件以及使用什么设置。我们将需要配置其中的一些。所以，我在下面概述了设置控制的基础。

### 名称

Name 配置选项是当他们单击 Add Content 时呈现给内容编辑器的名称，它应该是与内容编辑器相关且易于理解的内容。

### 别名

这是标识编辑器的的别名，应该是唯一的。

### 视图

这是用于后台的视图，也是呈现给内容编辑器的内容。

### 渲染

这是用于此编辑器的渲染，这允许使用自定义渲染而不是默认渲染。

### 图标

Icon 属性定义出现在“添加内容”窗口中时使用的图标。这允许您引用 Umbraco 图标列表中的任何图标。您可以通过将鼠标悬停在后台的图标选择器中的图标上来获取所需的别名。[![](img/ad31d9a17206259337c10b54625719bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aB1WPAKX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-180.png)

### 配置

**AllowedDocTypes:** 在网格编辑器中应该允许选择其文档类型别名的数组。字符串可以是正则表达式模式，以允许在单个条目中匹配文档类型组。ie“Widget $”将匹配别名以“Widget”结尾的所有文档类型。

*   **enablePreview:** 启用在网格编辑器中呈现网格单元的预览。
*   **viewPath:** 设置文档类型网格编辑器在渲染时应该在哪里寻找视图的替代视图路径。默认为/Views/Partials/Grid/Editors/DocTypeGridEditor/
*   **previewViewPath:** 设置文档类型网格编辑器在后台渲染预览时应寻找视图的替代视图路径。默认为/Views/Partials/Grid/Editors/DocTypeGridEditor/Previews/
*   **previewCssFilePath:** 这允许添加自定义 css 文件路径，以便在预览内容时在后台呈现。
*   **previewJsFilePath:** 这允许添加自定义的 JavaScript 文件路径，以便在预览内容时在后台呈现。

## 配置插件

在这一节中，我们将看看如何配置插件来使用两个编辑器，一个允许较小单元格的文档，另一个允许较大单元格的文档。我们将通过命名约定和配置网格来实现这一点，只允许我们为不同大小指定的编辑器。

### 配置设置

看看我们将要使用的吹气配置设置:

```
{
    "name": "Full Width Documents",
    "alias": "docType",
    "view": "/App_Plugins/DocTypeGridEditor/Views/doctypegrideditor.html",
    "render": "/App_Plugins/DocTypeGridEditor/Render/DocTypeGridEditor.cshtml",
    "icon": "icon-item-arrangement",
    "config": {
       "allowedDocTypes": ["BlockFull$"],
       "nameTemplate": "",
       "enablePreview": true,
       "viewPath": "/Views/Partials/Grid/Editors/DocTypeGridEditor/",
       "previewViewPath": "/Views/Partials/Grid/Editors/DocTypeGridEditor/Previews/",
       "previewCssFilePath": "",
       "previewJsFilePath": ""
    }
},
{
    "name": "Block Documents",
    "alias": "blockDocType",
    "view": "/App_Plugins/DocTypeGridEditor/Views/doctypegrideditor.html",
    "render": "/App_Plugins/DocTypeGridEditor/Render/DocTypeGridEditor.cshtml",
    "icon": "icon-item-arrangement",
    "config": {
        "allowedDocTypes": ["Block$"],
        "nameTemplate": "",
        "enablePreview": true,
        "viewPath": "/Views/Partials/Grid/Editors/DocTypeGridEditor/",
        "previewViewPath": "/Views/Partials/Grid/Editors/DocTypeGridEditor/Previews/",
        "previewCssFilePath": "",
        "previewJsFilePath": ""
    }
} 
```

正如你所看到的，我们已经定义了插件两次，每个定义都有自己的名字和别名。但是这里要看的关键设置是"**allowedoctypes**"**:**

*   Block$ -这告诉插件只寻找以 Block 结尾的文档类型。
*   BlockFull$ -告诉插件只对以 BlockFull 结尾的文档类型。

现在，我们可以配置文档类型，并在其名称末尾添加 Block 或 BlockFull。允许它被正确的编辑器拾取。**配置网格**我们还需要配置网格，只允许这些编辑器在正确的网格尺寸上工作。为此，导航到我们之前创建的网格布局数据类型: [![](img/2cc15027e3f2336967959c7d25e6e469.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EuViVTN---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-181.png) 您需要调整每行配置和每行中每个单元格的设置。首先，让我们更新三列行配置的设置。单击配置，选择第一个单元格，取消选中“允许所有编辑器”复选框，然后选择除“全幅文档”编辑器之外的所有编辑器: [![](img/e7fe81957ba7cdea95b2908e53ef2dec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tn5azbRK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-182.png) 对该配置的其他两个单元格重复此操作。这可以通过单击该配置中的其他单元格来完成。更新所有其他行配置以匹配以下信息:

*   头条新闻
    *   不允许“阻止文档”
*   文章
    *   第 4 列:不允许“全幅文档”
    *   列 8:允许所有编辑者
*   两列
    *   第 6 列:不允许“全幅文档”
    *   第 6 列:不允许“全幅文档”

所有设置和配置现在都应该设置好了，保存所有这些设置并继续设置文档类型。

### 配置单据类型

导航到设置部分，并在文档类型文件夹中创建两个新文件夹。调用一个文件夹网格块和另一个网格全宽块: [![](img/b5842c0bf25028734e313ce26ca390f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mVIFucMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-183.png) 然后你应该有这样的东西: [![](img/2d57451d2d043d16483d80777fb1cb2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mD07VS8Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-184.png) 让我们创建两个新的文件类型，可用于全宽块和块编辑器类型。请记住，这些都是文档类型，因此您可以充分利用文档类型(即嵌套内容、内容选择器、媒体选择器、链接选择器等)。)在本例中，我将创建非常基本的文档类型，这并不是真正需要的，因为您可以在不使用文档类型的情况下创建所需的内容。我将创建的文档类型将只包含标题、内容部分和内容选择器。让我们从创建常规网格块的文档类型开始，将鼠标悬停在网格块文件夹上并单击三个点，选择“无模板的文档类型”。我们稍后将手动创建这些模板: [![](img/193d478816f8bf3ff668d730bcd126aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5KrPeBWR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-185.png) 命名新的文档类型，但要确保它以 Block 结尾。我正在创建一个叫做 SimpleBlock 的程序。你应该得到类似这样的结果: [![](img/8ed7d1a1d4dfdaf8f58e3472555f59cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TmzKyYNl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-186.png) 重复这个步骤为网格块创建另一个块，然后为网格全宽块创建两个块，在网格全宽块文件夹中创建它们。您应该得到类似这样的结果: [![](img/578a50eea18adf628903d380f386c188.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fbv982Lp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-187.png) **重要提示:**全宽块以 BlockFull 结束，否则我们配置的 doctype 编辑器不会选择它们。

## 使用文档类型

转到主页上的网格布局。请记住，我们已经将文档类型编辑器限制在各自的字段中。因此，全宽仅适用于 8 或 12 单元格，而块适用于 4、6、8 单元格。在表格布局中，点击 4 个单元格区域之一的“添加内容”: [![](img/994f59854cb5c1b4d3ec51278945acb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hoTBJWqx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-188.png) 您现在会看到一个您也可以访问的新编辑器，对于该字段，它应该是“块文档”: [![](img/269da71a78effd0ba2941a725252bb5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--93HJPXAb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-189.png) 点击该编辑器类型，然后会打开一个对话框，允许您从匹配的文档类型中进行选择: [![](img/d0c0d05958bb7183a1d1b304e384d2e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DyMD_Xj8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-190.png) 选择其中一个并填充内容。这样做之后，单击 submit，您应该会看到类似如下的内容: [![](img/f3d4460119a146ac622079fe5e4b59f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Bdh8LJX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-191.png) 正如您所看到的，内容没有被呈现。这是因为我们还没有设置一个视图来呈现这个内容。

## 渲染内容

为了呈现内容，文档类型插件会查找名称与文档类型别名相匹配的部分视图。目前 doctype 插件正在这个位置寻找局部视图: [![](img/1ffc4872c9ea245d877ca82cadc1542a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5-G_hSkx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-192.png) 值得注意的是，插件将 IPublishedContent 解析为这些局部视图，这意味着它可以被视为您在 Umbraco 中编辑的任何其他模板。这意味着我们需要设置一些。与我们在 Umbraco 中的文档类型别名相匹配的 cshtml 文件。在我的例子中，我将需要以下 4 个视图: [![](img/ecf6ba32a759b41577e6a03a5abfc429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wibJJtnN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-193.png) 由于所有这些块目前都是相同的，它们将具有相同的代码。因此，我将只展示其中一个的代码示例。下面是其中一个块的代码:

```
@*Because an IPublishedContent is parsed into this partial we can use the auto generated models to get a Typed path to our content*@
@inherits Umbraco.Web.Mvc.UmbracoTemplatePage<ContentModels.SimpleBlock>
@using ContentModels = Umbraco.Web.PublishedContentModels;
@*Render the html for this block*@
<div>
    <h2>@Model.Content.Title</h2>
    @Html.Raw(Model.Content.Content)
    <a href="@Model.Content.Link.Url">@Model.Content.Link.Name</a>
</div> 
```

您会注意到，在上面的代码中，我们从自动生成的模型中继承了我们正在渲染的特定块。这允许我们有一个到内容的类型化路径。在[初始开发者指南](http://www.jordanbradleyward.com/umbraco/umbraco-initial-developer-workshop/#post-31-_Toc515895017)中可以找到关于生成模型的更多信息。在添加了包含代码的局部视图之后，返回到主页的内容部分，注意您现在会看到正在呈现的内容: [![](img/deae77ef5163343d7e0510c5589c50b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZFSrEpv7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-194.png)

# Umbraco 内容模板

Umbraco 内容模板允许您用内容预先填充文档类型，并允许用户选择空白页或已经用虚拟内容预先填充的页面。这是为了帮助内容编辑人员了解页面应该如何配置而创建的，这也是添加自定义设置和样式的一个很好的方式，对于一个新手内容编辑人员来说，理解起来可能有点复杂。

## 设置内容模板

内容模板是在 Umbraco 后台的“设置”部分创建和添加的。现在导航到那里，悬停在内容模板上，单击三个点并创建。选择内容文档类型: [![](img/9095cfbe60fbeedf47393132c3ad8ccc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5grNJTCu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-195.png) 点击“内容”后，会看到一个页面部分，看起来像是在创建一个新页面。它具有文档类型的所有相同选项卡和设置。让我们将模板命名为“文章模板”，然后我们将配置这个模板，就像我们要将它用于文章页面一样。让我们配置网格，点击网格布局选项卡: [![](img/1538f03a2834e0397e73e8a38b778b0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jc2is110--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-196.png)

1.  选择一列布局，请参见上一节的截图
2.  选择标题
3.  然后是标题编辑器和富文本编辑器。
4.  单击标题行上的齿轮并添加中等填充设置，请参见添加设置和样式
5.  添加新的“文章”行
6.  向 4 单元格和 8 单元格部分添加标题编辑器
7.  向 4 单元格区域添加报价编辑器
8.  添加带有 4 单元格部分链接的自定义文档类型，请参见使用文档类型
9.  向 8 单元格区域添加一个富文本编辑器
10.  向 4 单元格区域添加自定义的侧栏类:![](img/2b1a9161eda77f61be415472da75d77a.png)

你应该得到一个类似这样的模板: [![](img/68f698cde747e98c0ae57949fddf9685.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XR6F1eJH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-198.png) 正如你所看到的，我们添加了一些自定义设置，如果你依赖这些设置，这将非常有用。预定义它们意味着内容编辑者不会忘记它们。设置模板还允许您预定义布局，有助于保持整个站点的一致性。保存内容模板并继续下一部分。保存后你应该看到它出现在树中: [![](img/cd2a87e672fa79a7c37a85c600e4c535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8HArTSBo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-199.png)

## 使用模板

在本节中，我们将了解在添加内容时如何使用该模板。导航到“内容”部分，悬停在主页上，点击三个点并选择内容: [![](img/1928328446993f1af44448c3630ca4d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uG-5WjaL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-200.png) 点击内容后，您将看到一个二级选择。该选项允许您从预定义的内容模板中进行选择: [![](img/14a8a32d095eab4674d4f927a8b6af67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--22pS4Mxk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-201.png) 选择我们刚刚在上一步中设置的文章模板，然后导航到“网格布局”选项卡。请注意，我们配置的模板现在已经预填充了该页面。允许您开始编辑我们已经创建的字段: [![](img/3e3b0bfa23b37d1dce7cda77603b9633.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NNg3xUqD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jordanbradleyward.com/wp-content/uploads/2018/06/word-image-202.png) 愉快的内容编辑！😊