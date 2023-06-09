# WooCommerce & WordPress 中的自然排序术语

> 原文：<https://dev.to/mavrakisk/natural-sort-terms-in-woocommerce--wordpress-3m56>

如果你正在寻找一种自然排序 WooCommerce 中产品的术语/属性的方法，或者甚至是分类，我想出了一种自然排序的方法。例如，您的产品中有一些属性，比如说数字列表。您的列表可能如下所示:

1.  one
2.  Two
3.  Two hundred and five
4.  One hundred and two
5.  One hundred and ten

将会发生什么，列表将显示为(默认排序):

1.  one
2.  Two
3.  One hundred and two
4.  One hundred and ten
5.  Two hundred and five

为了按照自然顺序对列表进行排序，如下所示:

1.  one
2.  Two
3.  One hundred and two
4.  One hundred and ten
5.  Two hundred and five

您只需将`get_terms()`的结果转换成一个数组，然后对其进行相应的排序。

```
<?php

$args = array(
  'order' => 'ASC',
  'orderby' => 'name',
  'hide_empty' => true,
);

$filter = get_terms( 'pa_filter', $args );

foreach ( $filter as $term ) {
    $sort_terms[$term->name] = $term;
}

uksort( $sort_terms, 'strnatcmp');

foreach ($sort_terms as $term) {
    //If you want to display an option list
    echo "<option value=" . $term->name . ">" . $term->name . "</option>";
} 
```

Enter fullscreen mode Exit fullscreen mode

照片由[edu·格兰德](https://unsplash.com/photos/0vY082Un2pk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄