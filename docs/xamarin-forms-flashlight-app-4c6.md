# Xamarin。表单-手电筒应用程序

> 原文：<https://dev.to/logeshpalani98/xamarin-forms-flashlight-app-4c6>

**简介**

这篇文章演示了如何在 Xamarin 中创建一个手电筒应用程序。表单应用程序。这意味着如果用户启用拨动开关，LED 就会打开。类似地，用户禁用拨动开关，LED 关闭。默认情况下，Xamarin 中不会列出这些功能。表单应用程序。所以，我们需要为此安装一个插件。

* *NuGet 包-搜索 Xamarin。表单**

*   kphillpotts。Lamp.Plugin ** Android 输出**

| [![Xamarin](../Images/20a39b205c3b407b4fa4941e61b7249d.png "Xamarin.Forms") ](https://2.bp.blogspot.com/-dO02YggTyz4/W4V1mPfcg0I/AAAAAAAAK2U/sTFlgJfFQxgMahab2WGWw0iAmK24nCg-wCEwYBhgL/s1600/Android%2BOutput.gif) |
|安卓输出|

我们开始吧。

**第一步**

```
 You can create a new Xamarin.Forms app by going to File \>\> New \>\> Visual C# \>\> Cross-Platform \>\> Cross-Platform App (Xamarin.Forms or Xamarin.Native), give the application name, and location directory path then click OK. 
```

(例如-项目名称:LampDemo)

| [![Xamarin](../Images/f7ca3815f020e660011462213c4db1c4.png "Xamarin.Forms") ](https://3.bp.blogspot.com/-u41JsyR-tXU/W4V1mCgf0bI/AAAAAAAAK2Y/BgBHdsM5xO8DUD_y_YxABcuO9dy4UWaaQCEwYBhgL/s1600/Create.png) |
|创建

**第二步**

```
 After the project creation, add the following NuGet package to your project. 
```

*   kphillpotts。灯。插件

| [![Xamarin](../Images/49a56abe0cb383609794b806e1823fb5.png "Xamarin.Forms") ](https://2.bp.blogspot.com/-gnIrorfpP18/W4V1mEhXH9I/AAAAAAAAK2c/OkAnE0cQbV4lpoKWbB6maquRn0DCiu4VQCEwYBhgL/s1600/Nuget.png) |
| Nuget |

为此，请打开解决方案资源管理器并选择您的解决方案。右键单击并选择“管理解决方案的 NuGet 包”。在弹出窗口中，单击导航到“浏览”选项卡，然后单击浏览“kphillpotts”。Lamp.Plugin”并选择以下 NuGet 包，选择您的项目，然后安装它。

**第三步**

```
 Now, open MainPage.xaml page. For that, go to Solution Explorer \>\> LampDemo (PCL) \>\>double click to open MainPage.xaml and here is the code of this page. In this page, we have designed one Toggle switch and this switch is used to turn on and turn off the LED. 
```

**XAML 电码**

**第四步**

接下来，打开解决方案浏览器> > LampDemo(PCL) > >点击打开主页面的设计视图。这是这个页面的代码。

*   _CrossLamp。current . TurnOn()-_ 获取打开 LED 的权限。
*   _CrossLamp。current . Turn Off()-_ 获取关闭 LED 的权限

**C#代码后面**

**第五步**

现在，让我们给出访问手电筒的权限。

* *安卓项目**

```
 Open AndroidManifest.xml file. For that, go to Solution Explorer \>\> LampDemo.Android \>\> double click to open AndroidManifest.xml and add the following permissions are enabled. 
```

* *UWP 项目**

```
 Open Package.Appmanifest file. For that, go to Solution Explorer \>\>LampDemo.UWP \>\>click open Package.Appmanifest file and navigate to "Capabilities" tab then click the following permission checkbox here. 
```

**第六步**

```
 Now, go to "Build " menu and click "Configure  Manager".Here configure your startup projects. Click F5 or start to build and run your application. 
```

最终结果如下所示，

**安卓输出**

**最后，我们成功创建了 Xamarin。使用 kphillpotts.Lamp.Plugin 创建 LampDemo 应用程序. **

* * *

**完整的源代码可以在[这里](https://github.com/logeshpalani98/FlashLight-App)找到。**