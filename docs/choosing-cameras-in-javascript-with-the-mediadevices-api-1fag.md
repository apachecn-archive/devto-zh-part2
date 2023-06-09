# 使用 mediaDevices API 在 JavaScript 中选择摄像机

> 原文：<https://dev.to/twilio/choosing-cameras-in-javascript-with-the-mediadevices-api-1fag>

大多数智能手机都有一个前置和后置摄像头，当你为移动设备创建视频应用程序时，你可能想在它们之间进行选择或切换。

如果你正在开发一个聊天应用程序，你可能想要前置摄像头，但如果你正在开发一个相机应用程序，那么你会对后置摄像头更感兴趣。在这篇文章中，我们将看到如何使用`mediaDevices` API 和媒体约束来选择或切换相机。

## 你需要什么

要关注这篇文章，你需要:

*   带有两个摄像头的 iOS 或 Android 设备进行测试，如果你有两个网络摄像头，这也可以在你的笔记本电脑上使用
*   因此你可以很容易地从你的移动设备上访问这个项目(因为我认为 [ngrok 很棒](https://www.twilio.com/blog/2015/09/6-awesome-reasons-to-use-ngrok-when-testing-webhooks.html)
*   GitHub repo 中的代码可以帮助您入门

要获得代码，克隆项目并签出起始项目标记。

```
git clone https://github.com/philnash/mediadevices-camera-selection.git -b initial-project
cd mediadevices-camera-selection 
```

Enter fullscreen mode Exit fullscreen mode

这个入门项目为您提供了一些 HTML 和 CSS，因此我们可以专注于 JavaScript。您可以直接打开`index.html`文件，但是我建议您使用 web 服务器来提供这些文件。我喜欢使用 [npm 模块服务](https://www.npmjs.com/package/serve)。我也在 repo 中包含了 serve，要使用它，首先安装 npm 的依赖项，然后启动服务器。

```
npm install npm start 
```

Enter fullscreen mode Exit fullscreen mode

运行服务器后，使用 ngrok 打开一个到它的隧道。`serve`在端口 5000 上托管文件，要使用 ngrok 隧道连接到该端口，请在新窗口的命令行中输入以下内容:

```
ngrok http 5000 
```

Enter fullscreen mode Exit fullscreen mode

现在你有了一个网站的公开版本，你可以在你的移动设备上打开它，这样你以后就可以测试它了。请确保您打开了 HTTPS URL，因为我们使用的 API 只能在安全的上下文中运行。

[![The ngrok window shows two URLs you can use, pick the HTTPS one.](img/6e4a616c3fb3cac79c6d5ab3c04aeaa4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oSfgV--r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2018/04/I_rv7Y2-mmWS5nsXOggIlsOtxhuUr3JAn0eRAtUtB7D-fm1e1nUF-VbUkGlihAhzgRuBJXZVeoeVgKvvXux5rjVx6SMWzMtFk8T3VvYtzdKTRlLYLe3sXnlYzBodFxMyZ2m17dQ.png)

该应用程序应该如下所示:

[![The app should have a title saying 'Camera fun' with a button and an empty drop down box.](img/7433fb06d200b75857fc2d1e2e870b6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C07jZrj0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2018/04/h0iHFp3LTpszVoQGdl6tWCLGGZZCPW8xBTfuyrHPGl0idifAcMHRPe0lRIyO0we8fTWDbFed_Omw_7JQp6RCJrCHSzd-KCCPW3W6750qrihsKDReT3hZs7Jk6PI6GkR8AA9LAzQ.png)

## 获取媒体流

我们的第一个挑战是将来自任何摄像机的视频流显示在屏幕上。一旦完成，我们将调查选择特定摄像机的选项。打开`app.js`，从 DOM 中选择按钮和视频元素开始:

```
// app.js
const video = document.getElementById('video');
const button = document.getElementById('button'); 
```

Enter fullscreen mode Exit fullscreen mode

当用户点击或触摸按钮时，我们将使用 [`mediaDevices` API](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices) 请求访问摄像头。为此，我们调用 [`navigator.mediaDevices.getUserMedia`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia) 传递一个对象的媒体约束。我们将从一组简单的约束开始，我们只想要视频，所以我们将视频设置为`true`，音频设置为`false`。

`getUserMedia`返回一个承诺，当这个承诺解决后，我们就可以从摄像机访问媒体流。将视频的`srcObj`设置为流，我们将在屏幕上看到它。

```
button.addEventListener('click', event => {
  const constraints = {
    video: true,
    audio: false
  };
  navigator.mediaDevices
    .getUserMedia(constraints)
    .then(stream => {
      video.srcObject = stream;
    })
    .catch(error => {
      console.error(error);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

保存文件，重新加载页面，然后单击按钮。你应该会看到一个权限对话框，要求访问你的相机，一旦获得许可，你的视频就会出现在屏幕上。在你的电脑和手机上试一下，当我用 iPhone 试的时候，我选择的是前置摄像头。

[![The camera app, now with my face in the previously blank space!](img/7b8970e1110859d101bfc857a88a269c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--57PBMH8G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2018/04/Kda5qsIH1Wasn1kXz1lcn9hHd6p4p1wRaUeVLS_nheQgWnjJ9Y8l6lUvdSrKAwF28oLMNJptZ2u64N_4WmuYn7mp4TZKbk-CC0T5y87AhzCOD2Vv6qgQdBZMajxx2ZyNBwKKYf8.png)

如果你用的是 iPhone，一定要在 Safari 中查看，因为这似乎不能在其他浏览器上使用。

## 有哪些相机可用？

API 为我们提供了一种方法来枚举音频和视频输入的所有可用设备。我们将使用 [`enumerateDevices`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/enumerateDevices) 函数为`<select>`框建立一组选项，这样我们就可以用它来选择我们想看的摄像机。再次打开`app.js`，从 DOM:
中选择`<select>`开始

```
const video = document.getElementById('video');
const button = document.getElementById('button');
const select = document.getElementById('select'); 
```

Enter fullscreen mode Exit fullscreen mode

返回一个承诺，所以让我们写一个函数来接收承诺的结果。该函数将媒体设备列表作为参数。

首先要做的是清空任何现有选项的`<select>`并追加一个空的`<option>`。然后我们在设备间循环，过滤掉任何不属于`kind`“视频输入”的设备。然后我们创建一个`<option>`，使用设备的 ID 作为值，设备的标签作为文本。我们还通过生成一个简单的“Camera n”标签来处理设备不报告标签的情况。

```
const video = document.getElementById('video');
const button = document.getElementById('button');
const select = document.getElementById('select');

function gotDevices(mediaDevices) {
  select.innerHTML = '';
  select.appendChild(document.createElement('option'));
  let count = 1;
  mediaDevices.forEach(mediaDevice => {
    if (mediaDevice.kind === 'videoinput') {
      const option = document.createElement('option');
      option.value = mediaDevice.deviceId;
      const label = mediaDevice.label || `Camera ${count  }`;
      const textNode = document.createTextNode(label);
      option.appendChild(textNode);
      select.appendChild(option);
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

在`app.js`结束时，调用`enumerateDevices`。

```
navigator.mediaDevices.enumerateDevices().then(gotDevices); 
```

Enter fullscreen mode Exit fullscreen mode

刷新页面，看看按钮旁边的下拉选择。如果你用的是安卓系统，或者使用 Chrome 或 Firefox，你会看到可用相机的名称。

然而，在 iPhone 上，你会从我们的功能中看到一般命名为“相机 1”和“相机 2”的项目。在 iOS 上，除非您授予网站使用至少一台摄像机的权限，否则您将无法获得摄像机的标签。这使得我们的界面在选择相机时用处不大，因为即使你得到了设备的 ID，你也不能分辨哪个是哪个相机。

[![On the iPhone you only see the labels we made up, 'Camera 1' and 'Camera 2'.](img/52160cae2bba98e83af7a1507933f526.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YasESyMq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2018/04/lkBjDsPZk87_aN3ONBWXs22xmkmQg_plaua_hNfWTFRxQFiieHyB3EXxZwn5zncsEAKKVdONDx3drBnlcYJDU3niHGu1naOEdujZHLWvUE4aRt4Fro-K-Km1diPqUZGM-5KkE.png)

我们还没有连接下拉选择来改变摄像机。在此之前，让我们看看另一种方式，我们可以影响我们想要选择的相机。

## 面向模式

我们可以用来选择摄像机的另一种方法是 [`facingMode`](https://developer.mozilla.org/en-US/docs/Web/API/MediaTrackConstraints/facingMode) 约束。与从`enumerateDevices`函数中获取 ID 相比，这是一种不太精确的选择相机的方式，但是对于移动设备来说非常有效。有四个选项可用于约束:`user`、`environment`、`left`和`right`。MDN 文档中解释了[约束，出于本文的目的，我们将使用`user`和`environment`，因为它们很好地映射到移动设备上的前置和后置摄像头。](https://developer.mozilla.org/en-US/docs/Web/API/MediaTrackConstraints/facingMode)

为了使用`facingMode`约束，我们需要改变在对`getUserMedia`的调用中使用的`constraints`。我们需要这些约束的一个对象，而不仅仅是对`video`说`true`。更新代码以选择前置摄像头，如下所示:

```
button.addEventListener('click', event => {
  const videoConstraints = {
    facingMode: 'user'
  };
  const constraints = {
    video: videoConstraints,
    audio: false
  }; 
```

Enter fullscreen mode Exit fullscreen mode

立即从您的移动设备进行测试。你应该会发现前面的摄像头被选中。将`facingMode`更新为`environment`，然后重试。现在应该选择了后置摄像头。
一旦我们获得了读取摄像机数据的许可，让我们将这段代码与从上面的`enumerateDevices`中得到的结果放在一起，构建一个摄像机切换器。

## 切换摄像机

我们有代码在第一次选择时选择一个用户或环境摄像机，但是如果我们想切换摄像机，还有一点工作要做。

首先，我们应该保留对当前流的引用，以便在切换到另一个流时可以停止它。再添加一个变量和一个实用函数来停止流中的曲目到`app.js`的顶部。

```
const video = document.getElementById('video');
const button = document.getElementById('button');
const select = document.getElementById('select');
let currentStream;

function stopMediaTracks(stream) {
  stream.getTracks().forEach(track => {
    track.stop();
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

函数`stopMediaTracks`获取一个流，并循环通过流中的每个媒体轨道，停止它们中的每一个。

当我们按下同一个按钮时，我们将改变摄像机，所以我们需要更新事件监听器。首先，如果我们有一个`currentStream`，那么我们应该停止它。然后，我们将检查`<select>`以查看我们是否选择了特定的设备，并基于此建立视频约束。

像这样更新按钮的点击处理程序和视频约束:

```
button.addEventListener('click', event => {
  if (typeof currentStream !== 'undefined') {
    stopMediaTracks(currentStream);
  }
  const videoConstraints = {};
  if (select.value === '') {
    videoConstraints.facingMode = 'environment';
  } else {
    videoConstraints.deviceId = { exact: select.value };
  }
  const constraints = {
    video: videoConstraints,
    audio: false
  }; 
```

Enter fullscreen mode Exit fullscreen mode

当我们想通过设备 Id 选择一个设备时，我们使用`exact`约束。我们避免使用`facingMode`约束，因为这可能会在不识别“用户”或“环境”模式的设备上失败，让我们没有任何媒体。

仍然在点击处理程序中，当我们获得使用视频的许可时，我们将改变更多的东西。将`currentStream`设置为传递给该函数的新流，以便我们稍后可以停止它，并启动对`enumerateDevices`的另一个调用。

`enumerateDevices`返回一个承诺，所以我们可以从我们的`then`函数返回它，并为结果链接一个新的`then`，然后由我们的`gotDevices`函数处理。

将您对`getUserMedia`的现有呼叫替换为:

```
button.addEventListener('click', event => {
  if (typeof currentStream !== 'undefined') {
    stopMediaTracks(currentStream);
  }
  const videoConstraints = {};
  if (select.value === '') {
    videoConstraints.facingMode = 'environment';
  } else {
    videoConstraints.deviceId = { exact: select.value };
  }
  const constraints = {
    video: videoConstraints,
    audio: false
  };

  navigator.mediaDevices
    .getUserMedia(constraints)
    .then(stream => {
      currentStream = stream;
      video.srcObject = stream;
      return navigator.mediaDevices.enumerateDevices();
    })
    .then(gotDevices)
    .catch(error => {
      console.error(error);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

当你添加完所有的代码后，你的`app.js`应该看起来像[这样就完成了一个](https://github.com/philnash/mediadevices-camera-selection/blob/master/app.js)。刷新页面，你可以选择和改变相机。这在手机和桌面上都适用。

[![The finished result, this is an animation showing that you can select one camera then change and go from viewing the back camera to the front camera.](img/d9a8391e9410dda7b09f9e1629506174.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AKF-v4yJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2018/04/bKT1LdWTyA_RANLksFX5srORt6RRgCmVDSWoJj8Pzf3OSNkhfAi0_KWssgerqSaV-_Ty9WRKI8p8cQ-xFJd5nPFEyYQ70OS8nTefBpLrle54e3Tm1pKaJQX40BM-zgqLhTK9haI.png)

## 下一步

我们已经看到了如何使用`facingMode`或`deviceId`约束来选择用户的摄像机。记住，`facingMode`更可靠，在你有权限使用相机之前，但选择一个`deviceId`更准确。你可以从 GitHub repo 中获得[这篇博客文章的所有代码，你也可以在这里](https://github.com/philnash/mediadevices-camera-selection)试用这个应用程序。

如果您正在使用 [Twilio Video](https://www.twilio.com/docs/api/video) 构建一个视频应用程序，您可以在调用 [`connect`](https://media.twiliocdn.com/sdk/js/video/releases/1.8.0/docs/global.html#connect) 或 [`createLocalVideoTrack`](https://media.twiliocdn.com/sdk/js/video/releases/1.8.0/docs/global.html#createLocalVideoTrack) 时使用这些约束。

选择或切换摄像头是视频聊天的一个有用功能，允许用户在你的应用程序界面中选择他们想要使用的确切摄像头，并且它可以在视频通话期间与[共享你的屏幕一起进行](https://www.twilio.com/blog/2018/01/screen-sharing-twilio-video.html)。

在视频聊天中，你还想看到其他有用的视频功能吗？或者对这个功能有什么疑问？请在评论或 Twitter 上告诉我，地址是 [@philnash](https://twitter.com/philnash) 。

* * *

*[用 mediaDevices API 用 JavaScript 选择相机](https://www.twilio.com/blog/2018/04/choosing-cameras-javascript-mediadevices-api.html)最初发表于 2018 年 4 月 19 日 [Twilio 博客](https://www.twilio.com/blog)。*