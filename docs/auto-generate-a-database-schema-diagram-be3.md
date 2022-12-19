# 自动生成数据库模式图

> 原文：<https://dev.to/mitchjacksontech/auto-generate-a-database-schema-diagram-be3>

# 为您的 Catalyst 应用生成实时数据库模式图

在构建 web 应用程序时，我总是创建一个 URL 端点来动态生成数据库模式图。这是一个永不过时的快速参考项目。

[![schema diagram](../Images/996a529777785b961cdbe17cae2dd450.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kL3Hzgfq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jum5zbmuj6c6612nfjza.png)

这种方法可以用于任何 Perl web 框架。提供的示例是使用 DBIx::Class ORM 的 Catalyst 框架。这个例子提供了网址[http://mywebsite.com/dbdiagram](http://mywebsite.com/dbdiagram)

```
 =head2 dbdiagram

Render an image representing the current data model

=cut

use SQL::Translator;
sub dbdiagram :Local :Args(0) {
    my ($self, $c) = @_;

    my $translator = SQL::Translator->new(
        parser => 'SQL::Translator::Parser::DBIx::Class',
        data   => $c->model('Ezn')->schema,
        producer => 'Diagram',
        producer_args => {
            output_type => 'png',
            title       => "My clever database diagram",
            add_color   => 1,
            font_size   => 'large',
        },
    ) or die SQL::Translator->error;

    $c->res->content_type('image/png');
    $c->res->body($translator->translate);
} 
```

*不要忘记*限制授权用户！

另请参见:

*   [催化剂框架](http://www.catalystframework.org/)
*   [DBIx::Class](https://metacpan.org/pod/DBIx::Class)
*   [SQL::Translator::Parser::DBIx::Class](https://metacpan.org/pod/SQL::Translator::Parser::DBIx::Class)
*   [SQL::Translator::Producer::Diagram](https://metacpan.org/pod/SQL::Translator::Producer::Diagram)