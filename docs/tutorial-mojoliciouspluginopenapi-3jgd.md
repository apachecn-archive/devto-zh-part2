# 教程:mojo licious::Plugin::open API:Hello World

> 原文：<https://dev.to/jonasbn/tutorial-mojoliciouspluginopenapi-3jgd>

# mojo licious::Plugin::open API 教程

我一直想用**大摇大摆**把自己的手*弄脏*。我最近迷上了[mojo licious::Plugin::open API](https://metacpan.org/pod/Mojolicious::Plugin::OpenAPI)，它适合我的*无聊堆栈*，我决定做一个原型。

我看了 Mojolicious::Plugin::OpenAPI 的教程,发现它有点混乱，所以我决定写一个更简单的教程。

本教程要求你已经安装了 [Mojolicious](https://metacpan.org/pod/Mojolicious) ，并推荐 [carton](https://metacpan.org/pod/distribution/Carton/script/carton) 。然而，这些组件的安装超出了本教程的范围。

OpenAPI 来自 **Swagger** ，我已经看过了，已经过去很久了，所以现在是时候看看 **OpenAPI** 了，一个关于如何以标准化格式编写 RESTful APIs 的规范。

这里，让我们从一个基本的`hello world`例子开始，[所有的文件都可以在 GitHub](https://github.com/jonasbn/perl-mojolicious-plugin-openapi-tutorial-hello-world) 上获得。

## 你好世界

首先，我们建立了一个应用程序，是的，我们可以做一个**mojolicous**lite-app，但是我主要使用**mojolicous**app，所以我认为继续坚持这个作为参考是有意义的。

```
$ mojo generate app HelloWorld 
```

Enter fullscreen mode Exit fullscreen mode

跳转到我们新生成的应用程序目录

```
$ cd hello_world 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们在我们的**mojolicous**应用程序中安装启用 **OpenAPI** 所需的插件

使用 **CPAN** 外壳:

```
$ perl -MCPAN -e shell install Mojolicious::Plugin::OpenAPI 
```

Enter fullscreen mode Exit fullscreen mode

使用`cpanm` :

```
$ cpanm Mojolicious::Plugin::OpenAPI 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要安装帮助，请参考[CPAN 安装指南](https://www.cpan.org/modules/INSTALL.html)。

基于 **OpenAPI** 创建一个定义 JSON 文件，以支持基于 **OpenAPI** 规范:
的 Hello World 实现

```
$ touch openapi.conf 
```

Enter fullscreen mode Exit fullscreen mode

这个文件的确切名称是 insignifcant，我只是喜欢使用清晰易懂的文件名，以便于识别。

打开`openapi.conf`并插入下面的*片段* :

```
{  "swagger":  "2.0",  "info":  {  "version":  "1.0",  "title":  "Hello World example"  },  "basePath":  "/api",  "paths":  {  "/hello_world":  {  "get":  {  "operationId":  "helloWorld",  "x-mojo-name":  "hello_world",  "x-mojo-to":  "example#hello_world",  "summary":  "Example app returning hello world",  "responses":  {  "200":  {  "description":  "Returning string 'hello world'",  "schema":  {  "type":  "object",  "properties":  {  "greeting":  {  "type":  "string"  }  }  }  },  "default":  {  "description":  "Unexpected error",  "schema":  {}  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们检查一下我们的定义。

*   `basePath`:定义我们的 URL 的根，所以我们将能够在`/api`访问我们的应用程序，关于使用这个部分的版本 API 的建议确实存在，但是对于我们的示例应用程序，这超出了范围。

*   `paths`:这里我们定义了第一个 API 路径，因此我们的 Hello World 应用程序可以在:`/api/hello_world`访问

*   `operationId`:是一个操作标识符，对于 OpenAPI 部分很重要，而下面的两个定义是同一个操作标识符到**mojolicous**应用程序的映射

*   `x-mojo-name`:这是用于在**mojolicous**应用程序中标识我们的操作的名称

*   `x-mojo-to`:这是我们在**mojo licous**应用程序中操作所使用的路线规范，稍后会详细介绍

*   这里我们定义了我们想要处理的类型，现在我们满足于`200`。响应定义概述了我们的响应，这可以归结为一个`string`而不是一个`object`，带有属性，但是这个例子会变得*太简单*，在我看来我们主要处理基本类型上的对象，所以这个扩展的例子是一个更好的参考。

下一步是在应用程序中启用[MetaCPAN:mojo licience::Plugin::open API](https://metacpan.org/pod/Mojolicious::Plugin::OpenAPI)插件

打开文件:`lib/HelloWorld.pm`并添加以下代码片段:

```
$self->plugin("OpenAPI" => {url => $self->home->rel_file("openapi.json")}); 
```

Enter fullscreen mode Exit fullscreen mode

注意指向我们之前创建的文件的指针:`openapi.json`。

完整的文件应该如下所示:

```
package HelloWorld;
use Mojo::Base 'Mojolicious';

# This method will run once at server start
sub startup {
  my $self = shift;

  $self->plugin('OpenAPI' => {url => $self->home->rel_file('openapi.json')});

  # Load configuration from hash returned by "my_app.conf"
  my $config = $self->plugin('Config');

  # Documentation browser under "/perldoc"
  $self->plugin('PODRenderer') if $config->{perldoc};

  # Router
  my $r = $self->routes;

  # Normal route to controller
  $r->get('/')->to('example#welcome');
}

1; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们添加 acual 操作，打开文件:`lib/HelloWorld/Controller/Example.pm`并添加下面的代码片段:

```
sub hello_world {
    my $c = shift->openapi->valid_input or return;

    my $output = { greeting => 'Hello World' };
    $c->render(openapi => $output);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，这映射到我们的 API 定义中的定义:`openapi.conf`

```
"x-mojo-to":  "example#hello_world", 
```

Enter fullscreen mode Exit fullscreen mode

完整的文件应该如下所示:

```
package HelloWorld::Controller::Example;
use Mojo::Base 'Mojolicious::Controller';

# This action will render a template
sub welcome {
  my $self = shift;

  # Render template "example/welcome.html.ep" with message
  $self->render(msg => 'Welcome to the Mojolicious real-time web framework!');
}

sub hello_world {
    my $c = shift->openapi->valid_input or return;

    my $output = { greeting => 'Hello World' };
    $c->render(openapi => $output);
}

1; 
```

Enter fullscreen mode Exit fullscreen mode

我决定在一个搭建的应用程序中实现本教程，您可以创建自己的控制器，但是以这种方式更改现有的控制器演示了我们新添加的 OpenAPI API 端点如何能够与现有的和附加的端点和谐共存。

现在启动应用程序

```
$ morbo script/hello_world 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们调用 API

```
$ http http://localhost:3000/api/hello_world 
```

Enter fullscreen mode Exit fullscreen mode

我们现在应该得到结果

```
HTTP/1.1  200  OK  Content-Length:  26  Content-Type:  application/json;charset=UTF-8  Date:  Thu,  26  Jul  2018  08:20:59  GMT  Server:  Mojolicious  (Perl)  {  "greeting":  "Hello World"  } 
```

Enter fullscreen mode Exit fullscreen mode

耶！我们的第一个**mojo licious****open API**实现成功了！

除了操作之外，还可以通过调用下面的 URL 来获取规范:`/api`

```
$ http http://localhost:3000/api/ 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，我们现有的操作和部分应用程序仍然可以正常工作，试着调用 URL: `/`

```
$ http http://localhost:3000/ 
```

Enter fullscreen mode Exit fullscreen mode

目前就是这样，祝你在尝试**mojo licious****open API**集成和 **OpenAPI** 时好运。感谢 Jan Henning Thorsen([@ JH Thorsen](https://twitter.com/jhthorsen))实现了 Mojolicious::Plugin::OpenAPI。

## 参考文献:

*   [metacpan:mojolis::plugin::open API](https://metacpan.org/pod/Mojolicious::Plugin::OpenAPI)
*   [metacpan:mojolis::plugin::open API tutorial](https://metacpan.org/pod/Mojolicious::Plugin::OpenAPI::Guides::Tutorial)中
*   [OpenAPI 网站](https://www.openapis.org/)
*   [教程的 GitHub 库](https://github.com/jonasbn/perl-mojolicious-plugin-openapi-tutorial-hello-world)