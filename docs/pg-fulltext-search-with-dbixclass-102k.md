# 使用 DBIx::Class 的 Pg 全文搜索

> 原文：<https://dev.to/jplindstrom/pg-fulltext-search-with-dbixclass-102k>

最近我为一个内部服务写了一个管理界面。它基本上是由服务处理的事情的列表。

对于故障排除问题，能够通过一些包含参数和处理结果的文本列来过滤列表似乎非常有用。使用 *Postgres 全文搜索*被证明是一件小事，所以我想在这里分享一下。

## Postgres 中的全文搜索

在 Postgres 中，使用文本搜索数据类型和操作符来执行全文搜索。这里有一个[好的介绍](https://www.compose.com/articles/mastering-postgresql-tools-full-text-search-and-phrase-search/)。

作为一个例子，假设我们有一个带有一个文本列的`book`表，该文本列包含该书的自由形式的描述。

让我们在 *Charles Stross* 的*洗衣档*系列中找书吧，因为最新的一本《迷宫索引》刚刚出版。耶！

```
select id, title, blurb from book where
    to_tsvector('english', blurb)
    @@
    to_tsquery(
        'english',
        '(scifi | sci-fi) & paranormal & bureacracy
    ); 
```

简单地说，这里发生的事情是将`blurb`列解析成一个`tsvector`，其中包含我们可以匹配的英语单词。匹配是使用`to_tsquery`函数完成的，该函数包含一个特定格式的搜索查询。从这个例子中，你大概可以理解整体的语法。

就是这样！对任何列进行全文搜索。做得好 Pg！

关于这个还有很多，我只知道最开始的事情，所以去阅读 Postgres 手册了解更多信息。

## 提高性能

对于上面的查询，`to_tsvector('english', blurb)`部分将为每一行运行这个函数，所以这会有点慢。

在这种特殊情况下，数据量最初并没有那么大。即使有几千行，这也感觉很快，几十毫秒后返回查询。

但是随着数据量的增加，这种情况不会持续很久，所以让我们在这个查询的`blurb`列上创建一个表达式索引。

```
CREATE INDEX CONCURRENTLY book_blurb_gin
    ON book USING GIN ( to_tsvector('english', blurb) ); 
```

注意`to_tsvector('english', blurb)`如何与我们在上面的查询中所做的完全匹配。

## 搜索可用性

作为一个好的起点，特别是对于非技术用户来说，不要用`tsquery`格式使事情过于复杂可能是个好主意。

只要说他们输入的所有单词都必须与文本匹配:通过用`' & '`连接用户输入中的单词来自己构造`tsquery`字符串。

如果你允许你的用户输入自由格式的搜索字符串，你必须向他们解释清楚。对于*技术倾向的用户*，我想出的最简短的解释是:

> 词与词之间用`&`、`|`、`!`，用`()`分组表达。
> 匹配‘单引号短语’。

例子对于传达这样的细节是非常有力的。你应该挑选一些与你的领域相关的有用的搜索词*，也许类似于这个:* 

`(scifi | sci-fi) & horror & 'paranormal bureacracy'`

但是一定要在示例中包含所有可用的特性，因为人们不会阅读文本，而这个示例可能是他们忙碌的目光所关注的唯一事物。\ *(ツ)* /

### 简单易用的查询格式

在 Postgres 11 及以后的版本中，有更多用户友好的方式来获取用户输入。进一步阅读关于 [plainto_tsquery 和 websearch_to_tsquery](https://www.postgresql.org/docs/11/static/textsearch-controls.html#TEXTSEARCH-PARSING-QUERIES) 的内容。

## tsquery 错误

如果用户输入无效的`tsquery`语法，Postgres 将在执行查询时抛出异常。你需要准备好处理这种情况，并把错误用更好的方式表达出来。您可以将错误消息与此进行匹配:

```
syntax error in tsquery 
```

## 链式 DBIx::Class 搜索方法

Perl 模块 [DBIx::Class](https://metacpan.org/pod/DBIx::Class) 是 SQL 级 DBI 模块之上的一个 ORM。它在很多方面都很优秀。

DBIC 的一个更好的特性是[链式结果集](https://metacpan.org/pod/distribution/DBIx-Class/lib/DBIx/Class/Manual/Features.pod#ResultSet-Chaining)。这意味着您可以拥有一个应用了 *where 子句*的 resultset 对象，然后传递该对象并通过应用进一步的条件、连接更多的表等来修改查询。

```
my $book_rs = $schema->resultset("Book");
my $title_book_rs = $book_rs->search({
    title => "...",
});
my $title_and_author_book_rs = $title_book_rs->search({
    author => "...",
});

# The query is only executed when we ask for the rows
my @book_rows = $title_and_author_book_rs->all; 
```

当然，我们可以在`ResultSet::Book`类的方法中定义这些对`search`的调用之一。

```
sub search_title_rs($self, $title) {
    return $self->search({ author => $title });
} 
```

现在，这是一个简单的例子，但是像这样将查询的细节封装到一个搜索方法中，使得实际的应用程序代码非常易读，即使当我们需要构造非常复杂的查询时。

像这样提取小的查询片段也是一种很好的方法，可以确保与每个结果集相关的细节都放在实际负责它的类中，而不是作为较大查询的一部分分散在代码库中。

我们将使用这种技术来搜索全文列。

## search_tsquery_rs

在 SQL 中使用`to_tsvector`和`to_tsquery`语法有点热情，在 DBIC 甚至更复杂。但是正如我们刚刚看到的，它可以很好地封装在一个`search_tsquery_rs`方法中，该方法可以链接到结果集上。

把它放在你的表的`ResultSet::Book`类中，或者更好，放在一个你所有的`ResultSet`类都从
继承的公共基类中

```
 =head2 search_tsquery_rs($column, $tsquery) : $rs

Search the $column as a Postgres tsquery fulltext search.

Use &, |, ! between words, and () to group expressions.

Invalid tsqueries will throw an exception that matches
m/syntax error in tsquery/.

=cut

sub search_tsquery_rs($self, $column, $tsquery) {
    $self->search({
        -and => [
            \[
                "to_tsvector('english', $column) @@ to_tsquery('english', ?)",
                $tsquery,
            ],
        ],
    });
} 
```

请注意在 DBIC 实现这一目标所需的特性:

*   文字 SQL 被插入到查询中，但是(这很酷)我们仍然可以为此使用占位符
*   这感觉更像是一次黑客攻击，但是为了获得语法上正确的 *where 子句*，我们需要将它与 AND 操作符结合起来

现在我们可以在我们的应用程序代码中无缝地使用这个方法:

```
my $book_rs = $schema->resultset("Book");
my $book_count = $book_rs
    ->search({ author => "..." })
    ->search_tsquery_rs("blurb", "horror & scifi")
    ->count; 
```

## 在 DBIC 处理 tsquery 错误

虽然您可以传递 resultset 对象并将它们链接在一起，但是实际的 SQL 查询不会执行，直到您请求返回一些数据，例如通过调用`->all`或`->count`。

这意味着我们不能将错误消息处理封装在`search_tsquery_rs`方法本身中，因为它不一定在抛出异常的地点和时间附近。

取而代之的是，你必须将*的执行地点*包装在`eval`中(或者更好:使用 [Try::Tiny](https://metacpan.org/pod/Try::Tiny) ，并在那里识别与`tsquery`相关的异常。

## 总结

就是这样！

我希望这是对 Postgres 全文搜索以及如何在 DBIx::Class 中灵活使用它的有益介绍。

为了了解 Postgres 是如何很好地支持这一点:总而言之，写这篇博文可能比研究和实现我的简单需求要花更长的时间。这也是一种绝对的快乐，使用软件时，设计良好的部分似乎只是一起工作。

## 奖励:Postgres 正则表达式搜索方法

有时，全文搜索是多余的。因此，当我们这样做的时候，除了使用 Postgres 的正则表达式之外，下面是如何进行 LIKE 查询。

```
=head2 search_regex_rs($column, $regex) : $rs

Search the $column as a Postgres SIMILAR TO regex match.

Invalid regexes will throw an exception that matches
m/invalid regular expression/.

=cut

sub search_regex_rs($self, $column, $regex) {
    return $self->search({
        $column => \[ "SIMILAR TO ?" => $regex ],
    });
} 
```