# 如何制作一个可以屏蔽电话的原生 Android 应用程序

> 原文：<https://dev.to/nikola/how-to-make-a-native-android-app-that-can-block-phone-calls--4e15>

[![](img/60fdcb47698f79fefd2e31173af8e5c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kY_h_RIl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4o0fiv2smerze5jsbpj2.jpg)

*最初发表于[我的博客](http://www.nikola-breznjak.com/blog/android/make-native-android-app-can-block-phone-calls/)。*

## TL；速度三角形定位法(dead reckoning)

在这篇文章中，我将一步一步地向你展示如何制作一个原生的 Android 应用程序，可以阻止某些号码给你打电话。

源代码是 Github 上的[。](https://github.com/Hitman666/AndroidCallBlockingTestDemo)

我希望我将在这里向您展示的分步指南能够帮助您，让您不必再做额外的研究。

当然，由于我在日常工作中不是一名原生的 Android 开发人员，我这样做也是因为它可以很好地提醒我，当我需要再次处理类似的情况时。向其他人喊出#jackOfAllTrades💪

还有，鉴于上面的陈述；我将感谢任何关于此代码的反馈。🙏

## [T1】！TL；速度三角形定位法(dead reckoning)](#tldr)

我花了很多时间浏览 StackOverflow 和博客帖子来寻找这个解决方案。所有这些都很有帮助:

*   如何在 Android 设备上检测来电？
*   [安卓棉花糖 6.0 无法接听来电](https://stackoverflow.com/questions/42339534/cant-answer-incoming-call-in-android-marshmallow-6-0)
*   [即使我声明了 Android 权限，它也不起作用](https://stackoverflow.com/questions/32635704/android-permission-doesnt-work-even-if-i-have-declared-it)
*   [以编程方式结束来电](https://stackoverflow.com/questions/20965702/end-incoming-call-programmatically)
*   [电话响了吗](http://gabesechansoftware.com/is-the-phone-ringing/)

但遗憾的是，它们都不是简单的初学者教程。所以，在做了大量额外的研究后，我让它工作了，这里是我解释如何工作的最佳尝试。

> 顺便说一下:在测试这个的时候，[如何在 Android Studio](http://www.nikola-breznjak.com/blog/android/simulate-incoming-call-sms-emulator-android-studio/) 的模拟器中模拟来电或短信的发现也非常有帮助。

### 开始一个新项目

在 Android Studio 中，转到`File->New->New Project`，给它一个名称和位置，然后点击`Next`:

[![](img/70944063a1942bef9f6963d56a64c1f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tvJ7XHm9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4iyIDSJ.png)

保留最低 API 级别的默认选项:

[![](img/ee0ab53e6dbe29353262cea5c890a741.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6C65eBYe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/wBim1w2.png)

选择一个`Empty Activity`模板:

[![](img/dfaa41a52bb0e2916c075d4f0cc0143b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VDUvXVVV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/eLo7Sia.png)

保持活动名称不变:

[![](img/054d61d0bd88a4086aacc1500ec9f1c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nF2ma3MD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8M3pwcQ.png)

### AndroidManifest.xml

在`AndroidManifest.xml`文件:
中设置权限(两个`uses-permission`标签)和`receiver`标签

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.nikola.callblockingtestdemo">

    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.CALL_PHONE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <receiver  android:name=".IncomingCallReceiver" android:enabled="true" android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.PHONE_STATE" />
            </intent-filter>
        </receiver>
    </application>
</manifest> 
```

Enter fullscreen mode Exit fullscreen mode

获得`READ_PHONE_STATE`许可后，我们获得(定义见[官方文件](https://developer.android.com/reference/android/Manifest.permission.html#READ_PHONE_STATE)):

> 允许只读访问电话状态，包括设备的电话号码、当前蜂窝网络信息、任何正在进行的呼叫的状态以及设备上注册的任何电话帐户的列表。

获得`CALL_PHONE`许可后，我们获得(定义见[官方文件](https://developer.android.com/reference/android/Manifest.permission.html#CALL_PHONE)):

> 允许应用程序发起电话呼叫，而无需通过拨号器用户界面让用户确认呼叫。

⚠️:我发现，即使这里没有说明，我也需要这个权限，这样我就可以以编程方式结束调用。

标签用来定义一个处理广播动作的类。顾名思义，当电话呼叫的状态改变时(我们接到电话、拒绝电话、正在通话等)，Android OS 将广播这个动作。).

### IncomingCallReceiver.java

创建一个新类(`File->New->Java Class`)，命名为`IncomingCallReceiver`并将这段代码粘贴到(*注意:你的`package`名称将与我的不同！* ):

```
package com.example.nikola.callblockingtestdemo;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.telephony.TelephonyManager;
import android.widget.Toast;
import java.lang.reflect.Method;
import com.android.internal.telephony.ITelephony;

public class IncomingCallReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {

        ITelephony telephonyService;
        try {
            String state = intent.getStringExtra(TelephonyManager.EXTRA_STATE);
            String number = intent.getExtras().getString(TelephonyManager.EXTRA_INCOMING_NUMBER);

            if(state.equalsIgnoreCase(TelephonyManager.EXTRA_STATE_RINGING)){
                TelephonyManager tm = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
                try {
                    Method m = tm.getClass().getDeclaredMethod("getITelephony");

                    m.setAccessible(true);
                    telephonyService = (ITelephony) m.invoke(tm);

                    if ((number != null)) {
                        telephonyService.endCall();
                        Toast.makeText(context, "Ending the call from: " + number, Toast.LENGTH_SHORT).show();
                    }

                } catch (Exception e) {
                    e.printStackTrace();
                }

                Toast.makeText(context, "Ring " + number, Toast.LENGTH_SHORT).show();

            }
            if(state.equalsIgnoreCase(TelephonyManager.EXTRA_STATE_OFFHOOK)){
                Toast.makeText(context, "Answered " + number, Toast.LENGTH_SHORT).show();
            }
            if(state.equalsIgnoreCase(TelephonyManager.EXTRA_STATE_IDLE)){
                Toast.makeText(context, "Idle "+ number, Toast.LENGTH_SHORT).show();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Android 中，如果我们想从`BroadcastReceiver`中‘获取’数据，我们需要继承`BroadcastReceiver`类，并且我们需要覆盖`onReceive`方法。在这个方法中，我们使用`TelephonyManager`来获取呼叫的状态，并使用`ITelephony`接口来结束呼叫。

老实说，这就是它变得有点“奇怪”的地方，要获得这个`ITelephony`接口，您需要创建`ITelephony`接口。

### ITelephony.java

为此，创建一个新类(`File->New->Java Class`)，将其命名为`ITelephony`，并将这段代码粘贴到(*注意:用下面的内容覆盖所有内容；没错，连诡异的包名*:

```
package com.android.internal.telephony;

public interface ITelephony {
    boolean endCall();
    void answerRingingCall();
    void silenceRinger();
} 
```

Enter fullscreen mode Exit fullscreen mode

Android Studio 会抱怨`package com.android.internal.telephony;`(这个包名称下的红色曲线点)，但这就是它必须如何设置才能工作。我没有找到确切的解释为什么必须包含这个，所以如果你知道，请在评论中分享。

### 运行时请求权限

这是阻碍我成功实现这一目标的一个因素！

即在 Android 6.0+之后，即使你在`AndroidManifest.xml`文件中设置了权限，如果它们属于**危险**权限的范畴，你仍然必须明确地向用户询问它们。这是此类权限的列表:

*   访问 _ 粗略 _ 位置
*   访问 _ 精细 _ 位置
*   添加 _ 语音邮件
*   车身 _ 传感器
*   呼叫电话
*   照相机
*   获取 _ 帐户
*   处理 _ 呼出 _ 呼叫
*   阅读 _ 日历
*   读取呼叫日志
*   读取 _ 单元格 _ 广播
*   阅读 _ 联系人
*   读取外部存储
*   读取电话状态
*   阅读 _ 短信
*   接收 _ 彩信
*   接收 _ 短信
*   接收 _WAP_PUSH
*   录音 _ 音频
*   发送 _ 短信
*   使用 _SIP
*   写日历
*   写呼叫日志
*   写联系人
*   写 _ 外部 _ 存储

要获得这样的许可，你可以使用下面的代码(我在`onCreate`方法的`MainActivity.java`中使用了它):

```
if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.M) {
    if (checkSelfPermission(Manifest.permission.READ_PHONE_STATE) == PackageManager.PERMISSION_DENIED || checkSelfPermission(Manifest.permission.CALL_PHONE) == PackageManager.PERMISSION_DENIED) {
        String[] permissions = {Manifest.permission.READ_PHONE_STATE, Manifest.permission.CALL_PHONE};
        requestPermissions(permissions, PERMISSION_REQUEST_READ_PHONE_STATE);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`PERMISSION_REQUEST_READ_PHONE_STATE`变量用于确定在`onRequestPermissionsResult`方法中请求了哪个权限。当然，如果不需要根据用户是否批准权限来执行任何逻辑，可以省略这个方法:

```
@Override
public void onRequestPermissionsResult(int requestCode, String permissions[], int[] grantResults) {
    switch (requestCode) {
        case PERMISSION_REQUEST_READ_PHONE_STATE: {
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                Toast.makeText(this, "Permission granted: " + PERMISSION_REQUEST_READ_PHONE_STATE, Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(this, "Permission NOT granted: " + PERMISSION_REQUEST_READ_PHONE_STATE, Toast.LENGTH_SHORT).show();
            }

            return;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### App 在行动

这是这款应用在运行中的样子，在模拟器上进行测试，并在 Android Studio 中使用 [Android 设备监视器](http://www.nikola-breznjak.com/blog/android/simulate-incoming-call-sms-emulator-android-studio/)触发调用:

[![](img/b539d6fb4543abbe905131c75926fefe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tPPYROcg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/CjU9D4J.gif)

## 结论

在这篇文章中，我向你展示了如何制作一个原生 Android 应用程序，可以阻止某些号码打电话给你。我指出了我所面临的拦截器，我仍在寻找一种隐藏本地来电弹出窗口的解决方案，这种弹出窗口有时会在电话被拒绝之前出现一小会儿。

所以，如果你有任何想法，我很乐意接受建议💪