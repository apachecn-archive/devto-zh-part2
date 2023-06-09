# 使用 ReSharper 文件布局应用 StyleCop 排序

> 原文：<https://dev.to/collinbarrett/applying-stylecop-ordering-with-resharper-file-layout-5521>

[![StyleCop with ReSharper](img/636e8608701c9f4ba5db2abdc2147614.png)T2】](https://collinmbarrett.com/stylecop-ordering-resharper/)

在我的 C#类中，我一直在纠结成员的理想排序。对成员进行排序显然是一个清洁/可维护的因素，而不是一个功能因素，但是我确实想在我的类中创建一致性。

作为 ReSharper (R#)的重度用户，我经常通过[代码清理](https://www.jetbrains.com/help/resharper/Code_Cleanup__Index.html)应用他们默认的 C# [文件布局](https://www.jetbrains.com/help/resharper/File_and_Type_Layout.html)来自动重新排列成员。R#的默认文件布局相当不错，但我更喜欢使用微软的 [StyleCop](https://en.wikipedia.org/wiki/StyleCop) 排序指南。例如，R#没有默认按照访问级别对属性进行排序，我发现这对于一致性很有价值。

我发现网上有一些博客帖子和资源在做类似的事情，但是没有一个是最近的，也没有一个是完全符合的。此外，JetBrains 确实为 R#发布了一个 [StyleCop 扩展](https://plugins.jetbrains.com/plugin/11619-stylecop-by-jetbrains)，但是它的范围似乎并不直接适用于单独应用文件布局。

## StyleCop 的排序规则

[style co analyzer 项目](https://github.com/DotNetAnalyzers/StyleCopAnalyzers)有一个规则子集，我已经将其转换用于 R#文件布局。这几条规则是经过精心挑选的，仅在满足以下要求的意义上:

*   适用于类、结构或接口内部
*   受 R#的文件布局特性支持
*   StyleCop 不推荐使用

### [SA1124](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1124.md) :不使用区域

在 R# XAML 中，这是通过在`TypePattern`节点中添加`RemoveRegions`标志来实现的。

```
&amp;lt;TypePattern DisplayName="StyleCop Classes, Interfaces, &amp;amp;amp; Structs" RemoveRegions="All"&amp;gt; 
```

Enter fullscreen mode Exit fullscreen mode

### [SA1201](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1201.md) :元素必须以正确的顺序出现

按以下顺序按类型对元素进行排序:

*   菲尔茨
*   构造器
*   终结器(析构器)
*   代表
*   事件
*   枚举
*   接口
*   性能
*   索引器
*   方法
*   结构
*   班级

在 R# XAML 中，这是通过`Entry`节点的顺序实现的。例如，将字段放在构造函数之前:

```
&amp;lt;Entry DisplayName="Fields"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Field" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Constructors"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Constructor" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;/Entry&amp;gt; 
```

Enter fullscreen mode Exit fullscreen mode

### [SA1202](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1202.md) :元素必须按访问排序

按以下访问级别顺序对相同类型的相邻元素进行排序:

*   公众的
*   内部的
*   受保护内部
*   保护
*   私人的

在 R# XAML 中，这是通过`Entry.SortBy`下的`Access`节点实现的，比如下面的字段:

```
&amp;lt;Entry DisplayName="Fields"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Field" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt; 
```

Enter fullscreen mode Exit fullscreen mode

### [SA1203](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1203.md) :常量必须出现在字段之前

在 R# XAML 中，这是通过常量和字段`Entry`节点的顺序实现的。

```
&amp;lt;Entry DisplayName="Constants"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Constant" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Fields"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Field" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;/Entry&amp;gt; 
```

Enter fullscreen mode Exit fullscreen mode

### [SA1204](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1204.md) :静态元素必须出现在实例元素之前

在 R# XAML 中，这是通过`Entry.SortBy`下的`Static`节点实现的，比如下面的字段:

```
&amp;lt;Entry DisplayName="Fields"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Field" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt; 
```

Enter fullscreen mode Exit fullscreen mode

### [SA1214](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1214.md) :只读元素必须出现在非只读元素之前

在 R# XAML 中，这是通过`Entry.SortBy`下的`Readonly`节点实现的，比如下面的字段:

```
&amp;lt;Entry DisplayName="Fields"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Field" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt; 
```

Enter fullscreen mode Exit fullscreen mode

## 最终重磨文件布局 XAML

将上述规则直接转换成 ReSharper C#文件布局 XAML 会产生以下类型模式(也可作为[要点](https://gist.github.com/collinbarrett/fcc004c386867135c06e26e449f73ba2) ):

```
&amp;lt;TypePattern DisplayName="StyleCop Classes, Interfaces, &amp;amp;amp; Structs" RemoveRegions="All"&amp;gt;&amp;lt;TypePattern.Match&amp;gt;&amp;lt;Or&amp;gt;&amp;lt;Kind Is="Class" /&amp;gt;&amp;lt;Kind Is="Struct" /&amp;gt;&amp;lt;Kind Is="Interface" /&amp;gt;&amp;lt;/Or&amp;gt;&amp;lt;/TypePattern.Match&amp;gt;&amp;lt;Entry DisplayName="Constants"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Constant" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Fields"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Field" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Constructors"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Constructor" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Destructors"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Destructor" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Delegates"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Delegate" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Events"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Event" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Enums"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Enum" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Interfaces"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Interface" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Properties"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Property" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Indexers"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Indexer" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Methods"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Method" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Structs"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Struct" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;Entry DisplayName="Classes"&amp;gt;&amp;lt;Entry.Match&amp;gt;&amp;lt;Kind Is="Class" /&amp;gt;&amp;lt;/Entry.Match&amp;gt;&amp;lt;Entry.SortBy&amp;gt;&amp;lt;Access Order="Public Internal ProtectedInternal Protected Private" /&amp;gt;&amp;lt;Static /&amp;gt;&amp;lt;Readonly /&amp;gt;&amp;lt;/Entry.SortBy&amp;gt;&amp;lt;/Entry&amp;gt;&amp;lt;/TypePattern&amp;gt; 
```

Enter fullscreen mode Exit fullscreen mode

## 在 ReSharper 中保存文件布局

1.  在 Visual Studio 中，导航到`ReSharper`->T1
2.  导航至`Code Editing`->-`C#`->-`File Layout`
3.  点击右上角的`XAML`
4.  按照您希望的顺序，将上面的 TypePattern 复制并粘贴到 XAML 中(例如就在“默认模式”之前)
5.  点击右上角的`Designer`确认新的类型模式显示正确
6.  保存您的更改

<figure>[![StyleCop ReSharper File Layout TypePattern](img/54e1600c043fd3341c21a0ea8c96a291.png)](https://collinmbarrett.com/media/styleCopResharperFileLayout_collinmbarrett.jpg)

<figcaption id="caption-attachment-6763">StyleCop ReSharper 文件布局类型模式</figcaption>

</figure>