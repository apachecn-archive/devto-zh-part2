# 使用网络音频 API 和 React 实现音频可视化

> 原文：<https://dev.to/twilio/audio-visualisation-with-the-web-audio-api-and-react-k20>

网络音频 API 是一个强大的浏览器 API，用于创建、处理和分析音频。我不是音乐家，所以我会把创作和操作留给专家。相反，在这篇文章中，我们将着眼于分析音频。为了让事情变得更加有趣，我们将看看如何用`<canvas>`可视化 React 组件中的音频。

当我们完成时，我们将有一个 React 应用程序，它可以监听您计算机上的麦克风，并显示波形来可视化数据。

## 入门

我们将需要 [Node.js](https://nodejs.org/en/) 来运行应用程序，所以请确保您已经安装了它。我已经[为我们启动了应用程序](https://github.com/philnash/react-web-audio)，所以按照下面的说明让它运行起来:

1.  从 GitHub 克隆或下载 repo

    ```
    git clone -b getting-started https://github.com/philnash/react-web-audio.git 
    ```

2.  转到目录并安装与 npm 的依赖关系

    ```
    cd react-web-audio
    npm install 
    ```

3.  启动应用程序

    ```
    npm start 
    ```

4.  访问 [localhost:3000](http://localhost:3000/) 查看应用程序运行

[![](../Images/453ad20283f904cb4853614ad1e1fa94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jRIsSVEI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-docs/images/p9kbBeeH2nLtD0_h6-sgBGZxn3sZ4vrHOC8JCOARzyEWf3.width-500.png)

我们准备开始构建应用程序。

## 麦克风输入

界面中有一个按钮可以抓取麦克风输入，但它还没有连接到任何东西。我们将创建一个按钮被点击时执行的函数。我们的函数将使用 [`getUserMedia` API](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia) 请求访问用户麦克风的许可。然后，一旦许可被授予，它将音频添加到应用程序的状态。

首先，打开`src/App.js`并在`App`组件的构造函数中设置状态对象。

```
class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      audio: null
    };
  } 
```

我们需要一个方法，将使用`getUserMedia`请求访问麦克风，并设置音频流的状态，如果它是成功的。将以下内容添加到组件中:

```
 async getMicrophone() {
    const audio = await navigator.mediaDevices.getUserMedia({
      audio: true,
      video: false
    });
    this.setState({ audio });
  } 
```

添加一个方法来停止音频捕获。这将循环遍历与`getUserMedia`返回的`MediaStream`相关联的每个`MediaTrack`，并停止它们，最终将流从状态中移除。

```
 stopMicrophone() {
    this.state.audio.getTracks().forEach(track => track.stop());
    this.setState({ audio: null });
  } 
```

添加打开和关闭麦克风的方法。

```
 toggleMicrophone() {
    if (this.state.audio) {
      this.stopMicrophone();
    } else {
      this.getMicrophone();
    }
  } 
```

我们将对界面中的按钮使用这种切换方法。为此，我们需要将其上下文绑定到组件。将以下内容添加到构造函数中:

```
 constructor(props) {
    super(props);
    this.state = {
      audio: null
    };
    this.toggleMicrophone = this.toggleMicrophone.bind(this);
  } 
```

该应用程序已经呈现了一个按钮，但我们需要将它与这些函数挂钩。更新`render`功能，使按钮在获取和停止麦克风输入之间切换。

```
 render() {
    return (
      <div className="App">
        <main>
          <div className="controls">
            <button onClick={this.toggleMicrophone}>
              {this.state.audio ? 'Stop microphone' : 'Get microphone input'}
            </button>
          </div>
        </main>
      </div>
    );
  } 
```

返回浏览器，点击“获取麦克风输入”按钮。浏览器会询问你是否允许使用麦克风，当你同意时，按钮会改变。不太令人兴奋，但现在我们有了音频流，我们正在分析它。

## 音频分析

在`src`目录中为分析创建一个新组件；称之为`AudioAnalyser.js`。我们将通过`props`将音频流传递给这个组件。该组件将负责使用 Web 音频 API 来分析音频流，并将分析结果存储在状态中。

我们将从一个空组件开始:

```
import React, { Component } from 'react';

class AudioAnalyser extends Component {

}

export default AudioAnalyser; 
```

当组件挂载后，我们将设置 Web 音频 API 对象。首先我们创建一个新的 [`AudioContext`](https://developer.mozilla.org/en-US/docs/Web/API/AudioContext) (遗憾的是，Safari 仍然只支持 webkit 前缀版本)。然后我们将创建一个 [`AnalyserNode`](https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode) 来为我们完成繁重的工作。

从`AnalyserNode`我们需要知道 [`frequencyBinCount`](https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode/frequencyBinCount) ，根据文档，它通常等于可用于可视化的数据值的数量。我们将创建一个 8 位无符号整数数组，一个`Uint8Array`，长度为`frequencyBinCount`。该`dataArray`将用于存储`AnalyserNode`将创建的波形数据。

我们将来自麦克风的媒体流作为道具传递到组件中，我们需要将它转换为 Web Audio API 的源。为此，在`AudioContext`对象上调用`createMediaStreamSource`，在流中传递。一旦我们有了源，我们就可以连接分析仪。

```
 componentDidMount() {
    this.audioContext = new (window.AudioContext ||
      window.webkitAudioContext)();
    this.analyser = this.audioContext.createAnalyser();
    this.dataArray = new Uint8Array(this.analyser.frequencyBinCount);
    this.source = this.audioContext.createMediaStreamSource(this.props.audio);
    this.source.connect(this.analyser);
  } 
```

这仍然没有为我们做任何分析。为此，每次我们想要更新可视化时，我们都需要调用`AnalyserNode`的`getByteTimeDomainData`方法。因为我们将制作这个可视化动画，所以每当我们想要更新可视化时，我们将调用浏览器的`requestAnimationFrame` API 从`AnalyserNode`中提取最新的音频数据。

为此，我们将创建一个每次运行`requestAnimationFrame`时都会被调用的方法。该函数将当前波形作为整数数组从`AnalyserNode`复制到`dataArray`。然后它会用`dataArray`更新组件状态中的`audioData`属性。最后，它将再次调用`requestAnimationFrame`来请求下一次更新。

```
 tick() {
    this.analyser.getByteTimeDomainData(this.dataArray);
    this.setState({ audioData: this.dataArray });
    this.rafId = requestAnimationFrame(this.tick);
  } 
```

在我们将源连接到分析器之后，我们从`componentDidMount`方法的结尾开始动画循环。

```
 componentDidMount() {
    // rest of the function

    this.source.connect(this.analyser);
    this.rafId = requestAnimationFrame(this.tick);
  } 
```

我们将在构造函数中初始化组件的状态，用一个空的`Uint8Array`并将`tick`函数的范围绑定到组件。

```
 constructor(props) {
    super(props);
    this.state = { audioData: new Uint8Array(0) };
    this.tick = this.tick.bind(this);
  } 
```

我们想做的另一件事是，如果我们移除组件，就释放所有的资源。创建一个`componentWillUnmount`方法，取消动画帧并断开音频节点。

```
 componentWillUnmount() {
    cancelAnimationFrame(this.rafId);
    this.analyser.disconnect();
    this.source.disconnect();
  } 
```

我们尚未从该组件呈现任何内容。我们可以看看我们正在生成的数据。使用以下代码向组件添加一个`render`方法:

```
 render() {
    return <textarea value={this.state.audioData} />;
  } 
```

打开`src/App.js`，导入`AudioAnalyser`组件:

```
import React, { Component } from 'react';
import AudioAnalyser from './AudioAnalyser'; 
```

仅当状态包含音频流时，在`render`函数中包含`<AudioAnalyser>`组件。

```
 render() {
    return (
      <div className="App">
        <div className="controls">
          <button onClick={this.toggleMicrophone}>
            {this.state.audio ? 'Stop microphone' : 'Get microphone input'}
          </button>
        </div>
        {this.state.audio ? <AudioAnalyser audio={this.state.audio} /> : ''}
      </div>
    );
  } 
```

加载页面并按下按钮以访问您的麦克风，您将看到在`<textarea>`中生成的值数组。然而，查看一堆更新的数字并不有趣，所以让我们添加一个新的组件来可视化这些数据。

## 音频可视化

创建一个名为`AudioVisualiser.js`的组件，并填入我们需要的样板文件。

```
import React, { Component } from 'react';

class AudioVisualiser extends Component {

}

export default AudioVisualiser; 
```

让我们用`render`方法开始这个类。我们想要绘制到一个`<canvas>`上，所以我们将在页面上呈现一个。

```
 render() {
    return <canvas width="300" height="300" />;
  } 
```

我们需要得到一个对`<canvas>`元素的引用，这样我们以后就可以利用它了。在构造函数中，使用 [`React.createRef()`](https://reactjs.org/docs/refs-and-the-dom.html) 创建引用，并将`ref`属性添加到`<canvas>`元素中。

```
 constructor(props) {
    super(props);
    this.canvas = React.createRef();
  }
  render() {
    return <canvas width="300" height="300" ref={this.canvas} />;
  } 
```

让我们构建一个将在画布上绘制波形的函数。这与最初由[索莱达·佩纳代斯](https://soledadpenades.com/)编写的[MDN 教程](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API/Visualizations_with_Web_Audio_API#Creating_a_waveformoscilloscope)相似，但是经过修改后可以在 React 组件内工作。

这个想法是用我们在前一个组件中创建的`audioData`,在数组中的每个数据点之间从左到右画一条线。

从一个名为`draw`的新函数开始。每次我们从分析器获得新数据时，都会调用这个函数。我们首先设置我们想要使用的变量:

*   从`props`到`audioData`及其长度
*   来自`ref`的画布
*   画布的高度和宽度
*   来自画布的 2d 绘图上下文
*   `x`这将被用来跟踪整个画布
*   `sliceWidth`，每次绘制时我们将向右移动的量

```
 draw() {
    const { audioData } = this.props;
    const canvas = this.canvas.current;
    const height = canvas.height;
    const width = canvas.width;
    const context = canvas.getContext('2d');
    let x = 0;
    const sliceWidth = (width * 1.0) / audioData.length; 
```

现在我们开始构建我们将要在画布上绘制的图片。首先设置我们的绘画风格，在这种情况下设置线条宽度为 2 和中风的颜色为黑色。然后我们从画布上清除以前的绘图。

```
 context.lineWidth = 2;    context.strokeStyle = '#000000';    
    context.clearRect(0, 0, width, height); 
```

接下来，开始我们将要绘制的路径，并将绘制位置移动到画布左侧的中间。

```
 context.beginPath();
    context.moveTo(0, height / 2); 
```

在`audioData`中循环数据。每个数据点都在 0 到 255 之间。为了使我们的画布标准化，我们除以 255，然后乘以画布的高度。然后，我们从前一点到这一点画一条线，并通过`sliceWidth`增加`x`。

```
 for (const item of audioData) {
      const y = (item / 255.0) * height;
      context.lineTo(x, y);
      x += sliceWidth;
    } 
```

最后，我们在画布右侧的中间画一条线，引导画布给整个路径上色。

```
 context.lineTo(x, height / 2);
    context.stroke();
  } 
```

每次更新`audioData`时都需要运行`draw`函数。向组件添加以下函数:

```
 componentDidUpdate() {
    this.draw();
  } 
```

让我们把所有的组件绑在一起，看看观想者的行动。

## 将组件集合在一起

打开`src/AudioAnalyser.js`并导入`AudioVisualiser`组件。

```
import React, { Component } from 'react';
import AudioVisualiser from './AudioVisualiser'; 
```

定义一个`render`方法，该方法呈现`<AudioVisualiser>`并将来自状态的`audioData`作为属性传递。

```
 render() {
    return <AudioVisualiser audioData={this.state.audioData} />;
  } 
```

我们结束了。用`npm start`启动应用程序，如果它不再运行，再次打开浏览器到 [localhost:3000](http://localhost:3000) 。点击按钮，制造一些噪音，然后观看观想者变得栩栩如生。

[![](../Images/6624a358fa6a947aee200e7f62ebedbc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9q5V7dF_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-docs/images/f45ncza6SRuSXhp7iE4XODmWWhy1_uqWUTbJy9Mg1uB8Eo.width-500.png)

## 下一步

在这篇文章中，我们看到了如何访问麦克风，设置 Web Audio API 来分析音频并将其可视化在画布上，在两个 React 组件之间划分工作。

我们可以以此为基础，创造更有趣、更有创意的观想。或者，如果你正在 React 中[创建视频聊天，你可以添加这种可视化来显示谁在通话中制造噪音，或者甚至检查你自己的麦克风是否工作。](https://www.twilio.com/blog/2018/03/video-chat-react.html)

你可以在 GitHub 上查看[这个应用的所有代码。我建议通读一下](https://github.com/philnash/react-web-audio) [MDN 关于用网络音频 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API/Visualizations_with_Web_Audio_API) 实现可视化的文章，看看你还可以用它实现什么。我很想看看你还能创造出什么样的视觉效果，如果你想出了什么，请在评论中或者在推特上告诉我，地址是 [@philnash](https://twitter.com/philnash) 。