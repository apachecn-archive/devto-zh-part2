# 更改状态栏颜色。表单]

> 原文：<https://dev.to/luciomsp/change-statusbar-color-xamarinforms-3dob>

通常，在开发应用程序时，我们希望能够根据客户的要求提供完整的用户界面。因此，当我们启动该应用程序时，状态栏是另一种颜色，这与上述不同，我们这里需要的是将颜色更改为类似于应用程序的颜色，我可以在 xamarin . forms 中执行此操作吗？

这相对简单，下面按平台列出>

机器人

window . setsstatusbarcolor(Android . graphics . color . parse color(" # ffff "))；//或 MainActivity 中的任何其他十六进位值。

ios

iOS 状态栏的颜色取决于导航栏的颜色，因此>

main page = new navigation page(new MyPage()){ BarBackgroundColor = Color。FromHex("000000 ")，BarTextColor = Color。白色}；

我会在导览列上建立黑色导览列和白色文字导览列。若要变更状态列上文字的颜色，您必须在 plist 资讯中设定> uistatusbarstyle uistatusbarstyles translatent

uistatusbarstyle black translucent significa texto Blanco。

var statusBar = Windows。UI . view management . status bar . getforcurrentview()；

其中 StatusBar 具有 BackgroundColor、backgroundopacity、ForegroundColor 属性。

* * *