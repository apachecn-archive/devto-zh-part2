# 将 PaperCSS 与 ASP.NET 核心 MVC 2.1 一起使用–布局和组件

> 原文：<https://dev.to/andruhovski/using-papercss-with-aspnet-core-mvc-21--layout-and-components-27ap>

在[上一部分](https://dev.to/andruhovski/using-papercss-with-aspnet-core-mvc-21--step-by-step-8k3)中，我们基于 PaperCSS 框架创建了一个简单的 ASP.NET 核心 MVC 应用程序，现在我们添加两个页面来展示我们如何制作自己的布局并使用表格或卡片等组件。

# 布局

PaperCSS 使用类似于 Bootstrap 的命名转换:row，xs-，sm-，md-，lg-，col。

我们改一下 About.cshtml:

```
@{
    ViewData["Title"] = "About";
}
<section class="paper">
    <div class="row flex-middle">
        <div class="sm-6 md-8 lg-10 col">
            <h2>@ViewData["Title"]</h2>
            <h3>@ViewData["Message"]</h3>
        </div>
        <div class="sm-6 md-4 lg-2 col">
            <p>Use this area to provide additional information.</p>
        </div>
    </div>   
</section> 
```

Enter fullscreen mode Exit fullscreen mode

在新版本中，我们创建了一行两列:
[![About Page](img/12549dbf3714aab6339c24294ca9fc36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8DXue5T_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n0apmzdnvw6b0hjfbo9z.png)

此外，我们用 *flex-middle* 类修饰 row，将内容放在单元格的中间。

接下来，我们将更改 Contact.cshtml:

```
@{
    ViewData["Title"] = "Contact";
}
<style type="text/css">
    .contact {
         height: 500px;
     }
</style>

<div class="row">
    <div class="sm-12 md-6 col">
        <section class="paper contact">
            <h2>@ViewData["Title"]</h2>
            <h3>@ViewData["Message"]</h3>

            <address>
                One Microsoft Way<br/>
                Redmond, WA 98052-6399<br/>
                <abbr title="Phone">P:</abbr>
                425.555.0100
            </address>

            <address>
                <strong>Support:</strong> <a href="mailto:Support@example.com">Support@example.com</a><br/>
                <strong>Marketing:</strong> <a href="mailto:Marketing@example.com">Marketing@example.com</a>
            </address>
        </section>
    </div>
    <div class="sm-12 md-6 col">
        <aside class="paper contact">
            <iframe width="325" height="300" src="https://www.bing.com/maps/embed?h=300&w=325&cp=51.478418000000005~-0.18626199999999482&lvl=12&typ=d&sty=r&src=SHELL&FORM=MBEDV8" scrolling="no"></iframe>
            <div style="white-space: nowrap; text-align: center; width: 325px; padding: 6px 0;">
                <a id="largeMapLink" target="_blank" href="https://www.bing.com/maps?cp=51.478418000000005~-0.18626199999999482&amp;sty=r&amp;lvl=12&amp;FORM=MBEDLD">View Larger Map</a> &nbsp; | &nbsp;
                <a id="dirMapLink" target="_blank" href="https://www.bing.com/maps/directions?cp=51.478418000000005~-0.18626199999999482&amp;sty=r&amp;lvl=12&amp;rtp=~pos.51.478418000000005_-0.18626199999999482____&amp;FORM=MBEDLD">Get Directions</a>
            </div>
        </aside>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在此视图中，我们添加 Bing Maps 片段作为第二个单元格，并按高度对齐两个单元格。

# 组件

在本帖中，我们增加了两页来展示如何使用最流行的组件——表格和卡片。出于演示目的，我们将添加事件和演讲者类

```
using System;
namespace PaperCssDemo.Models
{
    public class Event
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string ShortDescription { get; set; }
        public DateTime StartDateTime { get; set; }
        public string Location { get; set; }
        public string ImageUrl { get; set; }
    }

    public class Speaker
    {
        public int Id { get; set; }
        public string FullName { get; set; }
        public string Address { get; set; }
        public string Email { get; set; }
        public string Phone { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

并创建 2 个动作，以表格形式显示卡片样式和扬声器中的事件:

```
public IActionResult Speakers()
{
    var speakers = new List<Speaker>();
    for (var i = 0; i < 10; i++)
    {
        speakers.Add(new Speaker
        {
            Id = i + 1,
            FullName = Faker.Name.FullName(NameFormats.Standard),
            Address = Faker.Address.City(),
            Email = Faker.Internet.Email(),
            Phone = Faker.Phone.Number()
        });
    }

    return View(speakers);
}

public IActionResult Events()
{
    var events = new List<Event>();
    for (var i = 0; i < 15; i++)
    {
        events.Add(new Event
        {
            Id = i + 1,
            Title = Faker.Extensions.ArrayExtensions.Random(new[] { "Meetup", "Conference", "Exhibition", "Party" }),
            ShortDescription = Faker.Lorem.Sentence(6),
            Location = $"{Faker.Address.City()}, {Faker.Address.Country()}",
            StartDateTime = new DateTime(2018, 10, Faker.RandomNumber.Next(1, 28)),
            ImageUrl = $"https://loremflickr.com/320/240?random={i + 1}"
        });
    }
    return View(events);
} 
```

Enter fullscreen mode Exit fullscreen mode

表格表示非常简单。我们可以添加*表格悬停*或*表格交替*来改变表格的样式:

```
@model IEnumerable<PaperCssDemo.Models.Speaker>

@{
    ViewData["Title"] = "Speakers";
}

<h2>Speakers</h2>

<table class="table-hover">
    <thead>
        <tr>
            <th>
                @Html.DisplayNameFor(model => model.Id)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.FullName)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Address)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Email)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Phone)
            </th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Id)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.FullName)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Address)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Email)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Phone)
                </td>
            </tr>
        }
    </tbody>
</table> 
```

Enter fullscreen mode Exit fullscreen mode

扬声器视图将类似于下面的屏幕截图:

[![Table style](img/b63ec37d5240d7069debfdc10e030eee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e6bRyzer--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v5nnqds76jbf6q9csq3h.png)

卡牌风格使用几个类来装饰卡牌:*卡牌*、*卡牌体*、*卡牌标题*、*卡牌副标题*、*卡牌正文*、T10】

```
@model IEnumerable<PaperCssDemo.Models.Event>

@{
    ViewData["Title"] = "View";
}

<div class="row">
    @foreach (var item in Model)
    {
        <div class="sm-6 md-4 col">
            <div class="card">
                <img src="@item.ImageUrl" alt="Card random image">

                <div class="card-body">
                    <h4 class="card-title">@Html.DisplayFor(modelItem => item.Title)</h4>
                    <h5 class="card-subtitle">@Html.DisplayFor(modelItem => item.Location), @Html.DisplayFor(modelItem => item.StartDateTime)</h5>
                    <div style="height:100px">
                        <p class="card-text">@Html.DisplayFor(modelItem => item.ShortDescription)</p>
                    </div>
                    <button>Let me go here!</button>
                </div>
            </div>
        </div>
    }
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在此视图中，我们在中型屏幕上显示每 3 张一排的牌，在小型屏幕上显示每 2 张一排的牌:

[![Card-style](img/f845f6462a4d0de4e2b8fbe99164ab85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nXNVTquL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kuk7d0xckd15rcevqhjn.png)

最后，我们在 Layout.cshtml:
中添加链接

```
<a asp-area="" asp-controller="Home" asp-action="Events">Events</a>
<a asp-area="" asp-controller="Home" asp-action="Speakers">Speakers</a> 
```

Enter fullscreen mode Exit fullscreen mode