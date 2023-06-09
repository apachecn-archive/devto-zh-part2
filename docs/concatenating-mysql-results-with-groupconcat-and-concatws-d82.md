# 用 Group_concat 和 Concat_ws 连接 MySQL 结果

> 原文：<https://dev.to/cassidycodes/concatenating-mysql-results-with-groupconcat-and-concatws-d82>

最近，我需要构建一个查询，将数据库中的数据转换成我们发布到 ElasticSearch 的格式。我将在这里使用博客帖子的例子，因为它们很容易被每个人理解。想象一下，每篇文章可以有许多标签，您希望 ElasticSearch 上的一个字段包含标签 id，另一个字段包含标签名称和描述。

数据库可能是这样的:

*帖子*

```
id | title
---|------------------------------
1 | Blogging about dogs is fun!
2 | Look at these cute dogs
3 | Wow how about these cute dogs 
```

Enter fullscreen mode Exit fullscreen mode

*post_tags*

```
post_id | tag_id
--------|-------
1 | 1
1 | 2
2 | 1
2 | 3
3 | 1 
```

Enter fullscreen mode Exit fullscreen mode

*标签*

```
id | name | description
---|------|-----------------
1 | dogs | Posts about dogs
2 | cute | Cute things
3 | omg | OMG CUTE 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们想要的结果:

```
id | title | tag_ids | tag_names_descriptions
---|-----------------------------|---------|------------------------------------------
1 | Blogging about dogs is fun! | 1, 2 | dogs, cute, Posts about dogs, Cute things 
```

Enter fullscreen mode Exit fullscreen mode

## 开始查询

让我们从检索帖子和标签的连接表开始。在这个例子中，我们将把这篇文章限制在一篇，以保持我们的表格较小。

```
SELECT post.id,
       post.title,
       tag.id AS tag_id,
       tag.name AS tag_name,
       tag.description AS tag_description
FROM posts post
       INNER JOIN posts_tags pt
               ON pt.post_id = post.id
       INNER JOIN tags tag
               ON tag.id = pt.tag_id
LIMIT 1;

id | title | tag_id | tag_name | tag_description
---|-----------------------------|--------|----------|-----------------
1 | Blogging about dogs is fun! | 1 | dogs | Posts about dogs
2 | Blogging about dogs is fun! | 2 | cute | Cute things 
```

Enter fullscreen mode Exit fullscreen mode

好的，很好，我们有一个包含文章及其标签的表格，但是我们有重复的行！我们可以使用`GROUP BY`按照文章 id 对这些行进行分组，但是我们将丢失第二行中的标签数据，并得到如下所示的结果:

```
id | title | tag_id | tag_name | tag_description
---|-----------------------------|--------|----------|-----------------
1 | Blogging about dogs is fun! | 1 | dogs | Posts about dogs 
```

Enter fullscreen mode Exit fullscreen mode

## Group_concat

当我们使用`GROUP BY` :
时，我们可以使用`Group_concat`函数连接多行数据

```
SELECT post.id,
       post.title,
       Group_concat(tag.id) AS tag_ids,
       tag.name AS tag_name,
       tag.description AS tag_description
FROM posts post
       INNER JOIN posts_tags pt
               ON pt.post_id = post.id
       INNER JOIN tags tag
               ON tag.id = pt.tag_id
GROUP BY post.id
LIMIT 1;

id | title | tag_ids | tag_name | tag_description
---|-----------------------------|---------|----------|-----------------
1 | Blogging about dogs is fun! | 1, 2 | dogs | Posts about dogs 
```

Enter fullscreen mode Exit fullscreen mode

看看 Group_concat 的 MySQL 文档，你可以用它做一些很酷的事情，比如确保值是唯一的，对值进行排序，以及选择自定义分隔符。

但是我们的 tag_name 和 tag_description 呢？这里，我们需要将两个独立的列连接成一列！

## 串联 _ws

我们知道`Group_concat`给了我们一个字符串，所以如果我们在 MySQL 文档中的字符串函数下查找，我们会找到 [`Concat_ws`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_concat-ws) 。`Concat_ws`让我们用分隔符将两个或多个字符串连接起来。默认情况下，分隔符是逗号，但是为了易读性，我们也想要一个空格，所以让我们使用`SEPARATOR`选项。以下是我们的步骤:

连接标签名称:

```
Group_concat(tag.name SEPARATOR ", ") 
```

Enter fullscreen mode Exit fullscreen mode

连接标签描述:

```
Group_concat(tag.description SEPARATOR ", ") 
```

Enter fullscreen mode Exit fullscreen mode

把这两个连接起来！

```
Concat_ws(
  ", "
  Group_concat(tag.name SEPARATOR ", "),
  Group_concat(tag.description SEPARATOR ", ")
) 
```

Enter fullscreen mode Exit fullscreen mode

### 把所有的东西放在一起

```
SELECT post.id,
       post.title,
       Group_concat(tag.id) AS tag_ids,
       Concat_ws(
         ", "
         Group_concat(tag.name SEPARATOR ", "),
         Group_concat(tag.description SEPARATOR ", ")
       ) AS tag_names_descriptions
FROM posts post
       INNER JOIN posts_tags pt
               ON pt.post_id = post.id
       INNER JOIN tags tag
               ON tag.id = pt.tag_id
GROUP BY post.id
LIMIT 1;

id | title | tag_ids | tag_name | tag_descriptions
---|-----------------------------|---------|------------|------------------------------
1 | Blogging about dogs is fun! | 1, 2 | dogs, cute | Posts about dogs, Cute things 
```

Enter fullscreen mode Exit fullscreen mode

## 干得漂亮！

在这篇文章中，我们学习了如何在使用`GROUP BY`时使用`Group_concat`来连接一列中的数据。`Group_concat`让我们保留那些在分组时会消失的数据。

我们还学习了如何通过结合使用`Concat_ws`和`Group_concat`来组合来自多个列的数据。使用这个函数，我们可以将多个列和行连接成一个值。