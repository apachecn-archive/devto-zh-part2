# 在 WordPress 的 WP_Query 中使用‘posts _ where’过滤器

> 原文：<https://dev.to/alexmacarthur/using-the-postswhere-filter-with-wpquery-in-wordpress-4045>

如果你在 WordPress 开发上花了 20 秒钟，你可能已经使用、诅咒并爱上了 WP_Query 类——任何给定的 WordPress 网站或应用程序中最有用、最常用的工具之一。

除了负责任何给定页面或帖子上的主要查询之外，它还是在大量不同上下文中与数据库交互的首选方式。最常见的是，您可能会看到它用于检索数据，如自定义的帖子类型。例如，在一个站点获得所有的猫咪按摩教程:

```
$massageQuery = new WP_Query([
    'post_type' => 'cat_massage_tutorial',
    'posts_per_page' => -1
]); 
```

Enter fullscreen mode Exit fullscreen mode

不仅如此，您还可以使用这个类基于附加到不同帖子的元数据进行稍微复杂一点的查询。让我们得到所有难度等级在 8 以上的猫咪按摩教程:

```
$massageQuery = new WP_Query([
    'post_type' => 'cat_massage_tutorial',
    'posts_per_page' => -1, 
    'meta_query' => [
        [
            'key' => 'difficulty',
            'compare' => '>',
            'value' => 8,
            'type' => 'numeric'
        ]
    ]
]); 
```

Enter fullscreen mode Exit fullscreen mode

这真的很棒，老实说，相对来说很常见。所以，让我们做一些更有趣的事情，收集一些不容易被 WP_Query 查询的帖子。

想象一下，我们想要数据库中所有的“长”猫按摩教程——那些内容至少有一定数量的字符的教程。这些信息没有存储在 post meta 中，所以我们不能依赖 WP_Query 的现成功能。那么，我们如何在数据库中只收集“长”的帖子呢？

### 一个选项:循环遍历所有的东西。

我们*可以*做一些事情，比如获取所有的帖子，然后在我们的 PHP 中循环每一个检索到的帖子，过滤掉特定字符数下的帖子。但是，与我们现有的另一种选择相比，这种方法效率较低，而且有点麻烦。

### 更好的选择:使用‘posts _ where’过滤器来修改 WP_Query 中的‘where’子句。

通过修改 WP_Query 底层的 SQL 查询，可以有效地将工作卸载到应该完成的地方，而不是像在模板逻辑中那样在其他地方处理。

首先，创建一个 WP_Query 对象来查询我们所有的文章。

```
$massageQuery = new WP_Query([
    'post_type' => 'cat_massage_tutorial',
    'posts_per_page' => -1
]); 
```

Enter fullscreen mode Exit fullscreen mode

**在我们的参数中，传递一个`query_label`键和值。这不是 WP_Query 选项——这是我们传递的一个完全任意的键，稍后我们将使用它来标识我们的查询。**T3】

```
$massageQuery = new WP_Query([
    'query_label' => 'our_cat_massage_query',
    'post_type' => 'cat_massage_tutorial',
    'posts_per_page' => -1
]); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要过滤从我们的数据库中检索数据的 SQL 语句的一部分。

```
add_filter('posts_where', function ($where, $query) {
    //-- Stuff will go here.
    return $where;
}, 10, 2); 
```

Enter fullscreen mode Exit fullscreen mode

**使用我们之前传递的`query_label`键，确保我们只在特定的查询上这样做。**T3】

```
add_filter('posts_where', function ($where, $query) {
    $label = $query->query['query_label'] ?? '';
    if($label === 'our_cat_massage_query') {
        //-- More stuff will go here.
    }

    return $where;
}, 10, 2); 
```

Enter fullscreen mode Exit fullscreen mode

**现在，向该查询的`where`子句追加一个条件。**在这里，你显然需要对 SQL 有一个基本的了解。在这种情况下，我们获取内容超过 1200 个字符的所有帖子。

```
add_filter('posts_where', function ($where, $query) {
    $label = $query->query['query_label'] ?? '';
    if($label === 'our_cat_massage_query') {
        global $wpdb;
        $where .= " AND LENGTH({$wpdb->prefix}posts.post_content) > 1200";
    }

    return $where;
}, 10, 2); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的 WP_Query 对象将返回我们在参数中定义的内容，但是在我们如何过滤我们的`where`子句的范围内。所有的*都不需要*熟练使用任何其他 PHP。

### “我什么时候会需要这个？”

我不知道。我们谈论的这个 WordPress 开发项目。时刻准备着。

### “这让我感觉不太好。”

我就在你身边。当进行这样的查询时，创建一个 WP_Query 对象，然后在不同的位置编写一个过滤器就不那么优雅了，我当然愿意接受更有效的方法，所以这样做时不要完全放弃使用这个工具。你有更好的解决办法吗？分享一下！

不管是好是坏，我希望这将有助于在将来使用 WP_Query 完成任务。也就是说，如果你在自己的应用程序中遇到这方面或任何相关的问题，我随时可以为你效劳。[取得联系](https://dev.to/contact)！