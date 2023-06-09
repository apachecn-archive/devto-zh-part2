# 使用 Hugo 的文章推荐

> 原文：<https://dev.to/cirowrc/article-recommendation-using-hugo-3974>

嘿，

这个月，我一直对增加用户在博客中导航的总时间感兴趣。

作为文章推荐，这是博客所缺少的，我去做了。

[![Article recommendation in use](img/db1e126eca498c5b145090c9fc1a92e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---PUv2rhe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ops.tips/blogimg/recommended-articles.svg)

看看你是如何为你的静态网站做到这一点的！

### 想法

鉴于我的内容通常被标记，我认为添加文章推荐的一个简单方法是简单地将标记有与当前文章相同的标签的文章进行合并，然后随机列出它们。

例如，考虑本文的情况:[Go](https://ops.tips/blog/udp-client-and-server-in-go/)中的 UDP 服务器和客户机。

作为被标记为 [Linux](https://ops.tips/tags/linux) 、[网络](https://ops.tips/tags/networking)和 [Go](https://ops.tips/tags/go) 的文章，我们可以推断有三个内容池可能与本文中的内容相似(它们之间有一些重叠)。

[![Overlap between bag of articles by selecting articles with tags matching](img/9c6253a30b81dc3ab07645f7e3ce8b40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1f49WsJ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ops.tips/blogimg/recommendation-overlap.svg)

向前看，我们可以认为，一旦我们解决了可能推荐的文章，下一步是优先选择其中的一些:在更多类别中对那些有更多重叠的文章排名更高。

那我们就实施吧。

### 在 Hugo 中实现推荐文章列表

作为第一步，我们检索所有不在当前页面的页面:

```
// Assign the current scope (the current page) to
// a variable named `$currentArticle` so that within
// other scopes we are able to still reference the 
// current page.
{{ $currentArticle := . }}

// From the list of all pages from the site, only keep
// those whose name is different from the name of the
// current article and whose kind is `page`.
//
// ps.: here we could check something else, like permalink
// or another unique identifier.
// 
// ps.: here you'd probably pick a specific section. To do
// so, perform another `where`.
{{ $articles := where 
        (where $.Site.Pages ".Kind" "eq" "page") 
        ".Title" "!=" $currentArticle.Title }} 
```

注意。:在**上面的`where`必须是**内联的。这里我把它分成多行，只是为了获得更好的可读性。

接下来，我们现在创建两个列表:

1.  一个引用当前文章的标签集中具有至少两个标签的所有文章；和
2.  一个列表，引用当前文章标签集中带有单个标签的**的所有文章。**

我们可以称这两个`veryRelevantArticles`和`relevantArticles`(据此):

```
// Instantiate each of them with an empty slice.
{{ $veryRelevantArticles := slice }}
{{ $relevantArticles := slice }} 
```

设置好变量后，我们现在可以开始遍历所有文章页面的列表，并检查它们与当前页面的标签集有多少交集:

```
// Iterate over each of the articles from the list 
// of article pages
{{ range $idx, $article := $articles }}
        // Compute the number of tag intersactions.
        {{ $numberOfIntersections := len (
                intersect $article.Params.tags $currentArticle.Params.Tags
        ) }}

        // For those pages with a big number of 
        // intersections (>= 2), put in the first
        // slice.
        {{ if (ge $numberOfIntersections 2) }}
                {{ $veryRelevantArticles = 
                        $veryRelevantArticles | append $article }}
        // For the rest (single intersaction), put in the 
        // second slice.
        {{ else if (eq $numberOfIntersections 1) }}
                {{ $relevantArticles = 
                        $relevantArticles | append $article }}
        {{ end }}

        // note.: I'm ignoring those with 0 intersections.
{{ end }} 
```

一旦我们得到了所有感兴趣的文章，现在我们可以创建一个有序列表，从推荐数量最多的开始，到推荐数量最少的，也就是说，我们可以创建一个列表，它对应于我们上面创建的两个变量的串联:

```
// Create an empty slice to hold the final list
{{ $recommendedArticles := slice }}

// For each very recommended article, append to the
// list.
{{ range $veryRelevantArticles }} 
        {{ $recommendedArticles = $recommendedArticles | append . }} 
{{ end }}

// For each recommended article, append to the
// list.
// 
// This will lead to something like 
// [very, very, rec, rec, rec....]
{{ range $relevantArticles }} 
        {{ $recommendedArticles = $recommendedArticles | append . }} 
{{ end }} 
```

随着列表的创建，现在是展示它的时候了。

### 显示推荐列表

有了一个列表，首先包含交集最多的那些，然后是交集较少的那些，我们可以继续显示它们。

在这篇博客中，我采用的方法是展示从这个列表中挑选出来的前五个。

```
<ul>

// For every article in the set of the first 5
// recommended articles shuffled, show their
// anchor.
{{ range (shuffle (first 5 $recommendedArticles)) }}
<li>
  <a href="{{ .Permalink }}">
    {{ .Title }}
  </a>
</li>
{{ end }}

</ul> 
```

这保证了如果我有一些文章与当前文章的内容有很大的交集，它们会被显示出来(即使是无序的)。

如果你有很多内容和标签，你可能想要创建更多的类别(不仅仅是`veryRelevant`和`relevant`)。

在这种情况下，可以做一些更复杂事情。

### 关闭思绪

有趣的是，看看没有“一门真正的语言”我们能完成多少。

虽然 Hugo 给了我们一些简单的原语，但我们可以在此基础上构建并获得一些非常令人满意的结果。

你呢？你曾经做过类似的事情却做了不同的事情吗？请让我知道！

另外，如果你有任何问题，请随时在 Twitter 上给我发消息，地址是 [@cirowrc](https://twitter.com/cirowrc) 。

祝你愉快！