# 每个软件工程新手都应该知道的常见缩写—第 1 部分

> 原文：<https://dev.to/mukulbichkar/common-acronyms-every-newbie-in-software-engineering-should-knowpart-1-343l>

自从我开始编程以来，甚至今天每当我阅读文档时，我都会遇到许多在软件工程中很常见的缩写词或术语。了解完整的表单不是强制性的，但是了解它的含义和作用是很重要的。所以，我整理了一份常用缩略语的清单，可能会对像我这样的人有所帮助。

1.  FTP —文件传输协议
    它是一种用于在网络上的客户端和服务器之间传输文件的网络协议。FTP 建立在客户端-服务器模型上，使用两种不同的连接，即控制和数据，在客户端和服务器之间进行通信。控制通道仅用于控制通信，而数据通道处理实际的文件传输。使用 FTP，客户端可以上传、删除、更新、下载、重命名、移动或复制服务器上的文件。FTP 以两种不同的模式工作，即主动模式和被动模式，这两种模式决定了如何建立连接。在主动模式下，客户端与服务器建立控制通道，而数据通道由服务器建立。而在被动模式下，控制和数据连接都由客户端建立。因此，被动模式在客户端受防火墙保护且服务器无法与客户端建立连接的情况下很有帮助。

2.  TCP/IP —传输控制协议/互联网协议
    所以，TCP/IP 顾名思义由 TCP 和 IP 两部分组成。IP 是互联网的地址系统，它的唯一职责是将信息包从源传送到目的地。它不能处理这个包的错误检查或排序，这正是 TCP 要解决的问题。这有点像通过邮件发送一个由不同部分组成的拼图，不同的部分可以通过不同的路线到达，并且没有顺序。IP 确保这些片段到达目的地。正是 TCP 负责将这些部分组装在一起，并且在缺少任何部分时进行通信。TCP 是在发送最后一个数据段后，发送第一个数据段之前与发送方保持连接的一方。

3.  所见即所得
    WYSIWYG 是一个编辑器、程序或网站，允许用户在处理代码或文档时看到最终结果或成果。一个简单且最常用的 WYSIWYG 编辑器的例子是 Microsoft Word。另一个 WYSIWYG HTML 编辑器的例子是 Adobe 的 Dreamweaver。

4.  REPL——Read Eval Print Loop
    一个简单的交互式计算机编程环境，接受用户输入，对其进行评估，然后将结果打印/返回给用户。循环每次迭代中的 3 个步骤包括:
    Read:读取用户输入，解析它，并将其存储到内存中的数据结构中。
    Eval: Evaluate 获取步骤 1 中创建的数据结构，并对表达式求值。
    Print:打印第二步得到的表达式求值结果，并回传给用户查看。
    所有软件开发人员日常使用的一个最常见的例子是 CLI，也称为 Windows 生态系统的命令行界面或 Powershell。许多支持 REPL 的语言有 Python、Node.js、JavaScript 等。仅举几个例子。

[![](img/acedbb9140e0f6aaf1fc04f97d1494c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F0VIEHTd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/87your320gsavr7ov457.PNG)

1.  API —应用程序编程接口
    应用程序编程接口是一组定义明确的程序，有助于两个系统、组件等之间的通信。它指定了组件应该如何交互。API 就像一张餐馆菜单卡，上面有一道菜的清单和一道菜的描述。你点了一道菜，并不担心餐馆厨房如何制作这道菜，你只关心美味的食物。任何开发人员都在大多数现代 web 或移动应用程序中使用多个三方 API。一些常见的例子是用于用户登录的谷歌地图 API、Youtube API 或脸书 API。

2.  ORM —对象关系映射
    它是一种使用面向对象编程语言在不兼容系统之间转换数据的技术。ORM 工具提供了将面向对象的领域模型映射到关系数据库的框架。它们提供了一种处理对象关系阻抗不匹配问题的方法。ORM 减少了代码行数，并提供了缓存的优势，从而减少了内存负载。常见的 ORM 工具和框架有 Hibernate、Entity Framework、SQLAlchemy 等。

[![](img/8bd9783839b4c73d6a14d33057eef1e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XUX7B1fo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k6l6dd1on7uft2q1vfd7.png)

1.  LOB、CLOB 和 BLOB —大型对象、字符大型对象、二进制大型对象
    大型对象是一种用于存储大型对象的数据类型。字符大对象是数据库管理系统(DBMS)中字符数据的集合。CLOB 用于存储基于 Unicode 字符的数据，例如任何字符集的大型文档。二进制大对象是作为单个实体存储的二进制数据的集合。斑点通常是图像、音频或其他多媒体对象。

2.  ER 图—实体关系图
    在了解实体关系之前，先了解实体很重要。一个实体是一个可定义的东西，比如一个人、一个物体、一个概念或者一个事件——可以存储一些关于它的数据。它们是名词，例如，顾客、汽车、学生等。实体关系图说明了系统中实体之间的关系。ER 图通常用于数据库设计。

3.  KISS——保持简单，愚蠢的
    [![](img/50d2e63b62e6db3937d6ed37b25f9207.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I5r53ITR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rwjixvz4wwuc8ookmaiv.png) 
    KISS 原则是关于保持代码简单、清晰、可读。编写除你之外的其他人容易理解和破译的代码。一个基本的例子是编写不超过 40 到 50 行代码的小方法。每种方法都应该有单一的功能，不应该解决多个用例。在代码中遵循 KISS 原则的一个好处是可以帮助其他开发人员理解你的代码，并且可以节省几个小时来理解代码和实现修改。

4.  SDK —软件开发工具包
    软件开发工具包是一个可下载的软件包，其中包含在平台上构建所需的工具。它为一个平台进行了大量定制，包含库、API、集成开发环境(IDE)和其他有助于调试、构建、运行和测试应用程序的工具。在现实世界中，假设你需要小麦面包，你不能去农场种植、种植和收获小麦，然后做面包。太费时间，不值得努力。所以就软件工程而言，这就是 SDK 的用武之地。Bread SDK 中的库将包含预先编写的函数，如 plantWheat()、growWheat()、harvestWheat()和 makeBread()，开发人员可以调用这些函数来简化这个过程。