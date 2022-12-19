# 自举 4 卡解释

> 原文：<https://dev.to/techiediaries/bootstrap-4-card-explained-16l1>

Bootstrap 4 引入了一个新的 card 组件，它取代了 Bootstrap 3 中的许多旧组件，可用于构建现代风格的 web 布局，而无需重新发明井或成为具有深厚 CSS 知识的 CSS 设计师。现在，多亏了 Bootstrap 4，作为一个具有少量 CSS 知识的开发人员，你可以构建现代和专业的基于卡片的布局。

BS4 卡为显示混合内容(文本、图像、链接等)提供了灵活的容器。)以分组的方式。它们可以通过在 HTML 标记中添加一堆 CSS 类来创建，并且可以很容易地对它们进行样式化和定制，以呈现一种新的和独特的外观。

卡片用于创建高级布局，如石工。你会在许多大的网络平台上找到卡片，如电子商务网站、分析仪表板和博客等。

对于 Bootstrap 用户来说，卡片是流行组件的替代品:面板、缩略图和井(所有这些现在都可以用卡片创建)

在我们开始了解如何创建不同类型的卡之前。这些是关于 BS 4 卡的信息汇总

*   卡片是用来以一种优雅而简洁的方式展示各种内容的
*   卡构建在 Flexbox 之上
*   默认情况下，卡片没有边距
*   卡可以很容易地组织成组，甲板或砖石柱
*   卡片有页眉、页脚和一个或多个正文
*   卡可以很容易地与其他引导组件等对齐并很好地混合。

在本教程中，你将了解新的卡片组件，然后你将看到卡片的不同元素，最后你将使用卡片和卡片列构建一个类似砖石的布局

首先创建一个 HTML 文件，然后从新文件
的引导文档中复制下面的 [starter 模板](https://getbootstrap.com/docs/4.0/getting-started/introduction/)

```
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">

    Bootstrap 4 Card Example
  </head>
  <body>
    <div class="container">   
    <!-- Our Simple Card Goes Here -->
    </div>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们从创建基本卡开始。用类`.card`添加一个`<div>`来创建您的卡组件的外部容器

接下来，在外部容器中，添加页眉、卡体和页脚

您可以通过创建一个`<div>`或`<h*>`标题元素来添加一个标题，然后添加`.card-header`类

您可以通过在`<div>`中添加一个`.card-body`类来创建卡体

对于页脚，您需要添加一个带有`.card-footer`
类的`<div>`

```
<div class="card">
    <div class="card-header">
        Card Header    
    </div>
    <div class="card-body">
        Card Body
    </div>  
    <div class="card-footer">
        Card Footer
    </div>            
</div> 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/bd9fb0f640aed8a36d139475d4a82a07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NZ2LsSuo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/47b24686-6c0b-44ef-88a6-96d6a7f0e87d.png)

请注意，您可以在您的卡上添加一个或多个卡体，也可以将卡体内的内容包装在其他标签中，如`<p>`

```
<div class="card-body">
    <p>
        You can also add a second body
    </p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/275aba1f227d88201c5c19cb5a164d78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y5pDqGg8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/10bf9b6b-f8df-45f6-ba88-75aae9db95d6.png)

当您想要在卡片中创建填充部分时，请使用卡片正文。

使用`.card-img-top`和`card-img-bottom`创建[图像帽](https://getbootstrap.com/docs/4.0/components/card/#image-caps)，它们是存在于卡体
顶部和底部的图像

```
<img class="card-img-top" src="https://source.unsplash.com/daily" alt="Card image top">
<div class="card-body"></div>

<img class="card-img-bottom" src="https://source.unsplash.com/daily" alt="Card image bottom"> 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/1409b22b007a42a7da49c811ac7414c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hAqinb9F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/c179ddec-8d9c-4098-88cd-4eae8c2788b3.png)

您可以看到卡组件占据了其容器部分的整个宽度，但是您可以用不同的方法来控制宽度:

*   使用`width`和`max-width` CSS 属性

```
<div class="card" style="width:20rem;">...</div> 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/f5d8a624eda8eb57556621b9270ac122.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vw_7VUb8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/8cf1a990-222c-4b8c-bc62-4c3f59f153a6.png)

*   使用自举 4 网格

您也可以设置高度，但在大多数情况下，高度需要适合卡片内容的垂直对齐，所以您很少需要设置它。

为了在卡体中添加内容，你还有一堆类(`.card-title, .card-subtitle, .card-text`)，它们可以很容易地设计不同的内容类型，比如卡片标题、副标题和文本。

您可以使用标准的 HTML 标签来设计`.card-text`类
中的文本

```
<div class="card-body">
         <h3 class="card-title">Card title</h3>
         <h4 class="card-subtitle">Card subtitle</h4>
          <p class="card-text">This is a card body 1</p></div> 
```

Enter fullscreen mode Exit fullscreen mode

想要使用帽子图像作为覆盖图吗？你可以简单地用`.card-img-overlay`类
来切换`.card-body`类

```
<div class="card">

<img class="card-img-top" src="https://source.unsplash.com/daily?nature" alt="Card image top">  
    <div class="card-img-overlay">
         <h3 class="card-title">Card 2 title</h3>
         <h4 class="card-subtitle">Card 2 subtitle</h4>
          <p class="card-text">This is a card 2 body </p>
    </div>
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/39c1fa65829244b7da51fb90cc82c33a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GTkTfzK9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/262d90e9-3b7f-45db-a916-5e4dc8e38ceb.png)

您可以使用带有`<ul>`标签
的`nav`、`nav-tabs` | `nav-pills`、`card-header-tabs` | `card-header-pill`类将导航标签和药丸添加到卡片的标题中

```
<div class="card-header">
                <ul class="nav nav-tabs card-header-tabs">
                        <li class="nav-item">
                            <a class="nav-link active" href="#">Tab 1</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" href="#">Tab 2</a>
                        </li>
                </ul>        
            </div> 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/c1bb6a47fa4813308368464ef8a5ee7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DAAnVgIN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/6ca40c82-33b3-4b18-82a4-48eef828d104.png)

您可以使用带有`<a>`元素
的`.card-link`类来添加到卡片内容的链接

```
<div class="card">

<img class="card-img-top" src="https://source.unsplash.com/daily?rock" alt="Card image top">  
    <div class="card-img-overlay white">
         <h3 class="card-title">Card title</h3>
         <h4 class="card-subtitle">Card subtitle</h4>
          <p class="card-text">You can add links to card body</p>
                    <a href="#" class="card-link">Link 1</a>
                <a href="#" class="card-link">Link 2</a>
    </div>
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/40c3759d03d98c98d18ce925df1653b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KzsSh5cR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/abc174b5-652a-48ff-8a6a-74fa46e8c37e.png)

您可以使用列表作为正文内容。例如，这就是你如何创建两个时尚的列表，而不需要任何额外的自定义 CSS

```
<div class="card text-primary border-primary">
  <ul class="list-group">
    <li class="list-group-item">Item 1</li>
    <li class="list-group-item">Item 2</li>
    <li class="list-group-item">Item 3</li>
  </ul>

    </div>   
    <br>
  <div class="card text-primary border-primary">
    <div class="card-body">
      <h2 class="card-title"> My List</h2>
  <ul class="list-group list-group-flush">
    <li class="list-group-item">Item 1</li>
    <li class="list-group-item">Item 2</li>
    <li class="list-group-item">Item 3</li>
  </ul>
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/99868ed25c33c9946654803e92646546.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M_09pPOZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/912616cd-4aec-4573-8b51-b124744c7319.png)

## 如何创建复杂的基于卡片的布局？

单张卡片是展示混合内容的好方法，但是通常你需要展示一组卡片来构建复杂的布局，比如砖石布局。

### 卡组

您可以使用卡片创建一个由多张卡组成的单元。单元的列将采用相同的宽度和高度(这是通过使用 Flexbox 实现的)。

这是一组 3 张牌 [![](../Images/483fa8fbeececd3f5223337f84e7cb0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1g8f81p2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/aa6e0296-4af2-44f2-8e8d-b4e85a8337d8.png) 的例子

在 [CodePen](https://codepen.io) 上看 tech iediaries([@ tech iediaries](https://codepen.io/techiediaries))的笔 [RQjNKr](https://codepen.io/techiediaries/pen/RQjNKr/) 。

### 卡片组

卡片组与卡片组相似，只是一副卡片中的每张卡片不相连

[![](../Images/56bcd5e6b5c4dc7854ee1a1afc318f13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HNd-4okk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.com/images/846528f7-6afe-4f60-9dbb-a913b3de9910.png) 见 [CodePen](https://codepen.io) 上 tech iediaries([@ tech iediaries](https://codepen.io/techiediaries))的笔[rjnkr](https://codepen.io/techiediaries/pen/RQjNKr/)。

### 卡列

Cards Columns 提供了一种分组方式，可以将卡片组织成类似砖石的布局(Pinterest 风格)。你不需要提供额外的 CSS 或 JavaScript/jQuery 代码，只需用`.card-columns`将你的卡片包装在`<div>`中，Bootstrap 4 将为你呈现一个砖石布局。但是请记住，这只是 CSS 而不是 JavaScript 代码，所以布局很简单，卡片从左上右下排序，没有简单的方法改变这种行为，除非你使用的是 Mosonry 插件。

参见下面的钢笔示例

请看 [CodePen](https://codepen.io) 上的 tech iediaries([@ tech iediaries](https://codepen.io/techiediaries))的笔 [Bootstrap 4 砌体布局](https://codepen.io/techiediaries/pen/EQbYpZ/)。

[![](../Images/f9163973755f337415d9ec4db3bd84a5.png)T2】](https://camo.githubusercontent.com/2a7292f550e0f83b8847c697c9d82596fafcbc0e/68747470733a2f2f73637265656e73686f747363646e2e66697265666f7875736572636f6e74656e742e636f6d2f696d616765732f65343364306134312d323839372d343632322d393661392d3938366430366134313762372e706e67)

## 结论

多亏了 Bootstrap 4，当你想要创建现代风格和基于卡片的布局时，即使你刚刚开始使用 HTML 和 CSS，或者你是一个没有广泛 CSS 知识的开发者，你也不会再被吓倒。