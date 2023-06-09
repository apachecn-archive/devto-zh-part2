# 直接下载任何 Xcode

> 原文：<https://dev.to/jahirfiquitiva/direct-download-any-xcode-385o>

从 App Store 下载 Xcode，甚至是**。当您的连接不像其他浏览器那样稳定和快速时，当下载随时可能被中断并且您很可能不得不重新开始整个下载时，使用浏览器时的 xip** 文件(无论是哪种浏览器)可能是一种痛苦。

这件事刚刚发生在我身上，我写这篇文章给自己，也是为了和其他可能面临类似情况的人分享。

下面是我下载它的过程…

### 1。安装 Xcode 命令行工具

这是一个大约 183Mb 的小软件包，我认为它足以通过你的浏览器下载。

*   前往[开发者下载网站](https://developer.apple.com/download/more/)
*   如果需要，请使用您的 Apple ID 登录
*   搜索你要下载的命令行包(我准备用最新的 Xcode 10 和 macOS Mojave 的稳定命令行工具)。![Command Line Tools download option](img/3e066ac65800d3c4eb36a6bb3bfaf5e2.png)
*   按下右侧的链接，等待下载完成
*   一旦下载，安装它作为任何其他。dmg 包。

### 2。安装[自制软件](https://brew.sh/)和 wget

我用[自制](https://brew.sh/)安装了 ***wget*** 。可能还有其他方法，但这是我的方法。😅

*   开放终端
*   奔跑

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

*   按照提示的步骤操作
*   一旦完成，运行

```
brew install wget 
```

Enter fullscreen mode Exit fullscreen mode

### 3。从浏览器安装并获取 cookies.txt

*   [下载谷歌 Chrome](https://chrome.google.com/webstore/detail/cookiestxt/njabckikapfpffapmjgojcnbfjonfjfg) (没有其他浏览器的链接，抱歉)
*   再次访问[开发者下载网站](https://developer.apple.com/download/more/)。
*   单击 cookies.txt 图标
*   在顶部你会看到一个链接，上面写着***‘点击这里’***。点击下载 ***cookies.txt*** 文件
*   无论你想把它保存在哪里

### 4。获取 Xcode 直接下载链接

*   再次访问[开发者下载网站](https://developer.apple.com/download/more/)。
*   选择你要下载的 Xcode 版本(我准备下载 Xcode 10)。![Xcode 10 download option](img/a0bfad4bd60b48500392c3e622ce8aa8.png)
*   右击右边的链接，选择“复制链接地址”
*   或者，访问这个 [StackOverflow answer](https://stackoverflow.com/a/10335943) 获得所有 Xcode 版本的链接。

### 5。开始下载

*   打开终端，转到你的 ***cookies.txt*** 文件所在的位置。
*   现在快跑

```
wget --load-cookies=cookies.txt -c https://download.developer.apple.com/Developer_Tools/Xcode_10/Xcode_10.xip 
```

Enter fullscreen mode Exit fullscreen mode

*   将末尾的链接替换为您想要下载的文件的链接，该文件是您在上一步中获得的。
*   等待下载完成。

**备注:**

*   将 ***-b*** 添加到下载命令中以在后台运行它，尽管没有它更容易检查进度和状态。
*   命令中的 ***-c*** 选项允许您 **c** 继续下载，即使您关闭了终端，或者取消了下载过程，或者重启了笔记本电脑等等。

### 6。安装 Xcode

*   打开下载的文件，以便它开始解压缩。
*   将解压后的文件移动到 ***应用文件夹*** 就大功告成了！

希望你觉得这有用，请不要忘记与他人分享！
了解我更多@[jahir . XYZ](https://jahir.xyz)T3】图片鸣谢:【unsplash.com】T4T6】原载于[medium.com](https://medium.com/@jahirfiquitiva/direct-download-any-xcode-57f9cb969bf2)