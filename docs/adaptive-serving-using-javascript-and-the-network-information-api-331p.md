# 使用 JavaScript 和网络信息 API 的自适应服务

> 原文：<https://dev.to/addyosmani/adaptive-serving-using-javascript-and-the-network-information-api-331p>

**`navigator.connection.effectiveType`用于根据用户网络连接的质量交付不同的资产。**

[![](../Images/33c568f18e1cf89d5f75cdd4714779ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ktkd6j7d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4z66d75fid8fje27lp2y.png)

[effectiveType](https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation/effectiveType) 是[网络信息 API](http://w3c.github.io/netinfo/) 的属性，通过 [navigator.connection](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/connection) 对象暴露给 JavaScript。在 Chrome 中，您可以将以下内容拖放到 DevTools 中，以查看您的有效连接类型(ECT):

```
console.log(navigator.connection.effectiveType); // 4G 
```

`effectiveType`的可能值为“慢速 2g”、“2g”、“3g”或“4g”。在慢速连接上，此功能允许您通过提供低质量版本的资源来提高页面加载速度。

在 Chrome 62 之前，我们只向开发者暴露了理论上的网络连接类型(通过`navigator.connection.type`)，而不是客户端实际体验到的网络质量。

Chrome 的有效连接类型实现现在使用最近观察到的往返时间(rtt)和下行链路值的组合来确定。

它将测得的网络性能总结为最相似的蜂窝连接类型(例如 2G)，即使实际连接是 WiFi。例如，想象你在星巴克的无线网络上，但是你的实际有效网络类型是 2G 或 3G。

[![](../Images/aa22099ed8f4e1129cc99cffe4ac86a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T54UF-7H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wqeuhx12frs3k126bmrv.png)

如何应对网络质量的变化？我们可以使用`connection.onchange`事件监听器来监控连接变化:

```
 function onConnectionChange() {
    const { rtt, downlink, effectiveType,  saveData } = navigator.connection;

    console.log(`Effective network connection type: ${effectiveType}`);
    console.log(`Downlink Speed/bandwidth estimate: ${downlink}Mb/s`);
    console.log(`Round-trip time estimate: ${rtt}ms`);
    console.log(`Data-saver mode on/requested: ${saveData}`);
}

navigator.connection.addEventListener('change', onConnectionChange) 
```

下面是一个快速测试，我在 DevTools 中模拟了一个“低端移动”配置文件，并能够从“4g”切换到“2g”条件:

[![](../Images/a7f248a57c29edc81ff6c8f0efd33654.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gdIz0VyD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t9zadl65erjhll14zbcp.png)

Android 上的 Chrome、Opera 和 Firefox 都支持。在`navigator.connection`上还有其他一些网络质量提示，包括`rtt`、`downlink`和`downlinkMax`。

我用过的一个开源项目是 Vue.js [谷歌涂鸦](https://oodle-demo.firebaseapp.com)应用。使用数据绑定，我们能够根据 ECT 值将一个`connection`属性设置为`fast`或`slow`。大致是:

```
if (/\slow-2g|2g|3g/.test(navigator.connection.effectiveType)) {
  this.connection = "slow";
} else {
  this.connection = "fast";
} 
```

这允许我们根据用户的有效连接类型有条件地呈现不同的输出(视频和低分辨率图像)。

```
 <template>
      <div id="home">
        <div v-if="connection === 'fast'">
          <!-- 1.3MB video -->
          <video class="theatre" autoplay muted playsinline control>
            <source src="/static/img/doodle-theatre.webm" type="video/webm">
            <source src="/static/img/doodle-theatre.mp4" type="video/mp4">
          </video>
        </div>
        <!-- 28KB image -->
        <div v-if="connection === 'slow'">
          <img class="theatre" src="/static/img/doodle-theatre-poster.jpg">
        </div>
      </div>
   </template> 
```

[![](../Images/e07a043316930baf7cd95853cc184ea8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_tvmKtK---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8jukzhdu62nbghw0cfx3.png)

max bck 写了一篇关于使用 React 的[网络感知组件](https://mxb.at/blog/connection-aware-components/)的有趣文章。他同样强调了如何根据网络速度渲染不同的组件:

```
 switch(connectionType) {
            case '4g':
                return <Video src={videoSrc} /> 
            case '3g':
                return <Image src={imageSrc.hires} alt={alt} /> 
            default:
                return <Image src={imageSrc.lowres} alt={alt} />
        } 
```

注意:除了较慢的有效连接类型外，您可以将`effectiveType`与服务人员配对，以适应用户离线时的情况。

对于调试，您可以使用 Chrome 标志“force-effective-connection-type”来覆盖网络质量估计，该标志可以在 chrome://flags 中设置。DevTools 网络仿真也可以为 ECT 提供有限的调试体验。

`effectiveType`值也通过[客户端提示](https://www.chromestatus.com/features/5407907378102272)公开，允许开发者向服务器传达 Chrome 的网络连接速度。

有关此功能的更多信息，请参见:

*   [谷歌 Chrome 网络信息 API 示例](https://googlechrome.github.io/samples/network-information/)
*   [连接感知组件](https://mxb.at/blog/connection-aware-components/)
*   [使用 NetInfo API 和服务工作者的动态资源](https://deanhume.com/dynamic-resources-using-the-network-information-api-and-service-workers/)
*   [基于网络的图像加载](https://justmarkup.com/log/2017/11/network-based-image-loading/)
*   [Chrome 62 Beta:网络质量评估 API](https://blog.chromium.org/2017/09/chrome-62-beta-network-quality.html)
*   [意向发货:针对网络质量的 NetInfo API 扩展](https://groups.google.com/a/chromium.org/forum/#!topic/blink-dev/UVfNMH50aaQ)

你也可以在[addyosmani.com](https://addyosmani.com/blog/adaptive-serving/)上找到这个帖子