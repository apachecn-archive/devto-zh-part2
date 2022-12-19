# [解决方法]如何用 Blazor SPA (v.0.5.x)进行单选按钮输入的双向数据绑定？

> 原文：<https://dev.to/j_sakamoto/workaround-how-to-two-way-data-binding-of-radio-button-input-with-blazor-spa-v050-31fd>

## 简介:“Blazor”是什么？

“Blazor”是一个单页面 Web 应用程序(也称为“SPA”)框架。

Blazor 允许你运行 C#代码和。NET Standard 2.0 本机程序集(。dll)放在现代的 web 浏览器上，没有任何。NET 服务器进程。

如果你是 Blazor 的新手，我推荐你看这个 YouTube 视频。

[NDC 奥斯陆 2017 -“网络应用不能真的做*那个*，对吧？——史蒂夫·桑德森”](https://youtu.be/MiLAE6HMr10?t=31m59s)

视频讲的是 Blazor 的原型，我写这篇帖子时的当前版本是 v.0.5.1，有一些突破性的变化。但那个视频对理解“Blazor”的概念和利弊是有用的。

## Blazor 的双向数据绑定

Blazor 是一个 SPA 框架，所以你可以很容易地双向绑定一个数据到 input 或者 select/option 元素，就像这样:

```
@functions {
    int EditionCode = 1;
}

<select bind="@EditionCode">
  <option value="1">Home</option>
  <option value="2">Pro</option>
  <option value="3">Enterprise</option>
</select> 
```

Enter fullscreen mode Exit fullscreen mode

Blazor 通过**“bind = ...”提供了那些双向数据绑定机制**语法。

## “绑定”语法对单选按钮输入无效！

但是，作为一个例外，您不能通过“bind= ...”将双向数据绑定到单选按钮输入元素语法，此时(Blazor v.0.5.x)。

以下示例代码不起作用。

```
@functions {
    int EditionCode = 1;
}

<label>
  <input type="radio" name="edition" value="1" bind="@EditionCode"/> Home
</label>
<label>
  <input type="radio" name="edition" value="2" bind="@EditionCode"/> Pro
</label>
<label>
  <input type="radio" name="edition" value="3" bind="@EditionCode"/> Enterprise
</label> 
```

Enter fullscreen mode Exit fullscreen mode

我可以预计上述代码在不久的将来会运行良好，但 Blazor v.0.5.x 还不允许使用该代码。

我们能做什么？

## 解决此问题的方法

别担心。有变通办法。

单选按钮输入元素的双向数据绑定如下:

```
@functions {
    int EditionCode = 1;
}

<label>
  <input type="radio" name="edition" checked="@(EditionCode == 1)" onchange="@(() => EditionCode = 1)"/> Home
</label>
<label>
  <input type="radio" name="edition" checked="@(EditionCode == 2)" onchange="@(() => EditionCode = 2)"/> Pro
</label>
<label>
  <input type="radio" name="edition" checked="@(EditionCode == 3)" onchange="@(() => EditionCode = 3)"/> Enterprise
</label> 
```

Enter fullscreen mode Exit fullscreen mode

代码`checked="@(EditionCode == ...)"`作为“从数据到 DOM”的单向绑定工作。

另一方面，代码`onchage="@(() => EditionCode = ...)"`作为“从 DOM 到数据”的单向绑定工作。

通过组合这两个代码，可以实现到单选按钮输入双向绑定。

当然，你也可以动态构建单选按钮输入，就像这样:

```
@functions {
    int EditionCode = 1;

    // "Edition" class has "int Code" and "string Text" properties.
    Edition[] Editions = new [] {
      new Edition { Code = 1, Text = "Home" },
      new Edition { Code = 2, Text = "Pro" },
      new Edition { Code = 3, Text = "Enterprise" },
    };
}

@foreach (var edition in this.Editions)
{
<label>
  <input type="radio" 
         name="edition"
         checked="@(EditionCode == edition.Code)"
         onchange="@(() => EditionCode = edition.Code)"/>
         @edition.Text
</label>
} 
```

Enter fullscreen mode Exit fullscreen mode

这种变通方法并不酷也不聪明，但不管怎样，它工作得很好。

我认为 Blazor 的未来版本将支持单选按钮输入的“bind”syntx。

另请参见:

# [![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png) 绑定尚未处理的单选按钮 #5579](https://github.com/dotnet/aspnetcore/issues/5579) 

[![MarkStega avatar](../Images/abd5586571583628c8897c5946d0f3b1.png)](https://github.com/MarkStega) **[MarkStega](https://github.com/MarkStega)** posted on [<time datetime="2018-08-16T13:00:32Z">Aug 16, 2018</time>](https://github.com/dotnet/aspnetcore/issues/5579)

我的 cshtml 中有如下内容

```
 <td>
        <input type="radio" name="vacation" bind="@northwestVacation" /> Northwest @northwestVacation<br>
        <input type="radio" name="vacation" bind="@sinaiVacation" /> Sinai @sinaiVacation
    </td> 
```

在我的代码中有相应的属性`northwestVacation`和`sinaiVacation`。在调试过程中，我看到`get`被多次调用，但是即使有了`true`值，相应的单选按钮也没有处于选中状态。而且，当我选择任何一个按钮时，我都不会看到在任何一个属性上调用`set`。

[View on GitHub](https://github.com/dotnet/aspnetcore/issues/5579)

让我们一起期待 Blazor 的新发布吧！

用 Blazor 快乐编码:)