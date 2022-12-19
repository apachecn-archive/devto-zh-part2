# 在 React 本机应用程序中呈现原始 HTML

> 原文：<https://dev.to/skptricks/rendering-raw-html-in-your-react-native-application-2e32>

帖子链接:[在 React 本地应用程序中呈现原始 HTML](https://www.skptricks.com/2018/11/rendering-raw-html-in-your-react-native-app.html)

本教程解释了如何使用 WebView 组件在 react 本机应用程序中呈现原始 HTML 代码。基本上，WebView 是 Android 操作系统(OS)的一个系统组件，允许 Android 应用程序直接在应用程序中显示来自 web 的内容。
在本例中，我们将通过 WebView 组件在 android 应用程序中呈现原始 HTML 代码，它确实需要手动或按程序调整大小，而不是由组件本身处理。此外，为了更好地与 UI 的其余部分保持一致，对视图内容进行样式化也需要类似的解决方案，将样式表传递到 WebView 容器中，甚至更糟，在 HTML 本身中包含样式属性。
注意:在本例中，我们使用 WebView 组件的 source prop 在 android 或 ios 屏幕上显示原始 HTML 代码。

[在 React 本地应用中呈现原始 HTML](https://www.skptricks.com/2018/11/rendering-raw-html-in-your-react-native-app.html)

[![](../Images/db6059ccb113042102f8255d4c55f10d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2AXHAgc7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://4.bp.blogspot.com/-3doyzMrFXpM/W9-1JcfsVRI/AAAAAAAACGQ/Vn6L9WLApYEGcXsR6mtJhgaQY2l9OdY_QCLcBGAs/s640/re.png)

[继续阅读...](https://www.skptricks.com/2018/11/rendering-raw-html-in-your-react-native-app.html)