# 如何在 30 分钟内在 React Native 中构建地理定位天气预报 app

> 原文：<https://dev.to/andrewsmith1996/how-to-build-an-geolocation-weather-forecast-app-in-react-native-in-30-minutes-1kmo>

继我上一篇关于如何在 React Native 中创建图像识别应用程序的指南([https://dev . to/andrewsmith 1996/how-to-build-a-Image-Recognition-app-in-React-Native-m6g](https://dev.to/andrewsmith1996/how-to-build-an-image-recognition-app-in-react-native-m6g))之后，我将写一篇关于如何在 React Native 中创建一个简单的地理定位天气预报应用程序的指南(当然，不到 30 分钟)

我们将构建一个简单的应用程序，它使用手机的地理定位功能来获取用户的位置，然后将该位置的纬度和经度传递给 Weather Map 的 Weather API，这将为我们提供该位置的 5 天天气预报(分为 3 个小时)。

天气应用程序是免费的，你需要在[https://openweathermap.org/api](https://openweathermap.org/api)拿到钥匙才能使用这个应用程序

[![Screenshots of finished app](../Images/66b02f23ae007b53c88dfab47bb709e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HElNNf5g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i9v16xw7a8cke74irm0s.png)

本教程假设您已经安装了 NodeJS 和 React Native。如果你还没有，那么请访问[https://Facebook . github . io/react-native/docs/getting-started . html](https://facebook.github.io/react-native/docs/getting-started.html)开始吧。它还假设您对 React 和 NodeJS 有基本的了解。

## 我们将建造什么

我们在这里实际上只创建了一个额外的 React 组件，这是一个实际的卡片，它将显示每个 3 小时的天气预报。

## 我们开始吧

首先，你需要初始化一个新的 React 本地应用。

```
react-native init geolocationWeatherReactNative 
```

然后 CD 进入新的 React 本地项目目录，运行以下命令启动 iOS 模拟器。

```
cd geolocationWeatherReactNative
react-native run-ios 
```

接下来，我们要安装 React Native Elements，这是一个 React Native UI 工具包，它将为我们提供一个在移动应用程序中常见的卡片组件。我们还将安装使用卡片元素所需的矢量图标库。

```
npm install --save react-native-elements

npm install react-native-vector-icons --save 
```

然后我们想把我们的新图书馆连接起来

```
react-native link react-native-vector-icons 
```

我们还需要在 Info.plist 文件中添加一个 NSLocationWhenInUseUsageDescription，否则应用程序将会崩溃。这只是一个简短的描述，说明你的应用程序将如何使用定位服务。因此，将以下内容添加到该项目的 iOS 文件夹中的 Info.plist 文件中。

```
<key>NSLocationWhenInUseUsageDescription</key>
<string>YOUR DESCRIPTION HERE</string> 
```

出于同样的原因，我们还需要将以下内容添加到 AndroidManifest.xml 中

```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" /> 
```

现在你差不多都准备好了。

首先，我们希望构建我们的 card 组件，它将被重用来显示每 3 个小时的天气预报细节。

## ForecastCard.js

因此，创建一个名为“components”的文件夹，并在其中创建一个 ForecastCard.js 文件。

在页面顶部，我们希望导入 React，以及 React Native 中的样式表、视图和图像模块，因为我们稍后会用到它们。

我们还需要从我们安装的 React 本地元素库中导入 Card 组件。

```
import React, {Component} from 'react';
import { StyleSheet, View, Image } from 'react-native';
import { Text, Card, Divider } from 'react-native-elements'; 
```

首先我们需要设置预测卡的类

```
 export default class ForecastCard extends Component {

} 
```

我们没有在这个组件中使用任何状态，它只是呈现我们从应用程序父组件传递给它的属性，所以不需要在这里添加构造函数。

在 ForecastCard 类的 render 函数中，我们将添加以下代码来暂时呈现一张空白卡片。

```
return (
    <Card containerStyle={styles.card}>

    </Card>
); 
```

然后将以下样式添加到卡片中，或者随意添加您自己的样式。

```
card:{
    backgroundColor:'rgba(56, 172, 236, 1)',
    borderWidth:0,
    borderRadius:20
} 
```

## App.js

现在让我们回到 App.js，开始研究应用程序的功能。

因此，让我们导入我们需要的所有模块:

```
import React, {Component} from 'react';
import { FlatList } from 'react-native'; 
```

请注意，我们正在导入 FlatList，这是一个 React 本机组件，我们稍后将使用它来呈现项目列表(预测卡)

我们将使用 4 个状态变量:

1.  用户位置的经度
2.  用户位置的纬度
3.  从 API 返回的预测
4.  指示 API 响应中是否有错误的错误字符串

然后在该类的构造函数中初始化这些

```
constructor(props){
    super(props);

    this.state = {
        latitude: 0,
        longitude: 0,
        forecast: [],
        error:''
    }; 
```

接下来，我们将创建一个函数，它将使用地理定位来返回用户的位置。所以用下面的代码设置一个 getLocation()函数。

```
getLocation(){

    // Get the current position of the user
    navigator.geolocation.getCurrentPosition(
    (position) => {
        this.setState(
        (prevState) => ({
            latitude: position.coords.latitude, 
            longitude: position.coords.longitude
            }), () => { this.getWeather(); }
        );
    },
        (error) => this.setState({ forecast: error.message }),
        { enableHighAccuracy: true, timeout: 20000, maximumAge: 1000 },
    );
} 
```

这段代码简单地使用内置的地理定位服务来获取用户的当前位置，然后将纬度和经度的状态设置为响应。然后，由于 setState()是一个异步操作，我们添加了一个回调函数来调用 getWeather()函数，这是我们接下来要设置的。

现在我们已经将用户的位置存储在应用程序的状态中，我们将使用这些数据传递给天气 API 来获取该地区的天气预报。

所以设置一个 getWeather()函数:

```
 getWeather(){

        // Construct the API url to call
        let url = 'https://api.openweathermap.org/data/2.5/forecast?lat=' + this.state.latitude + '&lon=' + this.state.longitude + '&units=metric&appid=YOUR API KEY HERE';

        // Call the API, and set the state of the weather forecast
        fetch(url)
        .then(response => response.json())
        .then(data => {
            this.setState((prevState, props) => ({
                forecast: data
        }));
        })
    } 
```

在上面的例子中，我们构建了一个 URL 字符串来调用 Weather API 的预测服务，然后我们添加了存储在类 state 中的纬度和经度。之后，我们追加 units 参数，以指定我们希望单位是公制的，然后我们将 API 键追加到末尾。

现在我们已经有了一个要调用的 URL，我们将使用 fetch()方法调用它，并使用 JSON 数据来设置预测变量的状态。

这将把预测的状态设置为一个数组，其中包含该位置 5 天的预测条目。

接下来，我们将使用 React Native 的 FlatList 组件在手机屏幕上呈现一个卡片列表:

```
render() {
    return (
        <FlatList data={this.state.forecast.list} style={{marginTop:20}} keyExtractor={item => item.dt_text} renderItem={({item}) => <ForecastCard detail={item} location={this.state.forecast.city.name} />} />
    );
} 
```

平面列表组件([https://facebook.github.io/react-native/docs/flatlist](https://facebook.github.io/react-native/docs/flatlist))接受多个道具，首先我们将为它提供“数据”，这是我们已经存储在 state 中的预测，然后我们将它指向 JSON 响应的“列表”部分，因为它包含每个 3 小时的预测块。然后，我们将使用 style 属性将列表向下推 20px，然后 keyExtractor 属性强制列表使用键的 id，而不是我们在列表中看到的默认“key”属性(在这种情况下，我们给它天气预报项目的时间戳作为唯一标识符)

下面一行是我们实际告诉 React 我们希望平面列表呈现什么的地方:

```
renderItem={({item}) => <ForecastCard detail={item} location={this.state.forecast.city.name} />} 
```

这里我们告诉它用我们创建的 ForecastCard 组件来呈现列表。

但是，首先我们需要将它导入到 App.js 文件的顶部:

```
import ForecastCard from './components/ForecastCard'; 
```

我们传递给它两个道具，细节和位置。细节基本上是我们从 API 调用的 JSON 响应中获得的 3 小时天气预报的每次迭代，这意味着我们可以访问每张卡中的每个数据块。那么 location 就是 JSON 响应的一部分，它包含天气预报所针对的城市。

现在我们已经有了 FlatList 设置，所以我们可以简单地将所有的属性传递给我们创建的 ForecastCard.js 组件。

## ForecastCard.js

现在，我们将在每张卡片上添加一个包含位置的标题。为此，我们将使用 React 原生文本元素，并显示我们传递给它的属性。

```
<Text style={styles.notes}>{this.props.location}</Text> 
```

然后，我们将使用视图组件添加图像和时间，并使用 Flexbox 将它们放置在每一侧:

```
<View style={{flexDirection:'row', justifyContent:'space-between', alignItems:'center'}}>
    <Image style={{width:100, height:100}} source={{uri:"https://openweathermap.org/img/w/" + this.props.detail.weather[0].icon + ".png"}} />
    <Text style={styles.time}>{time}</Text>
</View> 
```

注意我们是如何使用 Image 组件并向它传递从 JSON 响应中挑选出来的图像 URL 的属性的。

```
<Image style={{width:100, height:100}} source={{uri:"https://openweathermap.org/img/w/" + this.props.detail.weather[0].icon + ".png"}} /> 
```

为了显示时间，我们使用了一个变量。我们这样做是为了将日戳转换成一种更加用户友好的格式，并且只有时间。因此，在 render 函数中，就在 return 语句之前，我们将添加以下内容:

```
let time;

// Create a new date from the passed date time
var date = new Date(this.props.detail.dt*1000);

// Hours part from the timestamp
var hours = date.getHours();

// Minutes part from the timestamp
var minutes = "0" + date.getMinutes();

time = hours + ':' + minutes.substr(-2); 
```

这将把我们的日期戳格式化成一个很好的容易阅读的小时格式。

接下来添加分隔线，我们将使用分隔线组件，并给它一个颜色和一点点的空间。

```
<Divider style={{ backgroundColor: '#dfe6e9', marginVertical:20}} /> 
```

然后，我们卡组件的最后一部分将是描述和温度:

```
<View style={{flexDirection:'row', justifyContent:'space-between'}}>
    <Text style={styles.notes}>{this.props.detail.weather[0].description}</Text>
    <Text style={styles.notes}>{Math.round( this.props.detail.main.temp * 10) / 10 }&#8451;</Text>
</View> 
```

同样，我们将使用 flexDirection 和 justifyContent 在卡片的两侧隔开它们。我们将使用两个文本组件，第一个显示 JSON 响应中包含文本描述的部分，然后第二个文本元素包含 JSON 响应的温度部分，四舍五入到 1 位小数，得到一个格式良好的温度。然后我们将添加 HTML 实体

```
&#8451; 
```

添加摄氏符号。

然后，为了设置样式，我们将添加以下内容:

```
 const styles = StyleSheet.create({
    time:{
        fontSize:38
    },
    notes: {
        fontSize: 18,
        textTransform:'capitalize'
    }
}); 
```

总的来说，我们已经介绍了如何使用平面列表来呈现卡片列表，如何使用地理定位来获取坐标，以及如何使用天气 API 来获取 JSON 响应以及给定位置的天气预报。

我们还利用了许多新的 React 原生组件，如图像和平面列表，以及 React 原生元素库的介绍，以及如何使用卡片和分隔线。

因此，只需连接您的手机，并在 Xcode 中打开 Xcode 项目，将其加载到您的设备上进行测试。

这个应用程序的源代码可以在 Github[https://Github . com/andrewsmith 1996/geolocationweathereactnational](https://github.com/andrewsmith1996/geolocationWeatherReactNative)上找到，也展示在我的作品集上【https://andrewsmithdeveloper.com T2】这里

我希望你喜欢这篇文章，如果你对我的文章、代码或任何东西有任何问题或反馈，请告诉我！