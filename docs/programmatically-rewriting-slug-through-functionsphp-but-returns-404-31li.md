# 通过 functions.php 编程重写 slug，但返回 404

> 原文：<https://dev.to/jjjjcccjjf/programmatically-rewriting-slug-through-functionsphp-but-returns-404-31li>

我想将某个帖子类型的 slug(在本例中为 **article** )更改为其自定义字段中的某个值。

我去*functions.php*放了这段代码。

```
function na_remove_slug( $post_link, $post, $leavename ) {

if ( 'article' != $post->post_type || 'publish' != $post->post_status ) {
    return $post_link;
} 

# get the custom field value here to be the slug
$post_name = get_field('filter_category', $post->ID)->post_name; 
# replace the "article" by the value of the custom field
$post_link = str_replace( '/' . $post->post_type . '/', "/$post_name/", $post_link ); 
    return $post_link;
}
add_filter( 'post_type_link', 'na_remove_slug', 10, 3 ); 
```

Enter fullscreen mode Exit fullscreen mode

当我查询自定义帖子类型时，它的 slug 好像被修改了，例如:
**【www.example.com/article/some-title】**变成了

其中**设计**是我的自定义字段值。

但是当我最终访问的时候，我得到了一个 404。但是如果我访问 www.example.com/article/some-title 的，它会返回正确的网页。

我知道我的代码只是改变了链接显示时的样子，但是我如何使用我修改过的 slug 正确地显示我的单篇文章呢？

如果你需要更多的澄清，请随时评论。