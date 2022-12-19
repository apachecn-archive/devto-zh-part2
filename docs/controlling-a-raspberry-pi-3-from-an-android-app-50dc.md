# 从 android 应用程序控制树莓 pi 3

> 原文：<https://dev.to/nikolaof/controlling-a-raspberry-pi-3-from-an-android-app-50dc>

你好。我计划创建一个 android 应用程序，它将向/从 raspberry pi 3 模块发送命令和接收一些结果。例如，一个这样的命令可以是启动风扇 3 分钟。在这三分钟内(风扇运转时), raspberry pi 将发送从应用程序端的温度传感器接收到的温度数据，其中将提供这些值的图表。

我的想法是:
应用程序将扫描可用的设备。
它将连接到树莓 pi。
一个 python 蓝牙服务器将在 raspberry pi 网站上运行，执行 android 应用程序发送给它的命令，并将一些数据发送回应用程序。

到目前为止，我在互联网上搜索发现了 Evothings 框架，但我想看看你对如何轻松构建这样的应用程序的建议。

谢谢