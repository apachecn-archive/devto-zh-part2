# 调试 Expo/React 本机 WebView

> 原文：<https://dev.to/ghost/debug-exporeact-native-webview-1b3o>

我的 React Native 应用程序的“核心”逻辑涉及使用 WebViews，因为我需要访问 HTML5 画布。在开发此代码时，WebView 代码肯定会有错误和问题。弄清楚如何在 WebView 中调试代码并不那么明显。

[![](img/c5ede01eca69b451bbc4f76a5c51c943.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YElUFeE7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYs0oK49sjCS_ByJfPO8zSw.png) 

<figcaption>图 1: Chrome 对 WebView 的检查</figcaption>

### **选项 1:镀铬检查**

*   启动 Expo/React 本地应用*
*   打开 chrome，然后转到以下网址， [chrome://inspect](https://dev.tochrome://inspect)
*   然后点击 *inspect* 按钮，点击顶部链接打开最新的 WebView，应该会看到类似图 2 的东西
*   您应该会看到类似于图 1 的内容。现在，您可以像浏览普通网页一样浏览 WebView

[![](img/fd7be887d6974ec01fed04dcfc60662b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QVSlLJg2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/556/1%2AzC67_HMx3ckYKMyM3Box_w.png) 

<figcaption>图 2:浏览量列表</figcaption>

### 选项 2: React 本机调试器

或者，您可以使用 chrome 中的 React 本机调试器页面来访问该页面。

*   启动 Expo/React 本地应用*
*   转到 chrome 上的 React 本机调试器，在我的例子中，因为我使用 Expo，所以我的 URL 是[http://192 . 168 . 27 . 128:19001/debugger-ui/](http://192.168.27.128:19001/debugger-ui/)
*   进入三点按钮(关闭按钮旁边) >更多工具>远程设备
*   在底部，你会看到另一个面板打开
*   您应该可以在列表中看到您的 Android 设备
*   点击第一个项目的检查按钮(这将是最新的)
*   现在，您可以像浏览普通网页一样浏览 WebView

### 调试

因此，我发现调试 WebView 的最接近的方法是使用将出现在 DevTools 控制台中的 *console.log* 语句。在普通的 React 本机调试器控制台中，您将无法从 WebView 中看到任何 console.log 语句。您只能通过浏览用于远程设备的 DevTools 来查看它们。据我所知，你不能在网页视图中使用断点，但这仍然比什么都没有好。

[![](img/c501509ddcc140205592f3554e8a3fc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---ujmya7H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AB-x8MieGiYqAQWV4MCE1bw.gif) 

<figcaption>图 3:为远程设备打开 DevTools】</figcaption>

### **附录**

*确保在运行模拟器的同一台计算机/主机上执行此操作。例如，一个 Genymotion 虚拟机正在我的 Windows 机器上运行，但我仍然可以在 chrome inspect URL 上看到它的网络视图。这可以在图 3 中看到。

[![](img/7eef0f7cc6af8c1819c9c216daf72479.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0aZVjUvr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0XdcaoacZPT3H1LvV9tXvw.png) 

<figcaption>图 4:Windows 主机上的 Genymotion VM 和 Chrome。</figcaption>

**链接**

*   在这篇文章的帮助下
*   [元宵节](https://www.genymotion.com/)