# 教程:Mojolicious::Plugin::OpenAPI:参数

> 原文：<https://dev.to/jonasbn/tutorial-mojoliciouspluginopenapi-parameters-4ejj>

# mojo licious::Plugin::open API:参数教程

这是我的教程的后续，演示了一个 Hello World 示例的基本实现。

本教程将对该主题进行扩展，并将触及参数的使用。

## 申请

该应用程序实现并演示了两种参数处理方法:

1.  基于参数的查询，如:`/api/user?id=alice`
2.  基于 URL 参数，如:`/api/user/bob`

首先让我们生成我们的应用程序:

```
$ mojo generate app Parameters 
```

Enter fullscreen mode Exit fullscreen mode

跳转到我们新生成的应用程序目录

```
$ cd parameters 
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

打开`openapi.conf`并插入下面的*片段* :

```
{  "swagger":  "2.0",  "info":  {  "version":  "1.0",  "title":  "Demo of API with parameters"  },  "basePath":  "/api",  "paths":  {  "/users":  {  "get":  {  "operationId":  "getUsers",  "x-mojo-name":  "get_users",  "x-mojo-to":  "user#list",  "summary":  "Lists users",  "responses":  {  "200":  {  "description":  "Users response",  "schema":  {  "type":  "object",  "properties":  {  "users":  {  "type":  "array",  "items":  {  "type":  "object"  }  }  }  }  }  }  }  },  "/user/#id":  {  "get":  {  "operationId":  "getUserByUrl",  "x-mojo-name":  "get_user_by_url",  "x-mojo-to":  "user#get_by_url",  "summary":  "User response",  "responses":  {  "200":  {  "description":  "User response",  "schema":  {  "type":  "object",  "properties":  {  "user":  {  "type":  "object"  }  }  }  },  "default":  {  "description":  "Unexpected error",  "schema":  {}  }  }  }  },  "/user":  {  "get":  {  "operationId":  "getUserByParameter",  "x-mojo-name":  "get_user_by_parameter",  "x-mojo-to":  "user#get_by_parameter",  "summary":  "User response",  "parameters":  [  {"in":  "query",  "name":  "id",  "type":  "string"}  ],  "responses":  {  "200":  {  "description":  "User response",  "schema":  {  "type":  "object",  "properties":  {  "user":  {  "type":  "object"  }  }  }  },  "default":  {  "description":  "Unexpected error",  "schema":  {}  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们检查一下我们的定义。我们将不会涉及这样的基础知识，请参考 Hello World 教程，相反，我们将专注于与参数相关的方面。

我们引入了 3 个端点来模拟一个更完整的 RESTful API。

我们定义了以下路径:

*   `/api/users`，返回用户数组。这基本上是 Hello World 示例的一个变体，它只是返回一个对象，而这个端点返回一个对象数组。

*   `/api/user?id=«id»`，如果可以找到用户对象，则返回用户对象，这是基于查询参数的。mojo licence::Plugin::open API 根据我们的`openapi.json`中的定义验证这个参数

*   `/api/user/«id»`，返回一个用户对象，如果可以找到的话，这是基于 URL 的，就像一个适当的 RESTful 接口，Mojolicious::Plugin::OpenAPI 不**不**验证，但是 URL 是基于我们`openapi.json`中的定义进行验证的

在`lib/Parameters/Controller/`中创建新文件:`User.pm`

首先我们添加处理`/api/users` :
的方法

```
package Parameters::Controller::User;
use Mojo::Base 'Mojolicious::Controller';

my %users = (
    'bob'  => { name => 'Bob', email => 'bob@eksempel.dk' },
    'alice'=> { name => 'Alice', email => 'alice@eksempel.dk' },
);

sub list {

    # Do not continue on invalid input and render a default 400
    # error document.
    my $c = shift->openapi->valid_input or return;

    my @user_list = ();

    foreach my $user_id (keys %users) {
        my $user = $users{$user_id};
        $user->{userid} = $user_id;
        push @user_list, $user;
    }

    # $output will be validated by the OpenAPI spec before rendered
    my $output = { users => \@user_list };
    $c->render(openapi => $output);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在应用程序中模拟一个模型，所以数据只是放在一个名为`%users`的内部数据结构中，这当然可以是任何类型的模型、数据库、文件系统或其他服务。

现在启动应用程序:

```
$ morbo script/parameters 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们调用 API，注意你不需要`jq`，你可以使用`curl`或`httpie`，所以这只是为了坚持使用已经可用的工具，`jq`是个例外。

```
$ mojo get http://localhost:3000/api/users | jq 
```

Enter fullscreen mode Exit fullscreen mode

答:我们得到了一个完整的用户列表，目前包括 Bob 和 Alice:

```
{  "users":  [  {  "email":  "bob@eksempel.dk",  "name":  "Bob",  "userid":  "bob"  },  {  "email":  "alice@eksempel.dk",  "name":  "Alice",  "userid":  "alice"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加通过查询参数处理单个用户对象的方法:

```
sub get_by_parameter {

    # Do not continue on invalid input and render a default 400
    # error document.
    my $c = shift->openapi->valid_input or return;

    # $c->openapi->valid_input copies valid data to validation object,
    # and the normal Mojolicious api works as well.
    my $input = $c->validation->output;

    my $id = $input->{'id'};

    my $user = $users{$id};

    if ($user) {
        $user->{userid} = $id;
        # $output will be validated by the OpenAPI spec before rendered
        my $output = { user => $user };
        $c->render(openapi => $output);
    } else {
        $c->respond_to(
            any => { status => 404, json => { message => 'Not found' }}
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们调用 API:

```
$ mojo get http://localhost:3000/api/user?id=alice | jq 
```

Enter fullscreen mode Exit fullscreen mode

爱丽丝的结果是:

```
{  "user":  {  "email":  "alice@eksempel.dk",  "name":  "Alice",  "userid":  "alice"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们添加通过 URL 处理单个用户对象的方法:

```
sub get_by_url {

    # Do not continue on invalid input and render a default 400
    # error document.
    my $c = shift->openapi->valid_input or return;

    my $id = $c->stash('id');

    my $user = $users{$id};

    if ($user) {
        $user->{userid} = $id;
        # $output will be validated by the OpenAPI spec before rendered
        my $output = { user => $user };
        $c->render(openapi => $output);
    } else {
        $c->respond_to(
            any => { status => 404, json => { message => 'Not found' }}
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用 URL:
再次调用 API

```
$ mojo get http://localhost:3000/api/user/bob | jq 
```

Enter fullscreen mode Exit fullscreen mode

我们得到鲍勃的结果:

```
{  "user":  {  "email":  "bob@eksempel.dk",  "name":  "Bob",  "userid":  "bob"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

耶！我们的**mojo licious****open API**实现工作正常，我们甚至可以支持不同的 URL 模式。

既然我们接受参数，我们需要做最后一件事。那就是净化我们的输入。这一部分对于开始使用**mojo licence****open API**集成和**mojo licence::Plugin::open API**来说并不重要。

下一部分不是理解处理参数所必需的，但是如果你想进入验证阶段，这是非常重要的，请继续阅读。

让我们为处理数据的两个端点添加验证。Mojolicious::Plugin::OpenAPI 已经有了一个用于验证的钩子，但是我们需要扩展它。

让我们给我们的`Parameters::Controller::User`添加一个方法。我们的`id`无论我们如何接收它都必须坚持相同的验证，所以我们添加了这个基本的验证方法，它覆盖了我们控制器的现有验证。

```
sub _validate_id {
    my ($c, $id) = @_;

    my $validator = $c->validation->validator;
    my $validation = $validator->validation;
    $validation->input({id => $id});
    $validation->required('id')->like(qr/^[A-Z]/i);
    $c->validation->validator($validation);

    return $c;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们的两个端点获取用户非常相似，唯一的区别是如何接收`id`参数，所以让我们用下面的方法概括一下:

```
sub _proces_request {
    my ($c, $id) = @_;

    $c->_validate_id($id);

    if (not $c->validation->validator->has_error) {

        my $input = $c->validation->validator->output;

        my $id = $input->{'id'};
        my $user = $users{$id};

        if ($user) {
            $user->{userid} = $id;
            # $output will be validated by the OpenAPI spec before rendered
            my $output = { user => $user };
            $c->render(openapi => $output);
        } else {
            $c->respond_to(
                any => { status => 404, json => { message => 'Not found' }}
            );
        }
    } else {
        $c->respond_to(
            any => { status => 400, json => { message => 'Bad request' }}
        );
    }

    return $c;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们让这个方法调用我们的验证方法，所以我们需要做的就是让两个端点提取`id`参数并调用`_proces_request`方法:

第一:`get_by_parameter` :

```
sub get_by_parameter {

    # Do not continue on invalid input and render a default 400
    # error document.
    my $c = shift->openapi->valid_input or return;

    # $c->openapi->valid_input copies valid data to validation object,
    # and the normal Mojolicious api works as well.

    $c->_proces_request($c->param('id'));
} 
```

Enter fullscreen mode Exit fullscreen mode

其次:`get_by_parameter` :

```
sub get_by_url {

    # Do not continue on invalid input and render a default 400
    # error document.
    my $c = shift->openapi->valid_input or return;

    # $c->openapi->valid_input copies valid data to validation object,
    # and the normal Mojolicious api works as well.

    $c->_proces_request($c->stash('id'));
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该准备好了。完整的控制器组件应该如下所示:

```
package Parameters::Controller::User;
use Mojo::Base 'Mojolicious::Controller';

my %users = (
    'bob'  => { name => 'Bob', email => 'bob@eksempel.dk' },
    'alice'=> { name => 'Alice', email => 'alice@eksempel.dk' },
);

sub list {

    # Do not continue on invalid input and render a default 400
    # error document.
    my $c = shift->openapi->valid_input or return;

    my @user_list = ();

    foreach my $user_id (keys %users) {
        my $user = $users{$user_id};
        $user->{userid} = $user_id;
        push @user_list, $user;
    }

    # $output will be validated by the OpenAPI spec before rendered
    my $output = { users => \@user_list };
    $c->render(openapi => $output);
}

sub get_by_parameter {

    # Do not continue on invalid input and render a default 400
    # error document.
    my $c = shift->openapi->valid_input or return;

    # $c->openapi->valid_input copies valid data to validation object,
    # and the normal Mojolicious api works as well.

    $c->_proces_request($c->param('id'));
}

sub get_by_url {

    # Do not continue on invalid input and render a default 400
    # error document.
    my $c = shift->openapi->valid_input or return;

    # $c->openapi->valid_input copies valid data to validation object,
    # and the normal Mojolicious api works as well.

    $c->_proces_request($c->stash('id'));
}

sub _proces_request {
    my ($c, $id) = @_;

    $c->_validate_id($id);

    if (not $c->validation->validator->has_error) {

        my $input = $c->validation->validator->output;

        my $id = $input->{'id'};
        my $user = $users{$id};

        if ($user) {
            $user->{userid} = $id;
            # $output will be validated by the OpenAPI spec before rendered
            my $output = { user => $user };
            $c->render(openapi => $output);
        } else {
            $c->respond_to(
                any => { status => 404, json => { message => 'Not found' }}
            );
        }
    } else {
        $c->respond_to(
            any => { status => 400, json => { message => 'Bad request' }}
        );
    }

    return $c;
}

sub _validate_id {
    my ($c, $id) = @_;

    my $validator = $c->validation->validator;
    my $validation = $validator->validation;
    $validation->input({id => $id});
    $validation->required('id')->like(qr/^[A-Z]/i);
    $c->validation->validator($validation);

    return $c;
}

1; 
```

Enter fullscreen mode Exit fullscreen mode

为了检验我们的新验证，尝试不同的变化。正如我们实现的那样，我们只允许用户 id 的字母，因此我们认为该请求是一个*错误请求*

```
$ mojo get --verbose http://localhost:3000/api/user/123 | jq
GET /api/user/123 HTTP/1.1
Host: localhost:3000
Accept-Encoding: gzip User-Agent: Mojolicious (Perl)
Content-Length: 0

HTTP/1.1 400 Bad Request
Date: Fri, 27 Jul 2018 08:25:40 GMT
Content-Type: application/json;charset=UTF-8
Server: Mojolicious (Perl)
Content-Length: 25

{
  "message": "Bad request"
} 
```

Enter fullscreen mode Exit fullscreen mode

而另一个端点也有一个*坏请求* :

```
$ mojo get --verbose http://localhost:3000/api/user?id=123 | jq
GET /api/user?id=123 HTTP/1.1
Host: localhost:3000
User-Agent: Mojolicious (Perl)
Accept-Encoding: gzip Content-Length: 0

HTTP/1.1 400 Bad Request
Date: Fri, 27 Jul 2018 08:27:24 GMT
Server: Mojolicious (Perl)
Content-Type: application/json;charset=UTF-8
Content-Length: 25

{
  "message": "Bad request"
} 
```

Enter fullscreen mode Exit fullscreen mode

这部分到此结束。体验 mojo licious::Plugin::open API 的乐趣。

## 参考文献

*   [metacpan:mojolis::plugin::open API](https://metacpan.org/pod/Mojolicious::Plugin::OpenAPI)
*   [metacpan:mojolis::plugin::open API tutorial](https://metacpan.org/pod/Mojolicious::Plugin::OpenAPI::Guides::Tutorial)中
*   [OpenAPI 网站](https://www.openapis.org/)
*   [教程的 GitHub 库](https://github.com/jonasbn/perl-mojolicious-plugin-openapi-tutorial-parameters)
*   [mojo licence . org:mojo licence::Validator](https://mojolicious.org/perldoc/Mojolicious/Validator)
*   [mojo licence . org:mojo licence::Validator::Validation](https://mojolicious.org/perldoc/Mojolicious/Validator/Validation)