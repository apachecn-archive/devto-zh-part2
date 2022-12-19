# 使用 React Native 构建咖啡地图

> 原文：<https://dev.to/iwilsonq/building-a-coffee-map-with-react-native--3b45>

年轻的 web 开发人员了解 web。他们已经花了无数的时间来解决分歧和铸造利润。他们已经用 JavaScript、Python、Golang、Fortran、Basic 开发了无数的待办应用程序...你说吧！

但现在，这位炙手可热的开发商想要征服一个不太熟悉的领域。他们想取代他们团队中的移动开发人员；通过使用 hip 新框架，所有这些都是本机反应。考虑到这实际上就像编写一个简单的 web 应用程序，他们安装了 React Native CLI 并搭建了一个空项目。

开始还不错。他们发现他们必须创建视图，而不是 div。为了创建文本，他们必须使用内置的文本组件。他们必须使用内联 JavaScript 样式，而不是 CSS。为了创建一个好的布局，他们需要一些 flexbox 的知识。

但是，他们希望使用更强大的功能，如地理定位、音频输入或推送通知。他们发现，为了启用这些功能，他们必须打开 XCode 并编辑一些相当冗长的配置文件，更改 plist，并在 Objective-C 中创建头文件和实现。他们发现，也许他们应该坚持使用 web。

# 进入世博会

幸运的是， [Expo](https://expo.io) 的美丽团队已经创建了一个非常强大的 SDK，它极大地改善了 React 原生开发者的体验。他们使得当你用 Expo 创建一个应用程序时，你可能永远也不需要打开 XCode 或者编辑任何特定于平台的配置文件。

如果您熟悉用于引导 React web 应用程序的 create-react-app，Expo 的工作方式也差不多。从命令行运行`exp init <project-name>`，然后进入项目目录，用`exp start`运行它。Expo 为您提供了一个二维码，您可以使用它在您的设备上查看您的项目。你也可以使用`exp ios`或`exp android`来运行模拟器。模拟器在保存之间稍微快一点，但是没有真实设备的性能好。

# Espressopedia

这就像咖啡的 expedia。或者类似的东西。从高层次的角度来看，该应用程序将如下所示:

*   我们将有一个用户的位置在中心的地图视图
*   地图上会有一组标记，标明附近咖啡店和茶叶店的位置

我们将使用 Yelp API 来获取咖啡馆列表。他们的 API 很容易设置和使用，只需去 [Yelp](https://www.yelp.com/developers/v3/manage_app) 注册然后创建一个应用程序。

## 创建新项目

让我们开始编码。从安装 expo cli 开始。

```
npm install -g exp 
```

Enter fullscreen mode Exit fullscreen mode

然后运行

```
exp init espressopedia 
```

Enter fullscreen mode Exit fullscreen mode

它会问你是想要建立一个空白的模板项目，还是一个带有一些启动文件的项目，比如 tab navigator。我选择空白项目，因为我们不需要任何标签导航。

现在我要在 iOS 模拟器中测试这个应用程序。你也可以使用你自己的设备，但这取决于你下载 expo 客户端并进行设置。运行模拟器:

```
exp ios

# or for Android

exp android 
```

Enter fullscreen mode Exit fullscreen mode

并建立项目

```
exp start 
```

Enter fullscreen mode Exit fullscreen mode

现在，当你打开根目录时，你会发现空白模板`App.js`文件。

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Open up App.js to start working on your app!</Text>
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
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你是一个 React 老手，这个文件应该不会看起来太吓人。注意`View`和`Text`标签的用法。这个文件使用了`StyleSheet`，但是我们也可以将样式定义为一个普通的对象。

## 建筑地图

我们将探索的第一个 expo API 是`MapView`组件。

```
// app/components/Map.js
import React, { Component } from 'react';
import { View, Text } from 'react-native';
import { MapView } from 'expo';

const Marker = MapView.Marker;

export default class Map extends Component {
  renderMarkers() {
    return this.props.places.map((place, i) => (
      <Marker 
        key={i}
        title={place.name}
        coordinate={place.coords}
      />
    ));
  }

  render() {
    const { region } = this.props

    return (
      <MapView
      style={styles.container}
      region={region}
      showsUserLocation
      showsMyLocationButton
      >
        {this.renderMarkers()}
      </MapView>
    );
  }
}

const styles = {
  container: {
    width: '100%',
    height: '80%'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个地图组件是 Expo 的`MapView`组件的包装器。通过选择包装内置组件，我们可以通过生命周期方法和特定于应用程序的方法(如渲染标记)用功能来装饰我们的地图。这里，它不是针对我们查找咖啡店的用例实现的——这个决定是在呈现它的`App.js`组件中做出的。

```
// App.js
import React from 'react';
import { Text, SafeAreaView } from 'react-native';
import Map from './app/components/Map'

// A placeholder until we get our own location
const region = {
  latitude: 37.321996988,
  longitude: -122.0325472123455,
  latitudeDelta: 0.0922,
  longitudeDelta: 0.0421
}

export default class App extends React.Component {
  state = {
    region: null
    coffeeShops: []
  }

  render() {
    return (
      <SafeAreaView style={styles.container}>
        <Map
          region={region}
          places={this.state.coffeeShops}
        />
      </SafeAreaView>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们传递一个初始区域对象，它应该把你的地图放在库比蒂诺市周围的某个地方。当我们将用户位置放在地图视图的中心时，我们将替换它。我们还将`SafeAreaView`用于顶级组件。这使得我们的内容看起来很好，即使是在 iPhone X 怪异的屏幕区域。

# 获取用户位置

为了获得用户位置，我们可以使用 expo 中的`Location`和`Permissions`模块。将此添加到`App.js`T3 中

```
// App.js
/* ... */
import { Location, Permissions } from 'expo'

const deltas = {
  latitudeDelta: 0.0922,
  longitudeDelta: 0.0421
};

export default App extends Component {
  state = {
    region: null,
    coffeeShops: []
  };

  componentWillMount() {
    this.getLocationAsync();
  }

  getLocationAsync = async () => {
    let { status } = await Permissions.askAsync(Permissions.LOCATION);
    if (status !== 'granted') {
      this.setState({
        errorMessage: 'Permission to access location was denied'
      });
    }

    let location = await Location.getCurrentPositionAsync({});
    const region = {
      latitude: location.coords.latitude,
      longitude: location.coords.longitude,
      ...deltas
    };
    await this.setState({ region });
  }

    render() { /* ... */ }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们确保获得用户的许可来使用地理定位，因为我们的应用程序正在安装。如果他们拒绝，我们在状态中设置一个错误消息，并选择显示它而不是地图。一旦许可被授予，我们就可以调用`getCurrentPositionAsync`，它返回一个比我们需要的稍微复杂一点的位置对象，所以我们只获取我们想要的属性，即纬度和经度(以及增量，这样我们的地图就知道缩放多少)。

# 抓取数据

为了获得咖啡店数据，我们将查询 Yelp API。在 Yelp 上设置应用程序非常简单，只需登录并进入[管理应用程序](https://www.yelp.com/developers/v3/manage_app)。在这里，您将获得一个 API 密钥，您可以使用它来消费他们的 API。

就像在 web 上一样，我们可以利用 axios 库来执行 HTTP 请求。继续运行

```
npm install --save axios 
```

Enter fullscreen mode Exit fullscreen mode

现在为了模块化，我将在 app 目录内添加一个名为`services`的新文件夹，在这个文件夹内，有一个名为`yelp.js`的文件。在这里，我们定义了我们的应用程序将如何与 Yelp 的 API 接口。

```
// app/services/yelp.js
import axios from 'axios';

const YELP_API_KEY = '<YOUR_API_KEY>';

const api = axios.create({
  baseURL: 'https://api.yelp.com/v3',
  headers: {
    Authorization: `Bearer ${YELP_API_KEY}`
  }
});

const getCoffeeShops = userLocation => {
  return api
    .get('/businesses/search', {
      params: {
        limit: 10,
        categories: 'coffee,coffeeroasteries,coffeeshops',
        ...userLocation
      }
    })
    .then(res =>
      res.data.businesses.map(business => {
        return {
          name: business.name,
          coords: business.coordinates
        };
      })
    )
    .catch(error => console.error(error));
};

export default {
  getCoffeeShops
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个服务的工作方式是创建一个带有`axios.create`的 http 客户端，并传入`baseURL`和`Authorization`报头。然后，我们可以通过向`https://api.yelp.com/v3/businesses/search`发送一个带有查询参数的 GET 请求，使用它来查询 Yelp api。Axios 允许我们将参数设置为参数列表中的一个对象，从而简化了这一过程。之后，这个`getCoffeeShops`方法唯一让我们的应用程序与众不同的地方是我们在请求中指定了`categories`。我们可以把它改成“纯素”或“汉堡”,这将完全改变我们地图的结果。大部分时间。

现在让我们在`App.js`内部消费这个服务，从导入 YelpService 开始。

```
// App.js
/* ... */
import YelpService from './app/services/yelp'

export default App extends Component {

  /* ... */

  getCoffeeShops = async () => {
    const { latitude, longitude } = this.state.region;
    const userLocation = { latitude, longitude };
    const coffeeShops = await YelpService.getCoffeeShops(userLocation);
    this.setState({ coffeeShops });
  };

  getLocationAsync = async () => {

    /* ... */

    // Add this line!
    await this.getCoffeeShops();
  }

  render() {
    const { region, coffeeShops } = this.state;
    return (
      <SafeAreaView style={styles.container}>
        <Map region={region} places={coffeeShops} />
      </SafeAreaView>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们开始工作了！你应该能看到一张标有标记的地图，以及你的位置。除非你在模拟器上。然后你会看到你在旧金山的某个地方。我想知道那是不是世博会团队工作的地方？

[![Final](../Images/0ec1061e0fc112cd5392890cda02b4fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--REUY11a7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f0mdowhjji8m8jnmjf9d.png)

我希望你能从这篇文章中得到乐趣，希望它能激励你做出更酷的东西。在我准备这篇文章的过程中，我创建了一个类似的应用程序，增加了一些额外的功能，它甚至有过滤按钮。其中一个过滤器是“星巴克”，以防你找不到它们。如果你感兴趣，你可以在这里看到代码。

因为这是我的第一篇文章，我很感谢大家的评论、建议或批评。这将激发参与度，并确保未来的帖子会更好。

下次见。