# macOS 怪癖:蓝牙设备电池电量低

> 原文：<https://dev.to/aromig/macos-quirks-bluetooth-device-battery-low-dkm>

出于某种原因，macOS 为蓝牙电池设置了*非常*低的阈值(比如神奇鼠标&键盘)。信不信由你，设定为 2%。对我来说，这有点太接近死亡了，让我知道它的电池快没电了，我需要更多的警告——特别是考虑到苹果 Magic Mouse 2 的闪电端口在鼠标的底部。我在工作的时候不能给它充电。

我四处寻找，找不到一个设置，甚至一个 plist 条目来改变这一点。幸运的是，macOS 中的许多东西都可以通过命令行工具访问。我们可以用来访问这些信息的主要工具是[`ioreg`](http://www.manpagez.com/man/8/ioreg/)([I/O 注册表](https://developer.apple.com/library/archive/documentation/DeviceDrivers/Conceptual/IOKitFundamentals/TheRegistry/TheRegistry.html))。

我写了几个 bash 脚本来解决这个问题，一个用于鼠标，另一个用于键盘。

```
#!/bin/sh
PATH=/usr/local/bin:/usr/local/sbin:~/bin:/usr/bin:/bin:/usr/sbin:/sbin

# Script that checks the battery level of a connected mouse and displays a notification if it is below a threshold (default 15%). Can set threshold as a parameter. ex: ./check_mouse_battery.sh 10

MOUSENAME=`ioreg -c AppleDeviceManagementHIDEventService -r -l | grep -i mouse | cut -d = -f2 | cut -d \" -f2`

MOUSEBATTERY=`ioreg -c AppleDeviceManagementHIDEventService -r -l | grep -i mouse -A 20  | grep BatteryPercent | cut -d = -f2 | cut -d ' ' -f2`

COMPARE=${1:-15}

if [ -z "$MOUSEBATTERY" ]; then echo 'No Mouse Found.'
    exit 0
fi

if (( MOUSEBATTERY < COMPARE )); then osascript -e "display notification \"Battery Level at ${MOUSEBATTERY}%.\" with title \"Battery Low\" subtitle \"${MOUSENAME}\""
fi 
```

Enter fullscreen mode Exit fullscreen mode

脚本本质上:

1.  抓取设备名称`MOUSENAME` &其电池百分比`MOUSEBATTERY`。
2.  将`COMPARE`的值设置为第一个脚本参数或默认值(15)。
3.  如果没有找到设备，它将退出脚本。
4.  如果`MOUSEBATTERY`小于`COMPARE`，在 macOS 中显示当前电池电量的通知。

[![Low Battery Notification](img/f4fd50fd901ac8f83bee5cdcb2137164.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a-h9NuQi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/aromig/scripts/master/check_battery/check_mouse_battery_notification.png)

然后，我必须设置一种定期自动运行脚本的方法。为此我使用了 [`launchctl`](http://www.manpagez.com/man/1/launchctl/) ，设置说明包含在下面这个实用程序的库中。我将它的 plist 配置为每 2 小时运行一次脚本。

为了保持脚本简单，我将鼠标和键盘的检查分离到不同的文件中。摆脱这种烦恼本身就是一种有趣的练习，我可能真的会期待下一次收到警告，让我尽快给我的设备充电。

[Github 库](https://github.com/aromig/scripts/tree/master/check_battery)