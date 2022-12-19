# 从 ESP8266 传感器接收数据

> 原文：<https://dev.to/bocajnotnef/receiving-data-from-esp8266-sensors-3n5e>

好的，那么[上次](https://dev.to/bocajnotnef/intro-to-working-with-with-esp8266-2cf-temp-slug-5490487)我讲述了我如何对一些 ESP8266 微控制器进行编程和布线，以收集温度、湿度和土壤湿度数据，并将这些数据发送到网络上的指定地址。

在本文中，我将通过代码在笔记本电脑或 raspberry pi 等设备上运行服务器，以收集传入的数据并将其写入 sqlite3 数据库供以后分析。

这篇文章将涵盖线程、更高级的套接字管理以及与 Python 数据库的接口等主题。

Github 上的[提供了完整的版本化代码。请注意，链接是固定的，不会随着我的改进而更新——为此，请到](https://github.com/bocajnotnef/microcontrollers/blob/14747f88963147118de87178cdc213282cf47450/greenhouse/server.py)[这里](https://github.com/bocajnotnef/microcontrollers)查看温室项目。

我将从这段代码中提取一些片段放到这篇文章中，但是要了解全部内容，你应该看看链接。

* * *

## 快速翻旧账

所以，请记住，我这个项目的目标是在整个温室中部署一组环境传感器来采样数据，并将其发送到一个中心位置，在那里我将做出控制决策(例如，打开加热器、打开通风口、水托盘 A 等)。我们需要我们的服务器架构同时支持多个传感器，以及将每两秒钟的读数整理成一分钟左右的平均值。

# 第一步:从网络上获取数据

现在，让我们假设我们只期待一个客户端传感器与我们对话。我们稍后将处理多个传感器，但之后我们必须处理线程和锁定，这是一个巨大的混乱。

所以现在，一个传感器。

## 暂且不提:服务器套接字编程

当你是一个服务器时，你通常会打开一个套接字/端口来监听传入的客户端——就像一个 web 服务器，在端口 80 上监听新的客户端。棘手的是，您不想在那个套接字上与您的客户端进行主要通信，否则您将无法使用它来侦听更多的传入客户端。

想象一下，一个名为 Bob 的系统向 Google 请求下载某个巨大的文件，Google 在它用来接受新客户端连接的同一个套接字上以该文件作为响应。然后你去找谷歌，但打不通，因为谷歌把文件卸载给鲍勃时“线路忙”，这要花很长时间——在这段时间里，你不能和谷歌通话。

因此，你需要在“公共”端口上的一个套接字来初始化连接，然后另一个套接字来实际处理通信——有点像企业中的一个操作员接听来电，然后将它们路由到不同的线路。算是吧。

## 回网...

因此，我们有一群 ESP8226s 试图打开到一个固定 IP 和端口的连接——我要说的是，任意地，它是 IP 192.168.1.5 和端口 5555。这并不重要，只要服务器和微控制器同意，端口号在 2048 和 60666 之间。(我是在猜测那些数字，你可能想查一下)。

您的服务器需要监听该地址(IP/端口组合)上的传入连接。这相当容易:

```
serversocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
serversocket.bind(("192.168.1.5", 5555))
serversocket.listen() 
```

Enter fullscreen mode Exit fullscreen mode

走过这里:

首先，我们创建一个带有一些属性的套接字。这是一个`AF_INET`系列的套接字，基本上意味着它将与互联网对话(而不是你系统上的另一个进程或一些奇怪的其他网络协议)。这主要决定了套接字如何工作，以及我们要给它的地址类型。

这也是一个`SOCK_STREAM`类型的套接字，这实际上意味着套接字将有一个内部缓冲区，因此我们可以以一个有用的速率将数据“流式传输”给自己。

然后，我们将套接字绑定到一个地址。**注意:**如果您在使用该线路时遇到困难，请确保您拥有的 IP 与您正在使用的系统的 IP 相匹配。如果它们不匹配，这里会出现一个异常(因为您试图绑定到一个实际上并不存在的地址)。

之后，我们将套接字设置为“监听”模式。

跟我到目前为止？

```
(clientsocket, address) = serversocket.accept() 
```

Enter fullscreen mode Exit fullscreen mode

好的——现在，我们说“嘿，socket，监听一个新的连接，当你有一个新的连接时，递给我一个 socket 与这个新的连接对话，让我知道地址是什么。”这是一个*阻塞调用*，这意味着我们的程序现在将停止，直到新的连接进入。显然，当我们坐在这里等待时，这使得进行任何类型的数据处理变得相当复杂——但我们稍后会解决这个问题。

如果你在一个正在运行的微控制器旁边玩这个，你应该看到程序启动，然后在等待传入的套接字连接时挂起-如果你使用的是我的 ESP8266 代码，它会在获取 wifi 连接时稳定闪烁，然后在建立套接字连接时更加愤怒地闪烁，然后在写入套接字时应该是稳定的蓝色，并间歇闪烁。

当然，我们实际上还没有从套接字读取任何数据！让我们来解决这个问题:

```
buffer = ""

while 1:
    data = clientsocket.recv(128)
    datastr = buffer + data.decode('utf-8')
    split = datastr.split("\n")
    buffer = split[-1]
    print(split[:-1]) 
```

Enter fullscreen mode Exit fullscreen mode

所以，这里的济贫院就是`clientsocket.recv(128)` call。这要求套接字提供 128 字节的数据——这也是一个阻塞调用，意味着执行将暂停，直到我们获得 128 字节的数据。当我们得到数据时，我们把它从一堆字节解码成一个字符串，并把它放到一个缓冲区中。

该缓冲区是必要的，因为 128 字节可能不完全是从 ESP8266 发送的单个“记录”的长度-您可能拥有记录 A 的全部和记录 b 的一半。在我们的“协议”中，换行符用作记录之间的边界，这意味着我们有一个简单的方法来判断我们何时拥有整个记录。

因此，我们将接收到的数据添加到缓冲区中(以防我们刚刚得到记录 B 的后半部分)，然后我们在新行上分割缓冲区，以分离记录。我们将拆分后的最后一条记录(可能是部分记录)放回缓冲区。然后我们打印其余的记录(不包括最后一条)。

这样，您应该能够看到来自单个 ESP8266 传感器的传入数据！呜！

现在我们需要扩展到多个连接。

# 第二步:穿线连接

正如我几次提到的，我们使用了一些阻塞执行的调用，使得我们的程序暂停，直到一些外部事件发生。这使得它很难扩展到大量的传感器，所以我们将不得不引入一些线程。

如果你不熟悉的话，线程化是一个程序同时做很多事情的一种方式——多个“执行线程”一个单独的*进程*可以有多个*线程*，这些线程共享相同的内存，这使得它们比多个进程试图相互通信要快。(这也让写作变得容易了很多。)

线程的棘手之处在于，你可能会遇到**竞争条件**，即两个线程试图同时访问同一个资源。这通常会导致奇怪的——并且很难重现——行为，所以在使用线程来防止这种情况发生时，您必须非常非常小心地管理您对共享资源的访问，否则调试它将是一场人间地狱。

我们将要构建的通用架构看起来像是最简单的服务器模型。我们将有一个服务器线程监听端口 5555 并接受传入的连接。这些连接将被转移到不同线程的不同端口上的不同套接字，因此服务器线程可以继续等待。

谢天谢地，Python 提供了一个有用的线程库。有几种方法可以编写线程——你可以创建一个新的`Thread.Thread`对象并传入线程将运行的函数作为参数，或者你可以创建一个从`Thread.Thread`派生的类并覆盖`run`方法。我选择了后者，因为我想有一些保存的状态，这似乎是一个好方法。你可以做任何你喜欢的事情。

## 接受呼入连接

```
class Overseer(threading.Thread):
    def __init__(self, threads):
        super(Overseer, self).__init__()
        print("Starting overseer thread...")

    def run(self):
        global threads_run
        global threads

        serversocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        serversocket.bind(("192.168.1.149", 5555))
        serversocket.listen()

        while threads_run:
            (clientsocket, address) = serversocket.accept()
            l = Listener(clientsocket, address)
            threads.append(l)
            l.start()

        serversocket.close() 
```

Enter fullscreen mode Exit fullscreen mode

这大部分应该看起来很熟悉，真的。这里重要的是`global threads_run`变量和底部带有`l.start()`的`l = Listener(clientsocket, address)`。

Listener 类是我创建的另一个线程类，我们一会儿会讲到。

走过:

init 方法应该和您预期的差不多。有一点需要注意，你**必须**确保调用超类 init，否则当你试图`.start()`线程时会得到一个异常。

while 循环与所有线程共享的变量无关，只在原始主线程中修改。这使得我们可以干净地停止原始线程中的所有线程，并在完成后清理我们的资源。

有了自己线程中的监督者，我们可以不断地阻塞和等待传入的连接，而不会阻止我们的程序做其他事情。这就是线程化的主要思想和主要价值。

现在，这种设计的一个缺陷是，如果我们在等待传入连接时切换`threads_run` *，我们可能会永远等待下去。我一直想写一个中断，但是...以后再说。*

还要注意我们是如何将新的监听器线程附加到一个全局`threads`列表中的——我们将会谈到这一点。

## 监听那些呼入的连接

```
class Listener(threading.Thread):
    def __init__(self, clientsocket: socket.socket, address) -> None:
        super(Listener, self).__init__()
        print("Starting listener thread...")
        self.socket = clientsocket
        self.address = address
        self.buffer = ""

    def run(self):
        global lock
        global shared_list
        global threads_run

        while threads_run:
            data = self.socket.recv(128)
            datastr = self.buffer + data.decode('utf-8')
            self.buffer = ""
            split = datastr.split("\n")
            self.buffer = split[-1]
            split = split[:-1]

            with lock:
                shared_list.extend(split)
                lock.notify()

        self.socket.close() 
```

Enter fullscreen mode Exit fullscreen mode

这是事情开始变得有点复杂的地方，但它仍然非常接近我们之前所拥有的。请记住，监听器线程是在监督线程中构建和启动的，它们在监督线程中获得套接字和地址。

在监听器线程中，当全局`threads_run`为真时，我们不断地阻塞和接收来自套接字的数据。同样，由于这发生在它自己的线程中，这里的阻塞*不会*阻止我们在其他线程中执行。

我们对接收到的数据进行与之前相同的处理...然后我们就有了这个奇怪的事情。

还记得我说过，当你使用线程时，在访问共享资源时必须非常小心吗？这是其中的一个例子。这里，`shared_list`在所有正在运行的线程之间共享，它们在这里写入从网络上获得的数据。为了避免冲突和奇怪的行为，我们使用这个`lock`东西，它是一个`threading.Condition`类。本质上，锁是线程确保一次只有一个线程接触一个对象的一种方式。一个线程必须获取一个锁，一次只有一个线程可以拥有，然后它可以做一些事情，然后它必须为其他线程释放锁，当*他们*试图获取锁时，其他线程就会阻塞。Python 中的`with`语法很好地解决了这个问题；当您使用锁进行`with`时，Python 会处理锁的获取和释放。请注意，您必须手动调用`lock.notify()`，让其他线程知道锁是可用的。还要注意，我只在要与共享数据交互时获取锁，并且在处理完共享数据后立即释放它——在我处理其他线程不共享的字符串时，让它们等我是没有意义的，它们可能正在做一些有用的事情，所以我尽量在尽可能短的时间内保持锁。

这是锁的超高层次视图。锁可能会变得非常复杂，尤其是当您以循环等待结束时(线程 A 正在等待线程 B，但是线程 B 正在等待线程 A)。线程是一个超级复杂的概念，我不打算在这篇文章中深入探讨，但是请放心，如果您对此感兴趣的话，还有很多其他的阅读资料。

*总之，*一旦你有了锁，你就向列表中添加东西，然后释放锁，确保共享访问不会发生奇怪的事情。酷吗？酷毙了。

## 与数据库交互

如果你以前没有使用过数据库，数据库是为数据的简单存储和检索而设计的系统。它们被用于软件的几乎每一个方面，从管理博客的用户到 twitter 的内容到敏感物理区域的访问日志。它们是相当复杂的结构，通常需要一台服务器来运行。

出于我们的目的，我们可以使用 sqlite，这是一个超级轻量级的数据库引擎，它只是将所有数据存储在程序附近的一个文件中。在 Python 中设置和使用它非常容易。

现在，我将解释我在这里使用的 SQL 命令的用途，但与其说这是一个数据库使用教程，不如说它是一个与它们交互的教程。

酷吗？酷毙了。向前！

```
 class DBWriter(threading.Thread):
    def __init__(self):
        super(DBWriter, self).__init__()
        print("Starting DB writer thread...")

    def run(self):
        global threads_run
        global lock
        global shared_list

        connection = sqlite3.connect("greenhouse.db")
        connection.execute("CREATE TABLE if not exists readings "
                           "(source text, "
                           "airtemp real, "
                           "humidity real, "
                           "soil_moisture int, "
                           "timestamp text);")

        while threads_run:
            time.sleep(60)

            to_load_into_database = []
            with lock:
                if len(shared_list) > 0:
                    to_load_into_database = list(shared_list)
                    shared_list.clear()

            print(f"Condensing {len(to_load_into_database)} records...")
            by_source = defaultdict(list)
            record = namedtuple('SensorRecord', ['air_temp', 'air_hum', 'soil_moisture'])
            for line in to_load_into_database:
                line = line.split(';')
                mac = line[3].strip().split(" ")[1]
                by_source[mac].append(
                    record(
                        float(line[1].strip().split(" ")[1]),
                        float(line[2].strip().split(" ")[1]),
                        int(line[0].strip().split(" ")[1]),
                    )
                )

            for key in by_source:
                list_len = float(len(by_source[key]))

                avg_temp = 0
                avg_hum = 0
                avg_soil = 0

                for record in by_source[key]:
                    avg_temp += record.air_temp
                    avg_hum += record.air_hum
                    avg_soil += record.soil_moisture

                avg_temp /= list_len
                avg_hum /= list_len
                avg_soil /= list_len
                timestamp = datetime.datetime.utcnow().strftime("%Y-%m-%d %H:%M:%S")

                values = f" ('{key}', {avg_temp}, {avg_hum}, {avg_soil}, '{timestamp}')"
                print(f"Writing {values} to DB")
                connection.execute(f"INSERT INTO readings VALUES {values};")
            print("Sleeping...")

        print("DB writer thread exiting.")
        connection.commit()
        connection.close() 
```

Enter fullscreen mode Exit fullscreen mode

“啊啊！代码太多了！怎么回事，杰克，进展得这么顺利？!"

挺好的！我保证！真的！

我们会把它分解成碎片。我们来看看设置:

```
class DBWriter(threading.Thread):
    def __init__(self):
        super(DBWriter, self).__init__()
        print("Starting DB writer thread...")

    def run(self):
        global threads_run
        global lock
        global shared_list

        connection = sqlite3.connect("greenhouse.db")
        connection.execute("CREATE TABLE if not exists readings "
                           "(source text, "
                           "airtemp real, "
                           "humidity real, "
                           "soil_moisture int, "
                           "timestamp text);")

        while threads_run:
            time.sleep(60) 
```

Enter fullscreen mode Exit fullscreen mode

所以，`__init__`确实如此...几乎没什么有用的。

在开始运行时，我们全局化一些我们共享的东西，我们打开一个到数据库的连接，给它一个我们希望数据库存在的文件名。

“等等，杰克，”你说。“你为什么不把这种联系提升到`__init__`？不是吗...你是知道的...初始化？”

你会非常正确！我试过了。原来，`sqlite3`对象只能在创建它们的线程中使用，而且，从技术上讲，`__init__`函数与`run`函数是在不同的线程中执行的，因为`__init__`是在创建线程对象时被调用的，而`run`只是在`.start()`线程之后被调用。所以，你必须在线分开后制造物体。

一旦我们连接到数据库，我们确保我们要使用的表存在。如果我们没有`if not exists`短语，当我们试图创建一个已经存在的表时，我们将得到一个 SQL 错误。然后，我们提供数据库中需要的所有列的名称和类型对:

*   source，text:这是发送传感器的 MAC 地址。稍后我将使用它来确定哪个传感器在温室中的哪个位置，这样我就可以实际了解传入的数据，而不必猜测它来自哪里。
*   airtemp，real:温度是连续值，不是整数。尤其是当我们对传感器发送的时间进行平均时，由于温度以摄氏度为单位，因此与真实值相比，整数值非常宽且不精确。
*   真实湿度:参见前面的
*   土壤湿度:这有点奇怪，因为它是一个百分比。从技术上讲，平均值也可以是一个实数，但是我偷懒把它变成了一个 int。告我吧。
*   timestamp，text:我们将为所有数据添加时间戳，否则我们将不知道何时读取数据，但是对于时间戳应该如何存储在数据库中还没有真正的共识。我做文本，因为它是最灵活的格式。

设置好所有这些之后，我们就可以实际进入 while 循环了。这个线程会小睡一会儿，因为我们需要等待将要聚合的数据进入。

现在为肉的聚合:

```
 to_load_into_database = []
            with lock:
                if len(shared_list) > 0:
                    to_load_into_database = list(shared_list)
                    shared_list.clear()

            print(f"Condensing {len(to_load_into_database)} records...")
            by_source = defaultdict(list)
            record = namedtuple('SensorRecord', ['air_temp', 'air_hum', 'soil_moisture'])
            for line in to_load_into_database:
                line = line.split(';')
                mac = line[3].strip().split(" ")[1]
                by_source[mac].append(
                    record(
                        float(line[1].strip().split(" ")[1]),
                        float(line[2].strip().split(" ")[1]),
                        int(line[0].strip().split(" ")[1]),
                    )
                ) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们创建一个空列表，将共享数据复制到其中，然后我们获取锁，将共享列表清空到本地列表中。然后我们释放锁，这样其他线程就可以写入共享的 lit。

完成后，我们开始从字符串中提取数据，并将其加载到 namedtupe 中，这实际上是一个超级高效(并且不可变)的字典。我们还根据发送数据的传感器对数据进行分组。

现在，我们有了按发送传感器分组的数据，我们需要汇总这些数据。

```
 for key in by_source:
                list_len = float(len(by_source[key]))

                avg_temp = 0
                avg_hum = 0
                avg_soil = 0

                for record in by_source[key]:
                    avg_temp += record.air_temp
                    avg_hum += record.air_hum
                    avg_soil += record.soil_moisture

                avg_temp /= list_len
                avg_hum /= list_len
                avg_soil /= list_len
                timestamp = datetime.datetime.utcnow().strftime("%Y-%m-%d %H:%M:%S")

                values = f" ('{key}', {avg_temp}, {avg_hum}, {avg_soil}, '{timestamp}')"
                print(f"Writing {values} to DB")
                connection.execute(f"INSERT INTO readings VALUES {values};")
                connection.commit() 
```

Enter fullscreen mode Exit fullscreen mode

这几乎只是数学。我们汇总发送它们的传感器的所有读数，并建立一个基本上是 ISO 格式的时间戳，但没有毫秒。

完成后，我使用 Python 的优秀格式字符串语法(在 Python 3.6 和更高版本中，耶！).然后，我们执行 insert 语句并将更改提交给数据库。

确保一旦数据库写入器的`run`中的`while`退出，就关闭连接！如果你不这样做，奇怪的事情就会发生。

## 太好了，但是主长什么样？

```
def main():
    global threads_run
    global shared_list
    global threads
    threads = []

    threads_run = True

    threads.append(Overseer(threads_run))
    threads.append(DBWriter())
    threads[0].start()
    threads[1].start()

    while threads_run:

        instr = ""
        while instr != "stop":
            instr = input("enter 'stop' to stop: ")

        threads_run = False

    print("Waiting for threads...")

    for thread in threads:
        thread.join() 
```

Enter fullscreen mode Exit fullscreen mode

我们启动监督程序和 DBWriter，并确保它们被追加到线程列表中。然后我们两个都开始。监督者开始接受连接并生成侦听器，DBWriter 线程开始聚合数据。

然后我们开始一个等待输入的循环，这样用户就可以优雅地停止程序。当他们这样做时，我们等待所有的线程用`thead.join`暂停，然后我们退出我们的程序。

这就对了。那是我服务器的肉！请记住，这绝不是做这件事的“最佳”方式，而且绝对有缺陷。请关注 git repo 以获取更新！