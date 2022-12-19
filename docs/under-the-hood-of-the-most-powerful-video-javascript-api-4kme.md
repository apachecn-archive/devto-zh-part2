# 在最强大的视频 JavaScript API 下

> 原文：<https://dev.to/jwplayer/under-the-hood-of-the-most-powerful-video-javascript-api-4kme>

## 介绍

在 JW Player，我们相信我们的技术能够让开发者在他们的网站上提供最好的视频体验。从个人开发者到 Vice、Business Insider 和 Amazon Web Services 等企业，我们为每个人提供了合适的解决方案。 [Dev.to](https://dev.to/vaidehijoshi/stacks-and-queues--basecs-video-series--20oj) 好像也是这么想的(右击视频播放器)！

在本文中，我们将通过代码而不是口头说说来证明这一点。目标是通过代码演练和演示，展示如何利用我们的播放器 JavaScript API 在您的网站上提供更好的视频体验。然后，我们将总结 JW Player 的一些细节，解释*我们如何*成为网络上最快的玩家。事不宜迟:

## 一个健壮、强大的视频 JavaScript API

在 JW Player，我们的使命是为开发人员提供最强大的 JavaScript API，让您能够完全控制您的视频体验。这包括从高级玩家定制到分析报告的功能。

与开源解决方案相比，除了代码演示之外，我们还致力于提供强大的文档以及专门的世界级支持团队，以确保开发和实施轻而易举。您的时间是宝贵的，我们的开发人员和支持网站可以确保您在 StackOverflow 中花费更少的时间。

以下是您可以做些什么的示例:

1.  [视频墙](https://developer.jwplayer.com/jw-player/demos/innovation/click-to-play/?utm_source=dev.to&utm_medium=blog)
2.  [360 度视频& VR](https://developer.jwplayer.com/jw-player/demos/innovation/360-video/?utm_source=dev.to&utm_medium=blog)
3.  [视频背景](https://developer.jwplayer.com/jw-player/demos/developer-showcase/video-background/?utm_source=dev.to&utm_medium=blog)
4.  [自定义控制图标](https://developer.jwplayer.com/jw-player/demos/customization/custom-icons/?utm_source=dev.to&utm_medium=blog)
5.  [隐藏字幕](https://developer.jwplayer.com/jw-player/demos/toolbox/closed-captions/?utm_source=dev.to&utm_medium=blog) & [字幕样式](https://developer.jwplayer.com/jw-player/demos/customization/captions-styling/?utm_source=dev.to&utm_medium=blog)

让我们来看一下第一个演示——创建一个点击播放的视频墙。这个视频墙的目的是以电影的形式显示内容，而不会降低网页的速度。

在本演示中，您可以设置一个响应视频网格，仅从视频中提取海报图像，以确保通过单击缩略图加载视频播放器来快速生成第一帧。当观众四处点击时，它也会暂停原始视频。

你可以在为观众提供最佳 UX 的同时创造一种电影体验，而不是一次加载几个播放器，这在移动设备上是很痛苦的。

只需单击响应图像网格中的缩略图，即可设置和播放视频，如下所示:

[https://codepen.io/kim_hart/embed/aKOVQN%20?height=500&default-tab=result&embed-version=2](https://codepen.io/kim_hart/embed/aKOVQN%20?height=500&default-tab=result&embed-version=2)

```
 // Request playlist data
  (function() {
    var httpRequest = new XMLHttpRequest();
    if (!httpRequest) {
      return false;
    }
    httpRequest.onreadystatechange = function() {
      if (httpRequest.readyState === XMLHttpRequest.DONE) {
        if (httpRequest.status === 200) {
          var json = JSON.parse(httpRequest.response);
          getThumbnails(json);
        } else {
          console.log(httpRequest.statusText);
        }
      }
    }
    httpRequest.open('GET', '//cdn.jwplayer.com/v2/playlists/0FDAGB12');
    httpRequest.send();
  })();

  // Render thumbnails into grid layout
  var thumbs = document.querySelectorAll('.thumb');
  var player;

  function getThumbnails(data) {
    var playlist = data.playlist;

    thumbs.forEach(function(thumb, i) {
      var video = playlist[i];
      var titleText = document.createElement('div');

      titleText.className = 'title-text';
      titleText.innerHTML = video.title;
      thumb.appendChild(titleText);
      thumb.setAttribute('id', video.mediaid + 1);
      thumb.style.backgroundImage = "url('" + video.image + "')";

      thumb.addEventListener('click', function(e) {
        handleActivePlayer(e, video);
      });
    })
  };

  // On click, destroy existing player, setup new player in target div
  function handleActivePlayer(e, video) {
    var activeDiv = e.target;
    if (player) {
      player.remove();
    }
    thumbs.forEach(function(thumb) {
      thumb.classList.remove('active');
    })
    activeDiv.classList.add('active');

    // Chain .play() onto player setup (rather than autostart: true)
    player = jwplayer(activeDiv.id).setup({
      file: '//content.jwplatform.com/manifests/' + video.mediaid + '.m3u8'
    }).play();

    // Destroy the player and replace with thumbnail
    player.on('complete', function() {
      player.remove();
      player = null;
    });
  } 
```

## 我们如何构建网络上最快的视频播放器

当考虑视频播放器的用户体验时，第一帧的时间是影响视频可视性的最显著的因素。

Akamai 的一项研究发现，超过两秒钟的加载时间每增加一秒钟，T2 的视频废弃率就会增加 6%。一项[针对 OTT 收视率的独立研究](http://www.fiercecable.com/online-video/akamai-ott-video-streaming-quality-experience-drives-viewer-loyalty-provider-success)显示**缓冲会增加 16%的负面情绪，减少 20%的参与度**。这两项研究强烈表明，糟糕的回放是视频参与的最大抑制因素。

这就是为什么我们确保我们的播放器在所有设备和浏览器上都有**亚秒级的加载时间**，这样终端观众就永远不会看到缓冲屏幕。

怎么会？

1.  我们的播放器检测查看者的渲染环境，**只加载回放**所需的必要组件。基于播放列表中包含的媒体类型和观众浏览器的组合，我们对播放器进行了优化，以减少最常见的视频播放用例的网络请求，从而降低与设置时间相关的延迟成本。
2.  我们的嵌入脚本被设计成**发出更少的服务器请求**，以更好地与现代网页的整体构成进行交互。通过实现我们最新版本的网页播放器，你可以放心 JW 播放器是**积极减少其足迹，以改善您的整个网站体验。**
3.  我们的视频预加载在播放前和页面加载后立即获取媒体数据，这使得观众可以享受更快的播放速度和更低的带宽。具体来说，我们的后端预加载过程更智能，更精确地知道预加载的时间和量。我们还采取措施来优化在一个页面上加载多个视频播放器的网站的带宽消耗，只在播放器的可观看性超过 50%时才预加载播放器。
4.  默认情况下，播放器被设置为加载元数据，因此一旦启动播放，即点即用播放器就会立即开始播放。重申一下，这些预加载的改变让玩家变得更加聪明，避免浪费观众带宽，同时提高开始时间。
5.  最后，如果终端观众可以支持更高质量的视频流，我们的播放器**不会损害视频质量**。播放器保持观看者在视频之间的带宽，允许第二个和后续视频从一开始就受益于更高质量的开始。如果观众正在观看播放器嵌入的第一个视频，当观众返回到同一设备和浏览器上的站点时，播放器可以在最后已知的带宽上启动。

要了解幕后发生了什么，请查看我们开发者网站上的[玩家事件检查器](https://developer.jwplayer.com/tools/player-event-inspector/?utm_source=dev.to&utm_medium=blog)。在这里，您可以测试和调试 JW 播放器设置，并返回所有可用的 JW 播放器事件、getters 和 utils。

要了解更多信息，您还可以查看我们全面的[配置参考文档](https://developer.jwplayer.com/jw-player/docs/developer-guide/customization/configuration-reference/?utm_source=dev.to&utm_medium=blog)。

## 结论

我们创建了最强大、最灵活的视频 Javascript API，因此您可以根据自己的标准提供出色的视频体验。我们的团队维护播放器，以确保完整的设备和浏览器支持，使您始终处于最新状态—加上工具、演示和强大的 API 文档，使您可以专注于重要的事情。

要了解更多信息，[比较选项](https://www.jwplayer.com/plans/?utm_source=dev.to&utm_medium=blog)，看看哪个计划适合您。