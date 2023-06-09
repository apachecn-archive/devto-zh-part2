# RS232 零调制解调器引脚排列和接线

> 原文：<https://dev.to/helgayork/rs232-null-modem-pinout-and-wiring-2o4l>

什么是空调制解调器？

空调制解调器用于通过 [RS232 协议](https://en.wikipedia.org/wiki/RS-232)连接两台没有调制解调器的计算机。最初开发的 RS232 协议允许电传打字机通过它们的调制解调器相互连接，非常适合用于空调制解调器。

使用 RS232 协议的[不对称连接](https://searchnetworking.techtarget.com/definition/asymmetric-communications)需要在连接的一端有一个数据源或消费者，在另一端有一个调制解调器。RS232 零调制解调器没有标准的布线方案，你会发现在实践中使用了许多不同的变体。零调制解调器中的接收线和传输线交叉连接，以实现双向数据传输。

空调制解调器最常见的用途是使老式笔记本电脑和计算机能够交换数据。在没有网卡或 USB 端口的情况下，空调制解调器连接和空调制解调器仿真器是可用于这些传统机器之间的数据传输的唯一方法。

什么是零调制解调器电缆？

很明显，您会使用空调制解调器电缆[创建空调制解调器连接](https://www.eltima.com/create-virtual-serial-port.html)。

零调制解调器电缆与其他电缆的区别是什么？具体来说，零调制解调器电缆由三条线构成。其中两条线路负责传输数据，第三条线路作为信号地。

**空调制解调器引脚和接线**

根据通过零调制解调器电缆传输时使用的软件，您可能需要连接来提供认证握手。

以下是最常见的零调制解调器电缆方案。

[![Null modem cable](img/165690f658799f1914af3279ed086ef0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dyn8mgwA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.virtualserialportdriver.cimg/upload/eltima/articles/rs232/1.jpg)

这是微软推荐的 RS232 电缆布线配置，包括完全认证握手。

[![RS232 cable wiring includes full authentication handshaking](img/cbf1651657fb1e8afb71543fdb73f533.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EIaqe31A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.virtualserialportdriver.cimg/upload/eltima/articles/rs232/2.jpg)

这是 RS232 零调制解调器连接的行业标准。它使用微软开发的由七根电线制成的电缆。

**虚拟零调制解调器仿真器**

零调制解调器电缆不提供高速数据传输。理论上，零调制解调器连接的最大吞吐速度受到将其连接到计算机的串行端口速度的限制。这个最大值 115 kb/s 永远达不到，因为传输速度受到用于连接的电缆长度的影响。

使用 **[空调制解调器仿真器](https://www.eltima.com/best-virtual-null-modems/)** 的专用软件可以获得更高的传输速度。这个应用程序在两台计算机之间创建一个虚拟的零调制解调器连接。它使用虚拟零调制解调器电缆连接到机器的串行端口。

[![Virtual Null Modem Emulator](img/0dbdb64f05e49856c0f61f8eade1acd7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HLFbCCUQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.virtualserialportdriver.cimg/upload/eltima/articles/rs232/3.png)

虚拟零调制解调器软件允许您[创建虚拟 COM 端口对](https://www.eltima.com/create-virtual-serial-port.html)。该软件支持串行端口之间的双向数据传输。传输到该对中的一个 COM 端口的数据也同时被该对中的另一个端口接收。

与使用物理零调制解调器电缆相比，使用虚拟零调制解调器软件传输数据是一种更有效的通信方法。

高质量零调制解调器仿真器，如 Eltima 公司虚拟 Com 端口驱动程序，允许您仿真波特率，并实现全手动流量控制。该应用程序支持所有标准线路信号，如 RTS/CTS、DRT/DSR、振铃和错误。虚拟零调制解调器仿真器还使您能够模拟串行连接中的断线。