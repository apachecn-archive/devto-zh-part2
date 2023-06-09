# ACF 中继器&灵活的内容域

> 原文：<https://dev.to/leanminmachine/wordpress-acf-repeater--flexible-content-fields-4dpa>

所以基本上，我的一个客户希望我做一些事情，比如有一个内容字段(repeater: [ACF | Repeater](https://www.advancedcustomfields.com/resources/repeater/) )，他可以添加任意数量的行，并在前端显示出来。

[![](img/55d27a7438cd0e09e793aa18fed0cbb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8FARDm85--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d8jxp3ej7n91oxcuog61.png) 
*只是我应该做的一个例子…基本上我的客户希望有多个部分，我不知道他想要多少部分，这是一个可变的数字*

与此同时，我一直可耻地使用预先固定的名称，因为我原本打算让我的客户做的只是编辑预定义字段本身的内容。因为我很懒。:(

经过几个小时的研究，我似乎已经解决了我的问题..？

首先，我使用了便捷的 Google，我得到了这样一个搜索结果:[列出 Repeater 或 Flex 字段中的所有子字段——ACF 支持](https://support.advancedcustomfields.com/forums/topic/list-all-sub-fields-in-a-repeater-or-flex-field/)。我知道了，哦，Repeater 字段实际上是由一个数组组成的。这意味着我可以迭代显示我想要的子字段！

问题是，我到底该怎么做呢？使用 php 的 **var_dump($array)** 来‘检查’数组似乎返回给我这个(对于 repeater 数组):

```
array(1) { [0]=> array(2) { ["performance_parameters"]=> string(35) "
Performance parameters here

" ["application_area"]=> string(29) "
Application area here

" } } 
```

Enter fullscreen mode Exit fullscreen mode

不知何故，数组结构似乎不像 ACF 支持页面上显示的那样:(试图进一步访问数组导致我这样做(使用$array[0]):

```
array(2) { ["performance_parameters"]=> string(35) "
Performance parameters here

" ["application_area"]=> string(29) "
Application area here

" } 
```

Enter fullscreen mode Exit fullscreen mode

如果我使用$array[0][0]，我会返回 NULL。

说实话，我还是不完全确定为什么。我试着阅读索引和关联数组。但是如果我的字段/键名不是预先固定的，我该如何检索这些值呢？

更多的谷歌搜索出现了以下几个搜索结果:[如何在 PHP 中回显数组？-堆栈溢出](https://stackoverflow.com/questions/9816889/how-to-echo-an-array-in-php) &对我的用例来说更精确的一个， [wordpress -如何在高级定制字段中列出一个带有标签和值的 repeater 子字段？-堆栈溢出](https://stackoverflow.com/questions/36723013/how-to-list-a-repeater-sub-fields-with-label-and-value-in-advanced-custom-field)。最后，主要思想是使用 foreach 迭代和提取值，结构如下:

```
foreach($array as $key=>$value) {
    // do stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，这解决了从 repeater 字段获取值的第一个问题。

```
 <?php

                    $fields = get_field(‘flexible_content’);

//                    $keys = array_keys($repeater);

//                    $repeater2 = array_values($repeater); // does not work, nests another layer of array

                    if($fields){

                    foreach($fields as $field_type => $field){

                            foreach($field as $row) {
                                echo($row);
                            }

                        }
                    }
                    ?> 
```

Enter fullscreen mode Exit fullscreen mode

接下来是灵活内容领域的问题。奇怪的是，灵活内容的第一个字段是它的名称。我不想显示它的名字(现在)，所以我想知道如何去掉第一个元素…像往常一样，我转储变量，看看我面对的内容:

```
array(1) { [0]=> array(3) { ["acf_fc_layout"]=> string(8) "row_name" ["test_1"]=> string(14) "
Test 1

" ["test_2"]=> string(428) "
Test 2 
```

Enter fullscreen mode Exit fullscreen mode

我碰到了这个:[如何从一个关联 PHP 数组中获取第一项？-堆栈溢出](https://stackoverflow.com/questions/1617157/how-to-get-the-first-item-from-an-associative-php-array)，它说使用 reset()来检索数组的第一个元素。所以我访问了对象本身，然后像这样使用 reset():

```
 <?php

                    $flexibleContent = get_field('flexible_content_2');

                    if($flexibleContent){

                        // A bit of a hack to not display the first field, which is field name
                        $firstValue = reset($flexibleContent[0]);

                        foreach($flexibleContent as $field_type => $field){

                            foreach($field as $row) {

                                if($row === $firstValue) {
                                    // do nothing

                                } else echo $row;
                            }
                        }
                    }
                    ?> 
```

Enter fullscreen mode Exit fullscreen mode

我仍然有点不清楚这些 php 数组实际上是如何工作的，所以如果有人能解释一下，那就太好了！但是我很高兴我学到了一些有用的东西，比如 var_dump()、reset()和遍历数组:)