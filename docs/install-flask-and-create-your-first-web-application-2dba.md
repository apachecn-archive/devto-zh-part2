# 如何安装 Flask 安装 Flask 并创建您的第一个 web 应用程序

> 原文：<https://dev.to/sahilrajput/install-flask-and-create-your-first-web-application-2dba>

有大量的 [Python web 框架](https://wiki.python.org/moin/WebFrameworks)和 [Flask](http://flask.pocoo.org/) 是其中之一，但它不是一个全栈 web 框架。
它是“一个基于 [Werkzeug](http://werkzeug.pocoo.org/) 、 [Jinja 2](http://jinja.pocoo.org/docs/2.10/) 和良好意图的 Python 微框架。”包括一个内置的开发服务器、单元测试支持，并且完全支持 Unicode，具有 [RESTful](https://en.wikipedia.org/wiki/Representational_state_transfer) 请求调度和 [WSGI 兼容性](https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface)。

## 安装

要安装烧瓶，您可以点击[这里](http://flask.pocoo.org/docs/1.0/installation/)或者按照以下步骤操作:

### 第一步:安装虚拟环境

如果你正在使用 Python3，那么你不必安装虚拟环境，因为它已经自带了 [venv](https://docs.python.org/3/library/venv.html#module-venv) 模块来创建虚拟环境。
如果您使用的是 Python 2，venv 模块不可用。反而，`install virtualenv`。

**在 Linux 上，virtualenv 是由你的包管理器提供:**

```
//Debian, Ubuntu
$ sudo apt-get install python-virtualenv
//CentOS, Fedora
$ sudo yum install python-virtualenv
//Arch
$ sudo pacman -S python-virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

如果你用的是 Mac OS X 或者 Windows，下载 [get-pip.py](https://bootstrap.pypa.io/get-pip.py) ，然后:

```
$ sudo python2 Downloads/get-pip.py
$ sudo python2 -m pip install virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

在 Windows 上，作为管理员:

```
\Python27\python.exe Downloads\get-pip.py
\Python27\python.exe -m pip install virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:创建环境

在
中创建一个项目文件夹和一个`venv`文件夹

```
mkdir myproject
cd myproject
python3 -m venv venv 
```

Enter fullscreen mode Exit fullscreen mode

[![pic1](img/1f63142780a6ac60e9161711e57f8cdd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--inlN6npT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/48008954-b673d080-e140-11e8-87b3-2e668098469e.png)

在 Windows 上:

```
py -3 -m venv venv 
```

Enter fullscreen mode Exit fullscreen mode

如果因为使用的是旧版本的 Python 而需要安装 virtualenv，请使用下面的命令:

```
virtualenv venv 
```

Enter fullscreen mode Exit fullscreen mode

在 Windows 上:

```
\Python27\Scripts\virtualenv.exe venv 
```

Enter fullscreen mode Exit fullscreen mode

**激活环境**

在你开始你的项目之前，激活相应的环境:

```
. venv/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

[![pic2](img/a93dfbad19900b9ad4c7c72eacec0438.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zphtMgCe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/48008953-b5db3a00-e140-11e8-93d7-d3ca238d75e2.png)

在 Windows 上:

```
venv\Scripts\activate 
```

Enter fullscreen mode Exit fullscreen mode

您的 shell 提示符将会改变，以显示激活环境的名称。

### 第三步:安装烧瓶

在激活的环境中，使用以下命令安装 Flask:

```
$ pip install Flask 
```

Enter fullscreen mode Exit fullscreen mode

[![screenshot 2018-11-05 at 9 22 44 pm](img/2dc8659cca6d3af6234aafd80d825863.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_9TnB5yW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/48009094-f76be500-e140-11e8-882e-00917743fe68.png)

**烧瓶现已安装:**查看[快速入门](http://flask.pocoo.org/docs/1.0/quickstart/)或转到[文档](http://flask.pocoo.org/docs/1.0/)。

## 创建一个应用

所以，让我们构建一个最简单的`hello world`应用程序。
**遵循以下步骤:**

1.  因为，您已经在`myproject`文件夹中。创建一个“hello.py”文件，并编写下面的代码。

    *   导入 Flask 类。这个类的一个实例将是我们的 WSGI 应用程序。

        ```
        from flask import Flask 
        ```

    *   接下来，我们创建这个类的一个实例。第一个参数是应用程序的模块或包的名称。如果您使用单个模块(如本例所示)，您应该使用 __ name __ 因为根据它是作为应用程序启动还是作为模块导入，名称会有所不同( **main** 与实际导入名称)。这是必要的，以便 Flask 知道在哪里寻找模板、静态文件等等。

        ```
        app = Flask(__ name __) 
        ```

    *   然后我们使用 route() decorator 告诉 Flask 哪个 URL 应该触发我们的函数。该函数被赋予一个名称，这个名称也用于为该特定函数生成 URL，并返回我们希望在用户浏览器中显示的消息。

        ```
        @app.route('/')
        def hello_world():
            return 'Hello, World!' 
        ```

    `Make sure to not call your application flask.py because this would conflict with Flask itself.`

    要运行应用程序，您可以使用 flask 命令或 python 的`-m`开关来运行 Flask。在此之前，您需要通过导出`FLASK_APP`环境变量来告诉您的终端要使用的应用程序:

    ```
    $ export FLASK_APP=hello.py
    $ flask run
    //Or you can use
    $ export FLASK_APP=hello.py
    $ python -m flask run 
    ```

    [![screenshot 2018-11-05 at 11 07 58 pm](img/1913874e6273ce7fe95800e26ec34770.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KdhIOKTg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/48015704-c515b400-e14f-11e8-8690-9949dd356239.png)

    *   进入 [http://127.0.0.1:5000/](http://127.0.0.1:5000/) 查看你的项目运行情况。![screenshot 2018-11-05 at 11 10 19 pm](img/766d2b67a4a9ca67dc751880ebbcfe2e.png)