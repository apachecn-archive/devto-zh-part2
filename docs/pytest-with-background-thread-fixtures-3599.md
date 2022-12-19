# 带有后台螺纹夹具的 Pytest

> 原文：<https://dev.to/ghost/pytest-with-background-thread-fixtures-3599>

[https://medium . com/media/f 288938 e 0d 12 EC 115 f 481 ACB 0 f 5d 9d 45/href](https://medium.com/media/f288938e0d12ec115f481acb0f5d9d45/href)

最近我不得不测试我的一些 Python🐍🐍🐍需要外部依赖并使用 TCP 套接字进行通信的代码🔌。您可以将这种依赖关系视为本质上的数据库，因为它存储信息。然而，在测试我的 Python 代码时，我不能总是依赖 TCP 服务器来发送消息。

所以最后我用 Python 创建了一个简化的嘲讽版本。这样，我可以对我的代码运行自动化测试，而不需要安装/依赖已经存在的服务器。实际上，我最终用 Python 创建了一个 TCP 服务器，它将接收一些输入，并以我所期望的方式响应真正的 TCP 服务器。

我使用 Pytest 编写测试有几个原因，我更喜欢它而不是 unittest，因为它需要更少的样板代码。它也有许多有用的功能，如夹具。然而，在为我的 API 创建测试时，我意识到我必须首先启动假的 TCP 服务器。但是因为它总是在听👂为了交通🚦它会阻止我其余的测试运行。因此，在这个例子中，我将向您展示如何创建一个 Pytest fixture，并在后台线程(TCP 服务器)上运行它，同时仍然正常运行您的测试。

### tcp_server.py

<figure>[![](../Images/486c760b60abf1721ec23a576a2a2f32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0gyJsRYr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A2H1Kvh6RfkFtMUh-fXJuEg.png)

<figcaption>TCP _ server . py</figcaption>

</figure>

这是一个非常基本的 TCP 服务器，它接收一些输入，但总是回复“已接收”。此外，我创建了与上下文管理器一起使用的类，以便可以关闭套接字，因此有了 *__enter__()* 和 *__exit__()* dunder (magic)方法。该 TCP 服务器将始终在端口 9500 上侦听本地主机，客户端也必须连接到该端口，以便向该 TCP 服务器发送消息。

感兴趣的主要函数是 *listen_for_traffic()* ，它在监听流量的同时永远循环。如果它接收到任何数据，它总是回复“已接收”，当然，对于一个真实的服务器，我们可能想做一些更有趣的事情，但为了使这个例子简单，这就是这个服务器将做的所有事情。

### [T1】test _ example . py](#testexamplepy)

<figure>[![](../Images/28d44c0bec51580078de8ca0e678358a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4H7UXVTy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APfeX25OzVkHyYoa4Ffv00A.png)

<figcaption>test _ example . py</figcaption>

T6】</figure>

当您运行 Pytest 命令时，它将自动对所有以“test_”开头的模块和方法运行测试。我已经创建了一个 fixture(使用 fixture decorator)，fixture 允许在 Pytest 模块中重用代码。fixture 通常用于连接数据库，fixture 是在任何测试之前运行的，因此我们也可以使用它们来设置我们的代码。

所以看一下 *dummy_tcp_server()* 函数，首先我们使用上下文管理器(with)来设置我们的 tcp 服务器。

```
with example_server as tcp_server: 
```

这调用了 *__enter__()* dunder 方法，该方法启动了我们的 TCP 服务器，因此它现在正在侦听端口 9500 上的任何流量。

```
thread = threading.Thread(target=example_server.listen_for_traffic)
thread.daemon = True
thread.start() 
```

这是在另一个(后台)线程上实际运行我们的 TCP 服务器的一段代码。因此，在第一行中，我们告诉线程要调用什么函数， *listen_for_traffic* (没有括号)有一个无限的 while 循环，只等待侦听到达端口 9500 的流量。我们将线程设置为守护进程，这样当我们关闭主 Python 程序时，线程将自动杀死自己，这意味着我们不必自己管理它。

```
yield example_server 
```

收益率非常类似于 return 语句。我选择包含的主要原因是，如果测试由于某种原因引发了异常，yield 仍然允许上下文管理器退出。如果我们在产出后有任何拆卸代码，即使有异常，这些代码在所有测试完成后仍然会运行。yields(和 return 语句)也允许单独的测试访问 TCPServer 类中的函数。例如，如果您有一个函数(在 TCPServer 类中)存储发送到服务器的每条消息。如果您将 fixture 作为参数传递给任何测试，那么您可以访问这个函数。

```
def test_example():
 HOST = '127.0.0.1'
 PORT = 9500

 data = ""
 with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
 s.connect((HOST, PORT))
 s.sendall(b'Hello World')
 data = s.recv(1024)

 assert data.decode() == "Received" 
```

我们的代码部分是实际的测试。它发送“Hello World”到 TCP 服务器，记住如果你是以字符串的形式发送的(没有 *b''* )，那么你必须首先编码这个字符串(*“Hello World”)。encode()* )。然后，我们捕获来自服务器的响应，并对其进行解码(因为它是二进制的，所以会将其转换为字符串)，并断言或检查数据是否是我们所期望的“已接收”的字符串。

### 附录

*   [示例源代码](https://github.com/hmajid2301/medium/tree/master/6.%20Pyest%20with%20Background%20Threads)
*   [Pytest](https://docs.pytest.org/en/latest/)
*   [用碳制作的代码图像](https://carbon.now.sh)