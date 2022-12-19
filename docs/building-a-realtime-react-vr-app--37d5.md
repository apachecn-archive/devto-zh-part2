# 构建实时 React VR 应用程序

> 原文：<https://dev.to/eh3rrera/building-a-realtime-react-vr-app--37d5>

React VR 是一个库，允许你使用 JavaScript 为 web 编写虚拟现实应用，并在 [WebVR API](https://webvr.info) 之上进行 React。现在 Chrome、Firefox 和 Edge 等浏览器的最新(或在某些情况下是实验性的)版本都支持这一规范，在浏览器中访问 VR 不需要戴耳机。

WebVR 实验是一个展示一些项目的网站，这些项目向你展示了 WebVR 的可能性。一个引起我注意的项目是[音乐森林](https://forest.webvrexperiments.com/)，由谷歌创意实验室的优秀人员使用 [A-Frame](https://aframe.io/) 制作，这是由 [Mozilla VR 团队](https://vr.mozilla.org/)开发的网络虚拟现实的网络框架。

在音乐森林中，点击一个形状会触发一个音符，使用 WebSockets，人们可以一起实时播放音乐。然而，由于使用的所有功能和技术，该应用程序有些复杂(你可以在这里找到源代码[)。那么，为什么不用 React VR 和 Pusher 来创建它的简化版呢？](https://github.com/googlecreativelab/webvr-musicalforest)

下面是 React VR/Pusher 版本的外观:

[https://www.youtube.com/embed/vm5vKh7h0b4](https://www.youtube.com/embed/vm5vKh7h0b4)

用户可以在 URL 中输入频道标识符。当点击 3D 形状时，将播放声音并发布推送事件，以便同一频道的其他用户可以接收该事件，应用程序也可以播放该声音。

我们将有一个 Node.js 后端来发布事件，所以您应该熟悉 JavaScript 和 React。如果你不太熟悉一些 VR 概念或 React VR，这里有一个[指南](https://www.pluralsight.com/guides/front-end-javascript/getting-started-with-react-vr)让你开始。

作为参考(或者你只是想试试 app 的话)，可以在这里找到 React VR 项目[，在这里](https://github.com/eh3rrera/reactvr_musical)找到 Node.js 后端[。](https://github.com/eh3rrera/server_pusher_reactvr_musical)

## 设置 React VR 项目

让我们从安装(或更新)React VR CLI 工具开始:

```
npm install -g react-vr-cli 
```

接下来，创建一个新的 React VR 项目:

```
react-vr init musical-exp-react-vr-pusher 
```

现在转到它创建的目录，执行以下命令来启动开发服务器:

```
cd musical-exp-react-vr-pusher
npm start 
```

在你的浏览器中，进入[http://localhost:8081/VR/](http://localhost:8081/vr/)。类似下面的东西会显示出来:
[![Hello World no VR button](../Images/1dcb573e9e49b4c2707fd8c988ed1e80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dF7ZEKaR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dl.dropboxusercontent.com/s/gx8cx6m0q0g5gxf/creating-a-musical-experience-with-react-vr-and-pusher-first-no-vr.png)

如果你使用的是兼容的浏览器，你还应该看到用耳机查看应用程序的 VR 按钮:
[![Hello World VR button](../Images/e68c577fe3246e127ef0f881bf11d10e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w_-T7Zkm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dl.dropboxusercontent.com/s/xrq8g51rr7cfevh/creating-a-musical-experience-with-react-vr-and-pusher-first-vr.png)

现在让我们开始编写我们的应用程序。

## 创建背景

我们将使用一个[等矩形图像](https://www.flickr.com/groups/equirectangular/)作为图像背景。这类图像的主要特点是宽度必须正好是高度的两倍，所以打开你最喜欢的图像编辑软件，用你选择的渐变或颜色创建一个大小为 4096x2048 的图像:
[![Gradient](../Images/9c9cc91029fd56c9df79488fdf67c760.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G9Rp_FN9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dl.dropboxusercontent.com/s/v3qbhdalrjc083i/creating-a-musical-experience-with-react-vr-and-pusher-background.png)

在你的应用程序根目录下的`static_assets`目录中创建一个名为`images`的新文件夹，并保存你的图片。现在打开文件`index.vr.js`，用下面的内容替换`render`方法的内容:

```
render() {
  return (
    <View>
      <Pano source={asset('images/background.jpg')} />
    </View>
  );
} 
```

当你重新加载页面时(或者如果你启用了[热重新加载](https://facebook.github.io/react-vr/docs/dev-tools.html#hot-reloading)，你应该会看到这样的内容:
[![Background](../Images/b2a77239805207c40ad151831b89d809.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kM9zN80I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dl.dropboxusercontent.com/s/kyas3gr52x7pqq2/creating-a-musical-experience-with-react-vr-and-pusher-background.gif)

现在，为了模拟一棵树，我们将使用一个[圆柱体](https://facebook.github.io/react-vr/docs/cylinder.html)。事实上，我们需要 100 个这样的东西来在用户周围创建一个森林。在原始音乐森林中，我们可以在[js/components/background-objects . js](https://github.com/googlecreativelab/webvr-musicalforest/blob/master/js/components/background-objects.js)文件中找到生成用户周围树的算法。如果我们将代码改编成项目的 React 组件，我们可以得到这样的结果:

```
import React from 'react';
import {
  View,
  Cylinder,
} from 'react-vr';

export default ({trees, perimeter, colors}) => {
  const DEG2RAD = Math.PI / 180;

  return (
    <View>
      {Array.apply(null, {length: trees}).map((obj, index) => {
          const theta = DEG2RAD * (index / trees) * 360;
          const randomSeed = Math.random();
          const treeDistance = randomSeed * 5 + perimeter;
          const treeColor = Math.floor(randomSeed * 3);
          const x = Math.cos(theta) * treeDistance;
          const z = Math.sin(theta) * treeDistance;

          return (
            <Cylinder
              key={index}
              radiusTop={0.3}
              radiusBottom={0.3}
              dimHeight={10}
              segments={10}
              style={{
                color: colors[treeColor],
                opacity: randomSeed,
                transform: [{scaleY : 2 + Math.random()}, {translate: [x, 3, z]},],
              }}
            />
          );
      })}
    </View>
  );
} 
```

这个功能组件有三个参数:

*   `trees`，表示森林将拥有的树木数量
*   `perimeter`，一个控制距离用户多远的树将被渲染的值
*   `colors`，一个值为[的数组，为树着色](https://facebook.github.io/react-vr/docs/colors.html)。

使用`Array.apply(null, {length: trees})`，我们可以创建一个空值数组，我们可以对其应用 map 函数，在[视图](https://facebook.github.io/react-vr/docs/view.html)组件中用随机颜色、不透明度和位置来渲染一个圆柱体数组。

我们可以将这段代码保存在一个名为`Forest.js`的文件中，该文件位于一个`components`目录中，并在`index.vr.js`中以如下方式使用它:

```
...
import Forest from './components/Forest';

export default class musical_exp_react_vr_pusher extends React.Component {
  render() {
    return (
      <View>
        <Pano source={asset('images/background.jpg')} /> 
        <Forest trees={100} perimeter={15} colors={['#016549', '#87b926', '#b1c96b']} 
        />
      </View>
    );
  }
};

... 
```

在浏览器中，应该会看到这样的内容:
[![Forest](../Images/b3b5513e8989543505c2ffcb64d3334c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rXiZoCJo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dl.dropboxusercontent.com/s/xpt6nah4sqbzr2o/creating-a-musical-experience-with-react-vr-and-pusher-trees.gif)

很好，我们的背景完成了，现在让我们添加 3D 物体来播放声音。

## 创建 3D 形状

我们将有六个 3D 形状，每个形状在被点击时会发出六种不同的声音。此外，当光标进入和退出形状时，一个小动画会派上用场。

为此，我们需要一个 [VrButton](https://facebook.github.io/react-vr/docs/vrbutton.html) ，一个[动画。查看](https://facebook.github.io/react-vr/docs/animated.html)，形状有一个[盒子](https://facebook.github.io/react-vr/docs/box.html)，一个[圆柱体](https://facebook.github.io/react-vr/docs/cylinder.html)，一个[球体](https://facebook.github.io/react-vr/docs/sphere.html)。然而，由于每个形状都是不同的，所以让我们将相同的部分封装在一个组件中。将以下代码保存在文件`components/SoundShape.js`中:

```
import React from 'react';
import {
  VrButton,
  Animated,
} from 'react-vr';

export default class SoundShape extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      bounceValue: new Animated.Value(0),
    };
  }

  animateEnter() {
    Animated.spring(  
      this.state.bounceValue, 
      {
        toValue: 1, 
        friction: 4, 
      }
    ).start(); 
  }

  animateExit() {
    Animated.timing(
      this.state.bounceValue,
      {
        toValue: 0,
        duration: 50,
      }
    ).start();
  }

  render() {
    return (
      <Animated.View
        style={{
          transform: [
            {rotateX: this.state.bounceValue},
          ],
        }}
      >
        <VrButton
          onEnter={()=>this.animateEnter()}
          onExit={()=>this.animateExit()}
        >
          {this.props.children}
        </VrButton>
      </Animated.View>
    );
  }
}; 
```

当光标进入按钮区域时，`Animated.spring`会将`this.state.bounceValue`的值从`0`变为`1`，并显示一个弹跳效果。当光标退出按钮区时，`Animated.timing`会在`50`毫秒内将`this.state.bounceValue`的值从`1`变为`0`。为了实现这一点，我们用一个`Animated.View`组件包装了`VrButton`，该组件将在每次状态改变时改变`View`的`rotateX`转换。

在`index.vr.js`中，我们可以添加一个`SpotLight`(你可以添加任何你想要的其他类型的光或者改变它的属性)并使用`SoundShape`组件以这种方式添加一个圆柱体:

```
...
import {
  AppRegistry,
  asset,
  Pano,
  SpotLight,
  View,
  Cylinder,
} from 'react-vr';
import Forest from './components/Forest';
import SoundShape from './components/SoundShape';

export default class musical_exp_react_vr_pusher extends React.Component {
  render() {
    return (
      <View>
        ...

        <SpotLight intensity={1} style={{transform: [{translate: [1, 4, 4]}],}} /> 
        <SoundShape>
          <Cylinder
            radiusTop={0.2}
            radiusBottom={0.2}
            dimHeight={0.3}
            segments={8}
            lit={true}
            style={{
              color: '#96ff00', 
              transform: [{translate: [-1.5,-0.2,-2]}, {rotateX: 30}],
            }}
          />
        </SoundShape>
      </View>
    );
  }
};
... 
```

当然，你可以改变形状的属性，甚至用 3D 模型来替换它们。

让我们再添加一个金字塔(它是一个半径为零的圆柱体，有四个部分):

```
<SoundShape>
  <Cylinder
    radiusTop={0}
    radiusBottom={0.2}
    dimHeight={0.3}
    segments={4}
    lit={true}
    style={{
      color: '#96de4e',
      transform: [{translate: [-1,-0.5,-2]}, {rotateX: 30}],
    }}
  /> </SoundShape> 
```

一个立方体:

```
<SoundShape>
  <Box
    dimWidth={0.2}
    dimDepth={0.2}
    dimHeight={0.2}
    lit={true}
    style={{
      color: '#a0da90', 
      transform: [{translate: [-0.5,-0.5,-2]}, {rotateX: 30}],
    }}
  /> </SoundShape> 
```

一个盒子:

```
<SoundShape>
  <Box
    dimWidth={0.4}
    dimDepth={0.2}
    dimHeight={0.2}
    lit={true}
    style={{
      color: '#b7dd60',
      transform: [{translate: [0,-0.5,-2]}, {rotateX: 30}],
    }}
  /> </SoundShape> 
```

一个球体:

```
<SoundShape>
  <Sphere
    radius={0.15}
    widthSegments={20}
    heightSegments={12}
    lit={true}
    style={{
      color: '#cee030',
      transform: [{translate: [0.5,-0.5,-2]}, {rotateX: 30}],
    }}
  /> </SoundShape> 
```

和三棱镜:

```
<SoundShape>
  <Cylinder
    radiusTop={0.2}
    radiusBottom={0.2}
    dimHeight={0.3}
    segments={3}
    lit={true}
    style={{
      color: '#e6e200',
      transform: [{translate: [1,-0.2,-2]}, {rotateX: 30}],
    }}
  /> </SoundShape> 
```

添加必要的导入后，保存文件并刷新浏览器。你应该看到这样的东西:
[![Shapes](../Images/d86ce26fa3a5b52f3596fb0b264ec32a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sCI6GpxG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dl.dropboxusercontent.com/s/i7qj34285dcc3z0/creating-a-musical-experience-with-react-vr-and-pusher-shapes.gif)

现在让我们添加一些声音！

## 添加声音

对于音频，React VR 支持`wav`、`mp3`和`ogg`文件等。你可以在这里找到完整的列表[。](https://github.com/facebook/react-vr/blob/master/ReactVR/js/Audio/getSupportedFormats.js)

你可以去 [Freesound](https://www.freesound.org) 或者其他类似的网站获取一些声音文件。下载你喜欢的，放在目录`static_assets/sounds`里。在这个项目中，我们将使用六种动物的声音，[像一只鸟](https://www.freesound.org/people/funkymuskrat/sounds/217050/)，[另一只鸟](https://www.freesound.org/people/dobroide/sounds/59367/)，[另一只鸟](https://www.freesound.org/people/dobroide/sounds/34162/)，[一只猫](https://www.freesound.org/people/NoiseCollector/sounds/4911/)，[一只狗](https://www.freesound.org/people/NoiseCollector/sounds/4911/)和[一只蟋蟀](https://www.freesound.org/people/Adam_N/sounds/164482/)(作为一个快速注释，我不得不重新保存这个文件，降低其比特率，以便可以在 React VR 上播放)。

出于我们的目的，React VR 为我们提供了三个播放声音的选项:

*   [VR 音效](https://github.com/facebook/react-vr/blob/master/Libraries/Utilities/VrSoundEffects.js)
*   VrButton 的 [onClickSound](https://facebook.github.io/react-vr/docs/vrbutton.html#onclicksound) 事件
*   一个[声音](https://facebook.github.io/react-vr/docs/sound.html)分量

但是，只有声音组件支持 3D/位置音频，因此当听众在场景中移动或转头时，声音的左右平衡会发生变化。因此，让我们将它添加到我们的`SoundShape`组件中，并将一个`onClick`事件添加到`VrButton`中:

```
...
import {
  ...
  Sound,
} from 'react-vr';

export default class SoundShape extends React.Component {
  ...
  render() {
    return (
      <Animated.View
        ...
      >
        <VrButton
          onClick={() => this.props.onClick()}
          ...
        >
          ...
        </VrButton>
        <Sound playerState={this.props.playerState} source={this.props.sound} />
      </Animated.View>
    );
  }
} 
```

我们将使用一个 [MediaPlayerState](https://facebook.github.io/react-vr/docs/mediaplayerstate.html) 来控制声音的播放。两者都将作为组件的属性传递。

这样，让我们用`index.vr.js`中的信息定义一个数组:

```
...
import {
  ...
  MediaPlayerState,
} from 'react-vr';
...

export default class musical_exp_react_vr_pusher extends React.Component {

  constructor(props) {
    super(props);

        this.config = [
          {sound: asset('sounds/bird.wav'), playerState: new MediaPlayerState({})},
          {sound: asset('sounds/bird2.wav'), playerState: new MediaPlayerState({})},
          {sound: asset('sounds/bird3.wav'), playerState: new MediaPlayerState({})},
          {sound: asset('sounds/cat.wav'), playerState: new MediaPlayerState({})},
          {sound: asset('sounds/cricket.wav'), playerState: new MediaPlayerState({})},
          {sound: asset('sounds/dog.wav'), playerState: new MediaPlayerState({})},
        ];
  }

  ...
} 
```

以及一种在传递右索引时使用 MediaPlayerState 对象播放声音的方法:

```
 ...

export default class musical_exp_react_vr_pusher extends React.Component {

  ...

  onShapeClicked(index) {
    this.config[index].playerState.play();
  }

  ...
} 
```

现在，我们只需要将所有这些信息传递给我们的 SoundShape 组件。因此，让我们将我们的形状组合成一个数组，并使用映射函数来生成组件:

```
...

export default class musical_exp_react_vr_pusher extends React.Component {

  ...

  render() {
    const shapes = [
      <Cylinder
        ...
      />,
      <Cylinder
        ...
      />,
      <Box
        ...
      />,
      <Box
        ...
      />,
      <Sphere
        ...
      />,
      <Cylinder
        ...
      />
    ];

    return (
      <View>
        ...

        {shapes.map((shape, index) => {
          return (       
            <SoundShape 
              onClick={() => this.onShapeClicked(index)} 
              sound={this.config[index].sound} 
              playerState={this.config[index].playerState}>
                {shape}
            </SoundShape>
          );
        })}

      </View>
    );
  }

  ...
} 
```

如果您重新启动浏览器并尝试，您应该会听到单击形状时的声音。

现在让我们用 Pusher 为我们的应用添加实时多用户支持。

## 设置推动器

在[https://pusher.com/signup](https://pusher.com/signup)创建一个免费账户。
当你创建一个应用时，你会被要求输入一些配置选项:
[![Create Pusher App](../Images/f8b08872fda06c5d6241fb1f8844da74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IGUw1pnW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dl.dropboxusercontent.com/s/t1ud5lyv508wgyz/creating-a-musical-experience-with-react-vr-and-pusher-set-up-pusher.png)

输入名称，选择 React 作为前端技术，Node.js 作为后端技术。这将为您提供一些示例代码来帮助您入门:
[![Pusher Getting Started Code](../Images/b7e0a2d6483a0d8688ce00180fc53a2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6U0ZyQbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dl.dropboxusercontent.com/s/3b5knaiaykyn5y4/creating-a-musical-experience-with-react-vr-and-pusher-app.png)

但是不要担心，这不会把你限制在这些特定的技术上，因为你可以随时改变它们。使用 Pusher，您可以使用库的任意组合。

接下来，复制你的集群 ID(在应用标题旁边，在这个例子中是`mt1`)、应用 ID、密钥和秘密信息，因为我们接下来需要它们。您也可以在*应用键*标签中找到它们。

## 发布事件

React VR 充当 [Web Worker](https://www.w3schools.com/HTML/html5_webworkers.asp) (你可以在这个[视频](https://www.youtube.com/watch?v=KHx-32m50c4)中了解更多 React VR 架构)所以我们需要这样在`index.vr.js`中包含 Pusher 的 Worker 脚本:

```
...
importScripts('https://js.pusher.com/4.1/pusher.worker.min.js');

export default class musical_exp_react_vr_pusher extends React.Component {
  ...
} 
```

我们有两个需要注意的需求。首先，我们需要能够通过 URL 传递一个标识符(像`http://localhost:8081/vr/?channel=1234`)，这样用户就可以选择他们想要的频道，并与他们的朋友分享。

为了解决这个问题，我们需要读取 URL。幸运的是，React VR 自带了[原生模块位置](https://facebook.github.io/react-vr/docs/native-modules.html)，这使得 React 上下文可以使用对象`window.location`的属性。

接下来，我们需要调用一个服务器来发布 Pusher 事件，这样所有连接的客户端也可以播放该事件。但是，我们不希望广播事件的客户端也接收到它，因为在这种情况下，声音将播放两次，并且当用户单击形状时可以立即播放声音时，等待接收事件来播放声音是没有意义的。

每个推动器连接都分配有一个唯一的插座 ID。要排除接收者接收 Pusher 中的事件，我们只需要向服务器传递我们希望在触发事件时排除的客户端的套接字 ID。(你可以在这里找到更多信息[。)](https://pusher.com/docs/server_api_guide/server_excluding_recipients)

这样，稍微修改一个函数(`getParameterByName`)来让[读取 URL](http://stackoverflow.com/a/901144/3593852) 的参数，并在成功连接到 Pusher 时保存`socketId`，我们就可以同时满足这两个要求:

```
...
import {
  ...
  NativeModules,
} from 'react-vr';
...
const Location = NativeModules.Location;

export default class musical_exp_react_vr_pusher extends React.Component {
  componentWillMount() {
    const pusher = new Pusher('<INSERT_PUSHER_APP_KEY>', {
      cluster: '<INSERT_PUSHER_APP_CLUSTER>',
      encrypted: true,
    });
    this.socketId = null;
    pusher.connection.bind('connected', () => {
      this.socketId = pusher.connection.socket_id;
    });
    this.channelName = 'channel-' + this.getChannelId();
    const channel = pusher.subscribe(this.channelName);
    channel.bind('sound_played',  (data) => {
      this.config[data.index].playerState.play();
    });
  }

  getChannelId() {
    let channel = this.getParameterByName('channel', Location.href);
    if(!channel) {
      channel = 0;
    }

    return channel;
  }

  getParameterByName(name, url) {
    const regex = new RegExp("[?&]" + name + "(=([^&#]*)|&|#|$)");
    const results = regex.exec(url);
    if (!results) return null;
    if (!results[2]) return '';
    return decodeURIComponent(results[2].replace(/\+/g, " "));
  }

  ...
} 
```

如果 URL 中没有 channel 参数，默认情况下我们将 ID 指定为 0。该 ID 将被添加到推送通道中，以使其唯一。

最后，我们只需要调用服务器端的一个端点来发布事件，传递客户端的套接字 ID 和发布事件的通道:

```
...
export default class musical_exp_react_vr_pusher extends React.Component {
  ...
  onShapeClicked(index) {
    this.config[index].playerState.play();
    fetch('http://<INSERT_YOUR_SERVER_URL>/pusher/trigger', {
      method: 'POST',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        index: index,
        socketId: this.socketId,
        channelName: this.channelName,
      })
    });
  }
  ...
} 
```

这就是 React 部分的所有代码。现在我们来看看服务器。

## 创建 Node.js 后端

执行以下命令生成一个`package.json`文件:

```
npm init -y 
```

添加以下依赖项:

```
npm install --save body-parser express pusher 
```

并将以下代码保存在一个文件中:

```
const express = require('express');
const bodyParser = require('body-parser');
const Pusher = require('pusher');

const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
/*
  The following headers are needed because the development server of React VR
  is started on a different port than this server. 
  When the final project is published, you may not need this middleware
*/
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*")
  res.header("Access-Control-Allow-Headers", 
             "Origin, X-Requested-With, Content-Type, Accept")
  next();
});

const pusher = new Pusher({
  appId: '<INSERT_PUSHER_APP_ID>',
  key: '<INSERT_PUSHER_APP_KEY>',
  secret: '<INSERT_PUSHER_APP_SECRET>',
  cluster: '<INSERT_PUSHER_APP_CLUSTER>',
  encrypted: true,
});

app.post('/pusher/trigger', function(req, res) {
  pusher.trigger(req.body.channelName, 
                 'sound_played', 
                 { index: req.body.index },
                 req.body.socketId );
  res.send('ok');
});

const port = process.env.PORT || 5000;
app.listen(port, () => console.log(`Running on port ${port}`)); 
```

可以看到，这里我们设置了一个 Express 服务器、Pusher 对象和 route `/pusher/trigger`，它只是用要播放的声音的索引和 socketID 来触发一个事件，以排除事件的接收方。

我们结束了。我们来测试一下。

## 测试

使用以下命令执行 Node.js 后端:

```
node server.js 
```

在`index.vr.js`中更新你的服务器 URL(用你的 IP 地址代替`localhost`)并在你的浏览器中输入一个类似[的地址 http://localhost:8081/vr/？两个浏览器窗口中的 channel=1234](http://localhost:8081/vr/?channel=1234) 。当你点击一个形状时，你应该会听到声音播放两次(当然，和另一台电脑上的朋友一起这样做更有趣):

[https://www.youtube.com/embed/c1lf8FearWQ](https://www.youtube.com/embed/c1lf8FearWQ)

## 结论

React VR 是一个很棒的库，可以用一种简单的方式创建虚拟现实体验，特别是如果你已经知道 React/React Native 的话。将它与 Pusher 结合起来，你将拥有强大的工具来编写下一代 web 应用程序。

您可以按照本页中的步骤构建该项目的生产版本，以将其部署在任何 web 服务器中。

此外，您可以通过更改颜色、形状、声音或从原始音乐森林添加更多功能来自定义此代码。

最后，请记住，您可以在这个 GitHub 存储库中找到应用程序的代码。

这个帖子最早出现在 [Pusher 的博客](https://blog.pusher.com/building-a-realtime-react-vr-app/)上。