# 使用 WinAPI 声明用 Xojo 刷新窗口

> 原文：<https://dev.to/lefebvre/flash-a-window-with-xojo-using-a-winapi-declare-1458>

通过使用 Declare 命令，可以调用 Win32 APIs(也称为 WinAPI)来使用框架中没有内置的方法和属性。要创建 Declare 语句，您首先需要使用微软的文档找到您想要使用的 API:[微软开发者文档](https://msdn.microsoft.com/en-us/library/windows/desktop/hh920508(v=vs.85).aspx)。

Win32 API 主要基于 C/C++编程语言，并大量使用结构。

举个简单的例子，你可以调用一个简单的函数来闪烁窗口，以引起用户的注意。参考微软 Win32 文档中的 [FlashWindow 文档页](https://msdn.microsoft.com/en-us/library/windows/desktop/ms679346%28v=vs.85%29.aspx?f=255&MSPPError=-2147217396)，在那里可以看到该方法的声明。看起来是这样的:

```
BOOL WINAPI FlashWindow(
 _In_ HWND hWnd,
 _In_ BOOL bInvert
); 
```

Enter fullscreen mode Exit fullscreen mode

这告诉您这个方法是一个函数(开头的 BOOL 表示它返回一个布尔值),并且它有两个参数。HWND 参数是窗口的句柄，它是作为 window 从 Xojo 获得的。句柄(是整数)。BOOL 参数是一个布尔值。在文档页面的底部有一个部分告诉你包含这个函数的库，就是“User32.lib”。有了这些信息，您可以创建该函数的 Declare 语句，如下所示:

```
Declare Function FlashWindow Lib "User32" (handle As Integer, invert As Boolean) As Boolean 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过传入一个窗口句柄来调用此函数，因此，如果您在按钮的 Action 事件处理程序上声明了上述内容，您可以用以下代码来刷新窗口:

```
Call FlashWindow(MyWindow.Handle, True) 
```

Enter fullscreen mode Exit fullscreen mode

因为不需要返回值，所以 Call 语句用于避免必须声明一个变量来存储结果。

如果你想让它更容易访问，你可以使用 Extends method 特性使它对任何窗口都可用。为此，请使用以下代码在模块上创建一个全局方法:

```
Public Sub Flash(Extends w As Window)
  #If TargetWindows Then
    Declare Function FlashWindow Lib "User32" (handle As Integer, invert As Boolean) As Boolean
    Call FlashWindow(w.Handle, True)
  #EndIf
End Sub 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以用以下代码调用此方法:

```
Self.Flash 
```

Enter fullscreen mode Exit fullscreen mode

有关更多信息:

*   [调用本地 Windows API](http://developer.xojo.com/calling-native-windows-apis)
*   [GitHub 上的 WinAPI 库](https://blog.xojo.com/2017/12/20/winapilib-on-github/)