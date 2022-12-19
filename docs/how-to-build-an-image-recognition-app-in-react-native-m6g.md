# 如何在 30 分钟内在 React Native 中搭建一个图像识别 app

> 原文：<https://dev.to/andrewsmith1996/how-to-build-an-image-recognition-app-in-react-native-m6g>

几个月来，我一直对 React Native 着迷，并且已经在 Ionic 框架中做了一些开发，我对通过本机组件而不是 webview 渲染的应用程序的表现感到兴奋。

我们将构建一个简单的应用程序，它使用 React Native 的相机组件([https://github . com/React-Native-community/React-Native-camera](https://github.com/react-native-community/react-native-camera))来拍照，然后将该图像的 Base64 表示传递给免费的 clari fai Predict image API(【https://clarifai.com/】)来获取图像中内容的描述。

Clarifai API 是免费的，设置和使用非常简单，并且允许我们从图像中获得描述。

[![Screenshots of finished app](../Images/9c8cd55a6c2beb46a2d5a62825d7fd59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rklwuqx7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/njhlo5weodw30qtedg4k.png)

本教程假设您已经安装了 NodeJS 和 React Native。如果你还没有，那么请访问[https://Facebook . github . io/react-native/docs/getting-started . html](https://facebook.github.io/react-native/docs/getting-started.html)开始吧。它还假设您对 React 和 NodeJS 有基本的了解。

你还需要从[https://clarifai.com/developer/account/signup](https://clarifai.com/developer/account/signup)获得免费的 Clarifai API 密钥

## 我们将建造什么

我们将自己创建两个 React 组件:

1.  相机预览组件，它呈现内置的 React 本机相机组件。这将处理相机的预览，并包含识别图像中的内容的所有逻辑
2.  一个捕获按钮组件，它处理用户按下按钮来拍照，以及按钮的禁用状态。

## 我们开始吧

首先，你需要初始化一个新的 React 本地应用。

```
react-native init imageRecogitionReactNativeDemo 
```

然后 CD 进入新的 React 本地项目目录，运行以下命令启动 iOS 模拟器。

```
cd imageRecogitionReactNativeDemo
react-native run-ios 
```

接下来，我们将希望安装内置的反应本地相机组件，我们将使用

```
npm install react-native-camera --save~ 
```

然后我们想把我们的新图书馆连接起来

```
react-native link react-native-camera 
```

您还会希望安装 Clarifai，这是我们将把我们的图像传递给它以获得标识的地方。

```
npm install clarifai 
```

我们还需要在 Info.plist 文件中添加一个 NSCameraUsageDescription，否则应用程序将会崩溃。这只是一个小的描述，你可以在其中说明你的应用程序将如何使用相机。因此，将以下内容添加到该项目的 iOS 文件夹中的 Info.plist 文件中。

```
<key>NSCameraUsageDescription</key>
<string>This app requires the camera to take an image to be identified</string> 
```

现在，您几乎已经完成了所有的设置，所以您已经准备好构建我们的 2 个组件了。

首先，我们希望构建我们的相机组件，它将包含所有其他内容。

因此，创建一个名为“组件”的文件夹，并在其中创建一个 Camera.js 文件。

在页面顶部，我们希望导入 React，以及 React 本机的维度、警报、样式表和 ActivityIndicator 模块以供使用。

```
import React from 'react';
import { Dimensions, Alert, StyleSheet, ActivityIndicator } from 'react-native'; 
```

然后，我们将希望实际上进口反应原生相机模块，我们已经安装通过 NPM。

```
import { RNCamera } from 'react-native-camera'; 
```

我们还将导入我们的 Capture button 组件，但是我们将在后面进行介绍。

设置相机的类别

```
 export default class Camera extends React.Component {

} 
```

接下来，我们要设置相机组件的状态，所以为相机类创建一个构造函数。我们需要设置两个状态变量

1.  我们希望在警告中显示的文本包含图像中已识别的单词(我称之为 identifiedAs)
2.  一个布尔值，确定相机是否处于加载状态(当我们识别图像中的内容时，与活动指示器一起使用)。

所以你的构造函数应该是这样的

```
constructor(props){
    super(props);

    this.state = { 
        identifedAs: '',
        loading: false
    }
} 
```

在 Camera 类的 render 函数中，我们要添加来自 React 本地相机组件文档的以下代码。这将只是从 React Native 加载内置的相机组件。

```
<RNCamera ref={ref => {this.camera = ref;}} style={styles.preview}></RNCamera> 
```

现在让我们添加按钮来拍照，为此我们将创建一个全新的组件。

继续在 components 文件夹中创建一个 CaptureButton.js 组件。

在这里，我们将从 React Native 导入按钮和可触摸突出显示组件。以及默认的样式表模块和 React。

```
import React  from 'react';
import { StyleSheet, Button, TouchableHighlight } from 'react-native'; 
```

然后在这个类的 render 函数中，我们将添加一个 TouchableHighlight 组件([https://Facebook . github . io/react-native/docs/touchable highlight](https://facebook.github.io/react-native/docs/touchablehighlight))和一个 Button 组件，以获得默认的 iOS 和 Android 样式。我们还将通过默认的样式道具添加我们自己的样式。我们还需要使用 disabled prop，它从父 Camera.js 组件的状态中获取一个道具。

```
<TouchableHighlight style={styles.captureButton} disabled={this.props.buttonDisabled}>
    <Button onPress={this.props.onClick} disabled={this.props.buttonDisabled} title="Capture" accessibilityLabel="Learn more about this button"/>
</TouchableHighlight> 
```

我们还想给这个按钮添加一个按下事件，这样它就知道当用户按下它时该做什么(例如，拍照并从中识别)。为此，我们将添加一个 onPress 事件，并从我们之前拥有的父 Camera.js 组件(Camera.js 中的一个函数)中为其提供道具。

我们还希望在按钮被点击时禁用它，因此我们将再次使用从 Camera.js 组件传递来的一些道具，因为最终是 Camera 组件而不是按钮决定是否正在拍照的状态。

让我们也为按钮添加一些样式，只是把它向上推，给它一个背景和一些圆角。

```
const styles = StyleSheet.create({
    captureButton: {
        marginBottom:30,
        width:160,
        borderRadius:10,
        backgroundColor: "white",
    }
}); 
```

然后简单地将这个样式添加到 TouchableHighlight 组件的样式属性中

```
style={styles.captureButton} 
```

所以总的来说，你的 Button.js 应该是这样的

```
import React  from 'react';
import { StyleSheet, Button, TouchableHighlight } from 'react-native';

export default class CaptureButton extends React.Component {
    render() {
        return (
            <TouchableHighlight style={styles.captureButton} disabled {this.props.buttonDisabled}>
                <Button onPress={this.props.onClick} disabled={this.props.buttonDisabled} title="Capture" accessibilityLabel="Learn more about this button"/>
            </TouchableHighlight>
        );
    }
}

const styles = StyleSheet.create({
    captureButton: {
        marginBottom:30,
        width:160,
        borderRadius:10,
        backgroundColor: "white"
    }
}); 
```

现在回到 Camera.js 组件，你的渲染函数应该是这样的。我们已经通过 style props 为预览区域添加了一些样式，并且添加了我们自己的 buttonDisabled props，它将相机的加载状态发送到子按钮组件。我们还添加了 onClick 属性，并将其绑定到 takePicture()函数。

```
render() {
    return (
        <RNCamera ref={ref => {this.camera = ref;}} style={styles.preview}>
            <CaptureButton buttonDisabled={this.state.loading} onClick={this.takePicture.bind(this)}/>
        </RNCamera>
    );
} 
```

我们需要添加一个活动指示器([https://Facebook . github . io/react-native/docs/Activity Indicator](https://facebook.github.io/react-native/docs/activityindicator))来告诉用户图像正在被识别。

因此，让我们使用 React Native 的活动指示器组件，这是我们之前导入的。

```
<ActivityIndicator size="large" style={styles.loadingIndicator} color="#fff" animating={this.state.loading}/> 
```

为此，我们将使用默认的动画道具，并将其设置为类的加载状态，这样当状态更新时，活动指示器将相应地显示/隐藏。

总的来说，添加了我们的 ActivityIndicator 和我们自己的 Capture Button 组件后，Camera.js 组件的渲染函数应该如下所示

```
render() {
    return (
        <RNCamera ref={ref => {this.camera = ref;}} style={styles.preview}>
            <ActivityIndicator size="large" style={styles.loadingIndicator} color="#fff" animating={this.state.loading}/>
            <CaptureButton buttonDisabled={this.state.loading} onClick={this.takePicture.bind(this)}/>
        </RNCamera>
    );
} 
```

我们还将通过样式表模块添加一些样式，以使相机的预览和加载指示器居中，我们将使用维度导入来动态地使相机预览占据手机屏幕的整个宽度和高度。

```
const styles = StyleSheet.create({
    preview: {
        flex: 1,
        justifyContent: 'flex-end',
        alignItems: 'center',
        height: Dimensions.get('window').height,
        width: Dimensions.get('window').width,
    },
    loadingIndicator: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
    }
}); 
```

所以现在你应该有所有排序的用户界面，我们想添加拍照的功能。因此，首先我们要为我们创建的 Button.js 组件连接 click 事件。大部分代码取自 React 原生相机组件文档，但我将对其进行总结。

这希望成为一个异步函数

```
takePicture = async function(){

} 
```

然后，在检查相机已经初始化并且已经拍摄了照片之后，我们想要暂停相机的预览，在我们已经拍摄的照片上

```
// Pause the camera's preview
this.camera.pausePreview(); 
```

然后在此之后，我们可以简单地更新相机的状态，以计算图像的标签。

```
// Update the state to indicate loading
this.setState((previousState, props) => ({
    loading: true
})); 
```

然后，我们想要实际拍摄图片，并获得图片的 Base64 表示

```
//Set the options for the camera
const options = {
    base64: true
};

// Get the base64 version of the image
const data = await this.camera.takePictureAsync(options) 
```

然后我们将调用一个即将创建的新函数，它接受图像的 Base64 表示，并将其传递给 Clarifai API。

```
this.identifyImage(data.base64); 
```

同样，使用 Clarafai 文档，我们可以用 API 密钥初始化 Clarafai，并将 Base64 传递给它的 Predict API。然后，我们将把 JSON 响应中包含最高评级图像标签的部分传递给一个新函数。

```
identifyImage(imageData){

    // Initialise the Clarifai api
    const Clarifai = require('clarifai');

    const app = new Clarifai.App({
        apiKey: 'YOUR KEY HERE'
    });

    // Identify the image
    app.models.predict(Clarifai.GENERAL_MODEL, {base64: imageData})
        .then((response) =>  this.displayAnswer(response.outputs[0].data.concepts[0].name)
        .catch((err) => alert(err))
    );
} 
```

在 displayAnswer 函数中，我们希望更新应用程序的状态。这将设置警报消息的状态，禁用活动指示器，并重新启用所有按钮。

```
// Dismiss the acitivty indicator
this.setState((prevState, props) => ({
    identifedAs:identifiedImage,
    loading:false
})); 
```

现在我们已经得到了答案，我们将使用 React Native 的警报模块([https://facebook.github.io/react-native/docs/alert](https://facebook.github.io/react-native/docs/alert))在一个警报上显示给用户

```
Alert.alert(this.state.identifedAs,'',{ cancelable: false }); 
```

然后我们将恢复相机的预览，这样我们就可以拍一张新的照片。

```
// Resume the camera's preview
this.camera.resumePreview(); 
```

总的来说，您的 displayAnswer()函数应该如下所示

```
displayAnswer(identifiedImage){

    // Dismiss the acitivty indicator
    this.setState((prevState, props) => ({
        identifedAs:identifiedImage,
        loading:false
    }));

    // Show an alert with the answer on
    Alert.alert(this.state.identifedAs,'',{ cancelable: false });

    // Resume the preview
    this.camera.resumePreview();
} 
```

和整个 Camera.js 组件

```
import React from 'react';
import { Dimensions, Alert, StyleSheet, ActivityIndicator } from 'react-native';
import { RNCamera } from 'react-native-camera';
import CaptureButton from './CaptureButton.js'

export default class Camera extends React.Component {

    constructor(props){
        super(props);
        this.state = { 
            identifedAs: '',
            loading: false
        }
    }

    takePicture = async function(){

        if (this.camera) {

            // Pause the camera's preview
            this.camera.pausePreview();

            // Set the activity indicator
            this.setState((previousState, props) => ({
                loading: true
            }));

            // Set options
            const options = {
                base64: true
            };

            // Get the base64 version of the image
            const data = await this.camera.takePictureAsync(options)

            // Get the identified image
            this.identifyImage(data.base64);
        }
    }

    identifyImage(imageData){

        // Initialise Clarifai api
        const Clarifai = require('clarifai');

        const app = new Clarifai.App({
            apiKey: 'YOUR KEY HERE'
        });

        // Identify the image
        app.models.predict(Clarifai.GENERAL_MODEL, {base64: imageData})
        .then((response) => this.displayAnswer(response.outputs[0].data.concepts[0].name)
        .catch((err) => alert(err))
        );
    }

    displayAnswer(identifiedImage){

        // Dismiss the acitivty indicator
        this.setState((prevState, props) => ({
            identifedAs:identifiedImage,
            loading:false
        }));

    // Show an alert with the answer on
    Alert.alert(
            this.state.identifedAs,
            '',
            { cancelable: false }
        )

        // Resume the preview
        this.camera.resumePreview();
    }

    render() {
        return (
            <RNCamera ref={ref => {this.camera = ref;}} style={styles.preview}>
            <ActivityIndicator size="large" style={styles.loadingIndicator} color="#fff" animating={this.state.loading}/>
            <CaptureButton buttonDisabled={this.state.loading} onClick={this.takePicture.bind(this)}/>
            </RNCamera>
        );
    }
}

const styles = StyleSheet.create({
    preview: {
        flex: 1,
        justifyContent: 'flex-end',
        alignItems: 'center',
        height: Dimensions.get('window').height,
        width: Dimensions.get('window').width,
    },
    loadingIndicator: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
    }
}); 
```

现在回到顶层组件 App.js，导入您刚刚创建的漂亮的新相机组件。

```
import Camera from './components/Camera.js'; 
```

然后将其添加到 React 本机视图之间。

所以你的 App.js 应该是这样的

```
import React  from 'react';
import { StyleSheet, View } from 'react-native';
import Camera from './components/Camera.js';

export default class App extends React.Component {

    constructor(props){
        super(props);
        process.nextTick = setImmediate;
    }

    render() {
        return (
            <View style={styles.container}>
                <Camera />
            </View>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: '#fff',
        alignItems: 'center',
        justifyContent: 'center',   
    }
}); 
```

因此，总的来说，我们的简单应用程序已被分为 3 个组件，应用程序本身，我们自己的相机组件，和我们的按钮组件。然后，在此之上，我们使用内置的反应原生相机组件。

我们还利用了许多标准的 React 原生组件，如警报、活动指示器、样式表、可触摸突出显示和按钮。

因此，只需连接您的手机，并在 Xcode 中打开 Xcode 项目，将其加载到您的设备上进行测试。

这款应用的源代码可以在 Github[https://Github . com/andrewsmith 1996/Image-Recogition-React-Native](https://github.com/andrewsmith1996/Image-Recogition-React-Native)上找到，也展示在我的作品集上【https://andrewsmithdeveloper.com T2】这里

我希望你喜欢这篇文章，如果你对我的文章、代码或任何东西有任何问题或反馈，请告诉我！