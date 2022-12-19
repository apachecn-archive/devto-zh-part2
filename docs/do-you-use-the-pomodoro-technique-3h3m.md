# 你使用番茄工作法吗？

> 原文：<https://dev.to/shostarsson/do-you-use-the-pomodoro-technique-3h3m>

大家好，

我想知道你们中的一些人是否时不时地或者频繁地使用番茄工作法来提高你的注意力和效率。

* * *

这里有一个很好的视频，解释了什么是你不知道什么是番茄工作法。
[https://www.youtube.com/embed/mNBmG24djoY](https://www.youtube.com/embed/mNBmG24djoY)T2】

我之前也在用这个技巧，停了一段时间。但是当我想再次使用它时，我很快就找到了一个好的应用程序，可以在 Linux 上做简单的番茄红素。
不幸的是，这里的每一个应用程序要么充满了设置和功能，要么需要 Java 或类似的东西，我不想安装在我的电脑上。

然后我想到了一个好主意。-)

> "*为什么我不能使用终端在终端中设置一个定时器，在定时器结束时发送通知？*

和往常一样，使用终端非常简单。

* * *

#### 1。步骤 1 -开始 25 分钟的工作时间

```
sleep 1500 && notify-send "Your pomodoro session just ended. Have a well deserved 5 minutes break" 
```

Enter fullscreen mode Exit fullscreen mode

1500 秒是 25 分钟。然后，终端将显示一个通知，提示“*您的番茄大战刚刚结束。拥有受之无愧的 5 分钟休息时间*

#### 2。第二步——开始你应得的、没有负罪感的休息

```
sleep 300 && notify-send "Back to work"  # a short, 5-minute break 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 3。第三步-改进

但是，你也可以同时启动定时器和中断定时器。

```
sleep 1500 && notify-send "Your pomodoro session just ended. Have a well deserved 5 minutes break"; sleep 300 && notify-send "Back to work" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 4。第四步——别名(我是个懒人；-) )

将此添加到您的别名文件

```
pomodorostrt='sleep 1500 && notify-send "Your pomodoro session just ended. Have a well deserved 5 minutes break"'
pomodorobrk='sleep 300 && notify-send "Back to work"'
pomodoro='sleep 1500 && notify-send "Your pomodoro session just ended. Have a well deserved 5 minutes break"; sleep 300 && notify-send "Back to work"' 
```

Enter fullscreen mode Exit fullscreen mode

> 您可以将别名更改为较短的名称。但是因为我有自动补全功能，所以我更喜欢把它弄清楚。:-)

然后你只需输入`pomodorostrt`或`pomodorosbrk`即可开始**工作时段**或**休息时段**或`pomodoro`即可启动一个完整的**番茄时段**。

* * *

#### 又一个改进

你可以使用 zenity ( `man zenity`做进一步的解释)来有一个更有粘性的通知。

```
sleep 1500 && zenity --warning --text="25 minutes passed" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

那么你认为你可以使用终端来提高你的生产力吗？
你使用番茄工作法吗？你认为它在某些方面提高了你的工作效率或注意力吗？
你还在使用其他时间管理技巧吗？