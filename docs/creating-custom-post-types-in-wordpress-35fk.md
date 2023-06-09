# 在 Wordpress 中创建自定义文章类型

> 原文：<https://dev.to/enriquemorenotent/creating-custom-post-types-in-wordpress-35fk>

一个网站需要不同种类的数据并不罕见。Wordpress 自带“文章”和“页面”，但是我们可能需要更多。

很多时候，这是通过**类别**来实现的，因为它们提供了一种区分帖子内容及其内容的方式。

但是，如果我们开始在帖子中添加不同的数据结构，会发生什么呢？假设我们想在我们的 Wordpress 系统中定义“城市简介”。一个城市可能会有非常不同的自定义字段。比如说，`location`，`amount of population`，`continent`。

如何添加自定义字段的主题本身值得另一篇文章来讨论，但是假设我们已经知道了。我们可以在“城市”类别中分离代表一个城市的帖子，但是这使得数据管理变得非常困难。人们每次都必须浏览所有的帖子以获得所需的数据，并正确设置过滤器以到达人们正在寻找的帖子。

有更好的方法。

首先让我们注册一种新的帖子类型:

```
<?php

// functions.php

add_action('init', 'register_custom_posts_init');

function register_custom_posts_init() {

    $cities_labels = array(
        'name' => 'Cities',
        'singular_name' => 'City',
        'menu_name' => 'Cities'
    );

    $cities_args = array(
        'labels' => $cities_labels,
        'public' => true,
        'capability_type' => 'post',
        'has_archive' => true,
        'supports' => ['title', 'editor']
    );

    register_post_type('city', $cities_args);
} 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就创建了一个包含所有信息的“城市”结构。如果我们现在在 Wordpress 后端看，我们会看到一个新的菜单项被添加，叫做“城市”。在那里，我们可以添加属于该数据结构的所有条目，而不会受到其他类型的 post 的干扰，也不用担心设置任何其他类型的标志来设置我们的新数据条目。

现在，要查询信息，我们可以这样做:

```
<?php

$query = new WP_Query([
    'post_type' => 'city',
    'order' => 'ASC',
    'posts_per_page' => 100
]);

$cities = $query->posts;
?> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以直接通过帖子类型进行查询，这是一个非常容易编写的查询。

为我们的新帖子类型声明一个“详细视图”怎么样？通常对于普通的文章，我们会使用一个文件`single.php`，但是因为我们使用定制的文章类型，我们必须声明一个名为`single-city.php`的文件。在这个文件中，我们将显示一个条目的详细信息。

**通知！**要记住一件重要的事情。创建新的文章类型后，最好去`Settings > Permalinks`更新设置。配置可能需要刷新以识别新的 post 类型。一个经常发生在我身上的错误。

谈到 Wordpress 开发，这是一种新的表示数据的方式，我不会很快忘记。

干杯