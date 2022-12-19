# 拦截退出 docker 容器的 HTTP 流量

> 原文：<https://dev.to/jandedobbeleer/intercept-http-traffic-exiting-a-docker-container-3g68>

想象一下下面这个问题。您正在 docker 容器中运行一个应用程序，您觉得有必要检查应用程序正在执行的 http 请求。我自己也是一名应用程序开发背景的人，使用像 [mitmproxy](https://mitmproxy.org/) 这样的代理工具来查看实际的请求，而不是一直试图找出问题所在，这是一个巨大的好处。或者，使用代理来改变请求，并轻松地测试用例或重现错误报告。总而言之，在工作中使用正确的工具可以大大缩短交付时间。否则**我们还会到处添加打印语句**只是为了调试(我知道你正在这么做)。

在本例中，我有一个运行在 Debian Jessie 容器中的 Python Django web 应用程序，我需要看到向后端 REST API 发出的请求流。安装 mitmproxy 是一件轻而易举的事情，因为它是一个跨平台的工具，您可以遵循您的平台的安装指南。我自己在 Windows(Ubuntu)上用 WSL 运行它。在做其他事情之前，先开始 mitmproxy，舒服一下。捕获流量的默认端口是 8080，使用`-p <portnumber>`选项更改它，以防发生冲突。

```
$ mitmproxy 
```

Enter fullscreen mode Exit fullscreen mode

Docker 提供现成的代理支持，这很方便。由于我们已经在主机上运行了 mitmproxy，我们希望将所有离开容器的流量都路由到那个主机。为此，我创建了一个新的 docker-compose.override 文件，它包含所有设置和(几乎所有)逻辑。

```
version: '2'
services:
  mv-web:
    environment:
      - HTTP_PROXY=mitmproxy:8080
      - HTTPS_PROXY=mitmproxy:8080
    extra_hosts:
      - "mitmproxy:10.125.2.187"
    command: sh -c 'python3 certificate.py && python3 manage.py runserver 0.0.0.0:8020' 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我只添加了与说明解决方案相关的设置。您可以看到两个环境变量，`HTTP_PROXY`和`HTTPS_PROXY`。这些命令告诉容器将所有现有流量路由到 mitmproxy，后者是作为额外主机添加的。ip 地址`10.125.2.187`是我的主机，把它改成你的。

现在，你可能会注意到这里还有一个叫做`python3 certificate.py`的小项目，它在我们启动 Django 应用程序之前运行。如果只使用代理设置，http 请求可以输入 mitmproxy，但是 https 请求不会显示出来。原因是 mitmproxy 使用它自己的证书来解密流过的流量。结果，由于操作系统不信任该证书，请求将被阻止(这是应该的)。

最初，我试图将证书添加到容器的操作系统可信证书存储中，但这并没有按计划进行。我可以在容器内运行一个`curl`命令到 https 端点，然后**像老板**一样流过 mitmproxy，但是当我启动 Django 应用程序时，https 请求不断被中止。我花了一段时间才弄明白发生了什么。我们的 Django 应用程序使用[请求](http://docs.python-requests.org/en/master/)库来执行 http 调用。这个又使用[certificate](https://github.com/certifi/python-certifi)来创建它的可信证书存储，它没有 mitmproxy 的根证书的副本，这导致所有 https 调用都被静音并且不退出容器。

一旦你知道了，解决方法就很简单了。将名为`certificate.py`的脚本与 mitmproxy 的根证书(位于`~/.mitmproxy/mitmproxy-ca-cert.pem`)一起添加到您的容器中(通过卷映射，或者作为构建步骤)，并编辑`docker-compose.override.yml`文件以在启动 Django 应用程序之前执行`certificate.py`。

将 mitmproxy 的根证书复制到 Django 应用的根文件夹:

```
$ cp ~/.mitmproxy/mitmproxy-ca-cert.pem <project root> 
```

Enter fullscreen mode Exit fullscreen mode

在同一个项目根目录下创建一个名为`certificate.py`的新文件:

```
import certifi

caroot = certifi.where()
with open('mitmproxy-ca-cert.pem', 'rb') as mitm:
    cert = mitm.read()
with open(caroot, 'ab') as out:
    out.write(cert)
print('w00t, added mitmproxy root cert to certifi!') 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您使用 compose 打开 docker 容器时，您将看到所有流量都流经 mitmproxy！调试愉快！

## 小加法

### 多个 docker-compose 文件

当使用一个`docker-compose.override.yml`文件并且已经指定了另一个非标准的`docker-compose`文件时，确保使用两次`-f`来加载两个(或更多)设置文件。所以，假设你的设置文件叫做`docker-compose.standalone.yml`，你已经按照本教程添加了所有文件，打开你的容器的命令变成:

```
docker-compose -f .\docker-compose.standalone.yml -f .\docker-compose.override.yml up 
```

Enter fullscreen mode Exit fullscreen mode

我们的覆盖文件应该是最后一个，这样就不会有设置被之前文件中的设置覆盖，wins 中的最后一个文件。

### 检查防火墙设置

当我在 Windows 上运行时，我仍然需要打开端口`8080`来允许容器连接到 mitmproxy。在继续之前，请仔细检查您正在使用的系统，确保这不是导致头痛的原因😑。

### 但是我没有使用 Python 或者 Requests

如果您想这样做，但没有使用请求的 Python 应用程序，您可以尝试将证书添加到容器的操作系统中，就像我之前提到的那样。出于某种原因，我还必须在每次容器启动时设置它，但这并不太麻烦。代替`certificate.py`，创建一个名为`certificate.sh`的文件，其内容如下:

```
mkdir /usr/local/share/ca-certificates/extra
cp mitmproxy-ca-cert.pem /usr/local/share/ca-certificates/extra/root.cert.crt
update-ca-certificates 
```

Enter fullscreen mode Exit fullscreen mode

然后，更改`docker-compose.override.yml`文件来执行这个文件，而不是`certificate.py` :

```
command: sh -c './certificate.sh && <whatever the command>' 
```

Enter fullscreen mode Exit fullscreen mode

这个例子假设一个基于 Debian 的容器，确保根据你的容器的操作系统修改`certificate.sh`来添加根证书到正确的位置。

玩得开心！