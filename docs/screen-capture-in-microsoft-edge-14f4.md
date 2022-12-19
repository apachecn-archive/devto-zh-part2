# Microsoft Edge 中的屏幕捕获

> 原文：<https://dev.to/twilio/screen-capture-in-microsoft-edge-14f4>

我们之前已经讨论过如何在 Chrome 和 Firefox 中捕捉用户的屏幕。好消息，另一个浏览器现在支持了，微软的 Edge。

让我们看看如何用 Edge 捕捉屏幕。

[![The result of the code in this post. When you visit the project in Edge, you can capture the screen and show it in a video on the page.](../Images/bbb543abe00e6287fbc68378128293b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mwgxEY1_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2018/05/Xy9veXGKIqjoU6gqk0XoBElSSzuT68ZwxKa2e0TOCMa7X4kjuJEiVHox63uYSyWc7WbWWaoOsJaox_wcmQ1Vs0Zq6ZGCQmAwC10ZHe4_9pIv_P8PzqCstXq4cp3jo_lTfgamdyg.png)

## 你需要什么

*   最新版本的 [Edge](https://www.microsoft.com/en-us/windows/microsoft-edge) ，目前是 42 版本，EdgeHTML 版本 17(如果你像我一样在 Mac 上，你可以得到一个安装了 Windows 10 和 Edge 的[免费虚拟机进行测试)](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)
*   文本编辑器
*   本地网络服务器——我喜欢用 [serve](https://www.npmjs.com/package/serve) 来做这样的事情
*   为本地主机设置的  [ngrok](https://ngrok.com/) 或等效的隧道服务或 TLS 证书(我们稍后会看到原因)

## 屏幕截图

Chrome 需要构建一个扩展，Firefox 使用带有`"screen"`的`mediaSource`约束的`getUserMedia`来处理屏幕流，Edge 再次使用了不同的方法。虽然这听起来不太好，但实际上对 Edge 来说更好，因为他们遵循了提议的 [W3C 屏幕捕捉规范](https://w3c.github.io/mediacapture-screen-share/)。

这种支持是作为 Windows 10 和 Edge 的 4 月更新的一部分推出的，也是 [EdgeHTML 引擎版本 17](https://docs.microsoft.com/en-us/microsoft-edge/dev-guide#whats-new-in-edgehtml-17) 的一部分。那么它是如何工作的呢？

### 代码

要在 Edge 中访问屏幕的媒体流，代码看起来有点像这样:

```
navigator.getDisplayMedia().then(returnedStream => {
  // use the stream
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你将它与[火狐版本](https://www.twilio.com/blog/2017/10/screen-capture-in-firefox.html)相比，它要简单一些。你只需要调用 [`getDisplayMedia`](https://w3c.github.io/mediacapture-screen-share/) ，而不是调用`navigator.mediaDevices.getUserMedia`并传递一个媒体约束`{ video: { mediaSource: 'screen' } }`。在这个版本中，`getDisplayMedia`没有任何媒体限制，所以用户可以选择是显示他们的应用程序还是桌面。

为了探索这一切是如何结合在一起的，让我们构建一个我们为 Chrome 和 Firefox 构建的相同的示例应用程序，捕获屏幕，然后在一个`<video>`元素中显示它。

## 建筑截屏

创建一个新目录和一个`index.html`文件。我们将使用与 [Chrome 示例](https://github.com/philnash/screen-capture/blob/master/chrome/index.html)相同的 HTML 结构。将以下内容添加到您的`index.html`文件中:

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Screen
</head>

<body>
  <h1>Show my screen</h1>

  <video autoplay id="screen-view" width="50%"></video>

  <button id="get-screen">Get the screen</button>
  <button id="stop-screen" style="display:none">Stop the screen</button>

  <script>
    // Fill in the rest here
  </script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们一个简单的 HTML 页面，带有一个`<video>`元素和几个按钮。

我们代码的其余部分放在`<script>`标签之间。我们从获取对将要使用的元素的引用开始。然后，我们为屏幕流定义一个变量，并定义一个事件侦听器，当您单击 stop stream 按钮时，该事件侦听器会停止流。

```
(() => {
  const video = document.getElementById('screen-view');
  const getScreen = document.getElementById('get-screen');
  const stopScreen = document.getElementById('stop-screen');
  let stream;

  // Fill in the rest here

  stopScreen.addEventListener('click', event => {
    stream.getTracks().forEach(track => track.stop());
    video.src = '';
    stopScreen.style.display = 'none';
    getScreen.style.display = 'inline';
  });
})(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当用户点击“获取屏幕”按钮时，我们将调用`getDisplayMedia`函数:

```
let stream;

getScreen.addEventListener('click', event => {
  navigator.getDisplayMedia();
}); 
```

Enter fullscreen mode Exit fullscreen mode

调用`getDisplayMedia`将向用户显示一个提示，请求允许使用他们的屏幕。然后，用户可以选择他们想要共享的窗口或整个桌面。`getDisplayMedia`方法返回一个[承诺](https://www.twilio.com/blog/2016/10/guide-to-javascript-promises.html)，所以一旦成功完成，承诺将通过屏幕流解析。然后我们需要将它设置为`<video>` :
的源

```
let stream;

getScreen.addEventListener('click', event => {
  navigator.getDisplayMedia().then(returnedStream => {
    stream = returnedStream;
    video.src = URL.createObjectURL(stream);
    getScreen.style.display = "none";
    stopScreen.style.display = "inline";
  }).catch(err => {
    console.error('Could not get stream: ', err);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们在承诺中添加一个`catch`来处理错误或者用户拒绝许可，这就是我们需要的所有代码。

## 捕捉屏幕

要运行这个示例，我们需要从本地 web 服务器提供 HTML。我喜欢用一个叫做 [serve](https://www.npmjs.com/package/serve) 的 npm 模块来做这件事。如果你安装了 Node.js 和 npm，可以用:
安装

```
npm install serve -g 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用命令行导航到保存您的`index.html`文件的目录，并通过输入:
在`localhost:5000`上提供它

```
serve . 
```

Enter fullscreen mode Exit fullscreen mode

如果您有另一种在本地主机上提供静态文件的方法，也可以使用这种方法。

我们还没有完成，很像火狐，Edge 要求该网站在 HTTPS 上提供服务，以便允许开发者访问屏幕捕捉 API。与其尝试在您的开发机器上整理自签名证书，我建议使用 ngrok 来回避这个问题。我通常使用 [ngrok 在本地测试 web hooks](https://www.twilio.com/blog/2015/09/6-awesome-reasons-to-use-ngrok-when-testing-webhooks.html)，但是它有一个额外的好处，给你一个指向你本地机器的 HTTPS URL。[安装 ngrok](https://ngrok.com/download) (如果你在 Windows 上安装，请查看[这些说明)并在 localhost:5000:
上启动它](https://www.twilio.com/docs/usage/tutorials/how-use-ngrok-windows-and-visual-studio-test-webhooks)

```
ngrok http 5000 
```

Enter fullscreen mode Exit fullscreen mode

获取 HTTPS 的网址，并在 Edge 中输入。

[![The screen when ngrok is running. Select the URL that begins with HTTPS](../Images/497ee1b3859807f889f19616c33cc869.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nZvdUHba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2018/05/F6pXOvjX5kA1q1YrZa-9gxSvqGbJXp31bTUb3IWeO6m_ZSROVaZJMQEQWvZFYf23qkge_XD-7Wfx7sDlD5EvlXIif0DyuBON8ELbNAgwf1SFKLKK1yx858Z-HnINXBZH4rO94T4.png)

按下“获取屏幕”按钮，您将能够获得用户选择的应用程序或桌面的流。

[![Success! Now, when you click 'Get the screen' the permissions pop up shows and when you allow it, you can choose the application or screen to show.](../Images/9a34f534a583640410cde7d0872f8e67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jxjKAMDV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2018/05/v1jyWBvESpG8jDtkNu4Uul33FFikEL4fewltXOyHGY8yt-Zy0S0mDyLbvkC6h0ANLFv23DElo3FgyLV-j9xNGZs-vDmBPVQ-OuWhByDkkLAoqojzmEDfO4kOwoHuvubjJQCpVAU.png)

## 下一步

现在我们已经在 [Chrome](https://www.twilio.com/blog/2017/10/screen-capture-in-google-chrome.html) 、 [Firefox](https://www.twilio.com/blog/2017/10/screen-capture-in-firefox.html) 和 Edge 中看到了截屏。如果你想看看这三个的代码，[看看 GitHub repo](https://github.com/philnash/screen-capture) 。

如你所见， [`getDisplayMedia`](https://w3c.github.io/mediacapture-screen-share/) 是一种比构建扩展或检查特定版本的 Firefox 更容易获得用户屏幕的方式。如果你认为这个规范应该由那些浏览器来实现，通过提出或者[支持他们公开的 bug](https://bugzilla.mozilla.org/show_bug.cgi?id=1321221)来和他们取得联系。

你对浏览器的截屏有什么想法或计划吗？请在下面的评论中告诉我你的想法。或者在推特上发[@菲尔纳什](https://www.twitter.com/philnash)或者发邮件到 philnash@twilio.com联系。

* * *

微软 Edge 中的 *[截屏原载于 2018 年 5 月 16 日](https://www.twilio.com/blog/2018/05/screen-capture-in-microsoft-edge.html) [Twilio 博客](https://www.twilio.com/blog)。*