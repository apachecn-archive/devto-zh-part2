# 如何检测是否安装了 Chrome 扩展

> 原文：<https://dev.to/twilio/how-to-detect-if-a-chrome-extension-is-installed-3fmk>

在其他帖子中，我们研究了如何在 Chrome 浏览器中捕获屏幕输出，并构建了一个 T2 屏幕共享视频聊天应用。不过，有一个功能缺失了。Chrome 扩展使屏幕捕捉成为可能，但在应用程序试图使用它之前，没有测试它是否已经安装。在这篇文章中，我们将构建一个可以从前端检测到的 Chrome 扩展。

## 开始设置

我们将使用我们为屏幕捕捉构建的扩展，并添加功能使其可检测。然后，我们将构建一个示例来展示如何处理这两种情况，有和没有扩展。

从 [GitHub repo](https://github.com/philnash/screen-capture) 下载扩展的源代码，或者克隆`building-extension-detection`分支

```
git clone -b building-extension-detection https://github.com/philnash/screen-capture.git
cd screen-capture 
```

Enter fullscreen mode Exit fullscreen mode

## 分机

打开`extension/extension.js`看一看第 1 行。

```
chrome.runtime.onMessageExternal.addListener( 
```

Enter fullscreen mode Exit fullscreen mode

这段代码让我们监听从网页发送到扩展的消息。从网页发送消息的代码看起来像:

```
chrome.runtime.sendMessage(EXTENSION_ID, request, (response) => { }) 
```

Enter fullscreen mode Exit fullscreen mode

通过调用提供扩展 ID 的`sendMessage`函数、作为请求的对象和具有响应参数的回调函数，网页可以向特定的扩展发送消息。我们将使用这个响应参数来检测扩展是否存在。

我们可以这样做，因为在 Chrome 中，向不存在的扩展发送消息将导致回调以`null`作为响应参数执行..我们的应用程序可以测试该空值，并决定是否继续屏幕截图流程的其余部分。

**注意**:如果你正在使用一个不使用背景页面的扩展，那么你需要添加一个背景页面。您需要在 manifest.json 中使用`chrome.runtime.onMessageExternal.addListener(`函数监听传入消息的[这几行，就像示例项目的](https://github.com/philnash/screen-capture/blob/master/extension/manifest.json#L7-L13) [`extension.js`](https://github.com/philnash/screen-capture/blob/master/extension/extension.js#L1-L2) 。

## 回复消息

扩展已经在响应消息，但是它目前只响应单一类型的消息，并且只响应用户选择的屏幕流。为了检测扩展，我们需要能够发送回一个更简单的消息。该消息可能只是一个布尔值，但为了将来更有用，我们将深入扩展清单并返回扩展的当前版本。

将以下代码添加到`extension.js`
的顶部

```
chrome.runtime.onMessageExternal.addListener(
  (message, sender, sendResponse) => {
    if (message == 'version') {
      const manifest = chrome.runtime.getManifest();
      sendResponse({
        type: 'success',
        version: manifest.version
      });
      return true;
    }
    const sources = message.sources;
    const tab = sender.tab;
    chrome.desktopCapture.chooseDesktopMedia(sources, tab, streamId => {
      // result of selecting desktop
    });
    return true;
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当发送消息`"version"`时，扩展将用当前版本进行响应。

打开 Chrome 扩展设置(chrome://extensions)并上传或重新加载扩展，记下 ID。现在让我们看看如何在页面上使用它。

## 从页面中检测扩展名

让我们更新 Chrome 屏幕截图示例来检查扩展。从回购中打开`chrome/index.html`。首先，隐藏“获得屏幕”按钮，这样当我们不知道它是否工作时，没有人会试图与它交互。

```
 <video autoplay id="screen-view" width="50%"></video>
  <button id="get-screen" style="display:none">Get the screen</button>
  <button id="stop-screen" style="display:none">Stop the screen</button> 
```

Enter fullscreen mode Exit fullscreen mode

当页面加载时，我们将发送消息`"version"`到扩展，看看它是否存在。如果扩展返回它的版本，我们将取消隐藏按钮并设置点击监听器。不要忘记填写您自己的分机 ID。

```
 (() => {
      const EXTENSION_ID = 'YOUR_EXTENSION_ID_HERE';

      chrome.runtime.sendMessage(EXTENSION_ID, 'version', response => {
        if (!response) {
          console.log('No extension');
          return;
        }
        console.log('Extension version: ', response.version);
        const video = document.getElementById('screen-view');
        const getScreen = document.getElementById('get-screen');
        const stopScreen = document.getElementById('stop-screen');
        const request = { sources: ['window', 'screen', 'tab'] };
        let stream;

        getScreen.style.display = 'inline';
        getScreen.addEventListener('click', event => {
          // code to capture the screen and add to the video element
        });

        stopScreen.addEventListener('click', event => {
          // code to stop the screen capture
        });
      });
    })(); 
```

Enter fullscreen mode Exit fullscreen mode

通过在 localhost 上提供`chrome/index.html`来测试这一点(为此我使用了 [serve](https://www.npmjs.com/package/serve) )并在 Chrome 中打开页面。该扩展目前已安装并启用，因此“获取屏幕”按钮将出现。当前的扩展版本将记录在控制台中。

从 Chrome 扩展设置(chrome://extensions)中禁用扩展，然后重新加载页面。这一次，按钮将不会出现，控制台日志将显示“No extension”。

## 扩展检测完成

如果你想在 Chrome 中建立一个带有[截屏的应用，你需要使用一个扩展。现在您知道了如何构建一个可以被检测到的扩展，以便仅当该扩展存在时才启用该特性。为了改进这一点，您可以](https://www.twilio.com/blog/2017/10/screen-capture-in-google-chrome.html)[触发您的扩展](https://developer.chrome.com/webstore/inline_installation?csw=1)的内嵌安装，如果它仅在用户需要时才可用。

完整代码可在 [GitHub repo](https://github.com/philnash/screen-capture) 的主分支中获得。

请注意，您确实需要知道扩展的 ID，并将其设置为可以从需要该扩展的域进行外部连接。这意味着这种技术不太可能适用于第三方扩展，它只对检测您自己的扩展有用。

对此有疑问吗？把它们写在下面的评论里，或者在 Twitter @ philnash 上告诉我。

* * *

[如何检测是否安装了 Chrome 扩展](https://www.twilio.com/blog/2018/03/detect-chrome-extension-installed.html)最初发表于 2018 年 3 月 1 日的 [Twilio 博客](https://www.twilio.com/blog)。_