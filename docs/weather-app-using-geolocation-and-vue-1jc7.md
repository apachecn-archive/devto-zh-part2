# 天气应用:使用地理定位和 Vue

> 原文：<https://dev.to/fabiorosado/weather-app-using-geolocation-and-vue-1jc7>

这是从我的[个人博客](http://fabiorosado.co.uk)上传的帖子，希望你喜欢！

在这篇文章中，我将使用与我之前的教程相同的文件- [创建一个天气应用程序:使用 Vue 和 Axios](https://dev.to/fabiorosado/weather-app-using-vue-and-axios-4ia6) -因为我将向你展示的内容与之前的教程紧密相关。

你可能想知道，我为什么写这篇文章。

之前的教程是对 Vue 和 Axios 的介绍，但是位置是硬编码在 Vue app 里的。如果你想要一个工作的天气应用程序，你只能得到伦敦的天气，而不是你所在城市的天气。

*你需要阅读之前的教程来遵循这个吗？*

不，你可以[得到需要的文件](https://github.com/FabioRosado/fabiorosado.github.io/tree/master/assets/files/weather-tutorial)然后从我们停下的地方开始。你还需要从 OpenWeatherMap 获取一个 [API 密匙，这样你就可以获得天气信息。](https://openweathermap.org/api)

# 地理定位

HTML5 提供了地理定位 API，因此您可以获得用户的位置——在本例中是您。根据[我能使用](https://caniuse.com/#search=geolocation)吗，93%的浏览器都有地理定位功能，这意味着大多数浏览器都支持这一功能。

使用地理定位时，您需要知道一些事情:

*   用户需要批准共享位置的请求
*   对于带 GPS 的设备，地理定位更加准确
*   地理定位 API 只能在 HTTP 等安全环境下工作(有些浏览器不会要求许可，直接拒绝)。

因为地理定位取决于用户是否同意分享位置，所以当我们的应用程序无法获取位置时，我们应该有一个策略。

最后值得一提的是。我尝试了几种方法让地理定位和 Vue 一起工作。其中很多都失败了，但最终，我通过创建一个函数来检查地理定位是否启用，从而使它成功了。如果是，那么将调用一个新的函数，我们将获得经度和纬度的详细信息。

# VueJs

我们的 VueJs 应用程序处于这种状态:

```
let weatherApp = new Vue({
  el: '#app',
  data: {
    currentTemp: '',
    minTemp: '',
    maxTemp:'',
    sunrise: '',
    sunset: '',
    pressure: '',
    humidity: '',
    wind: '',
    overcast: '', 
    icon: ''
  },
  methods: {
    getWeather() {
      let url = "http://api.openweathermap.org/data/2.5/weather?q=London&units=metric&APPID={Your API Key}";
      axios
        .get(url)
        .then(response => {
          this.currentTemp = response.data.main.temp;
          this.minTemp = response.data.main.temp_min;
          this.maxTemp = response.data.main.temp_max;
          this.pressure = response.data.main.pressure;
          this.humidity = response.data.main.humidity + '%';
          this.wind = response.data.wind.speed + 'm/s';
          this.overcast = response.data.weather[0].description;
          this.icon = "images/" + response.data.weather[0].icon.slice(0, 2) + ".svg";
          this.sunrise = new Date(response.data.sys.sunrise*1000).toLocaleTimeString("en-GB").slice(0,4);
          this.sunset = new Date(response.data.sys.sunset*1000).toLocaleTimeString("en-GB").slice(0,4);
      })
      .catch(error => {
        console.log(error);
      });
    },
  },
  beforeMount() {
    this.getWeather();
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

很快，我们可以看到方法`getWeather`需要重构，因为 API URL 被硬编码为只显示伦敦的天气。该函数的其余部分看起来相当不错，因为我们只是对从 API 获得的值进行赋值。如果我们改变 URL 位，一切将保持不变。

```
 getWeather(url) {
      axios
        .get(url)
        .then(response => {
          this.currentTemp = response.data.main.temp;
          this.minTemp = response.data.main.temp_min;
          this.maxTemp = response.data.main.temp_max;
          this.pressure = response.data.main.pressure;
          this.humidity = response.data.main.humidity + '%';
          this.wind = response.data.wind.speed + 'm/s';
          this.overcast = response.data.weather[0].description;
          this.icon = "images/" + response.data.weather[0].icon.slice(0, 2) + ".svg";
          this.sunrise = new Date(response.data.sys.sunrise*1000).toLocaleTimeString("en-GB").slice(0,4);
          this.sunset = new Date(response.data.sys.sunset*1000).toLocaleTimeString("en-GB").slice(0,4);
      })
      .catch(error => {
        console.log(error);
      });
    }, 
```

Enter fullscreen mode Exit fullscreen mode

变量`URL`被删除了，我们只是给`getWeather`函数增加了一个参数`url`。现在，我们可以着手创建地理定位方法并获取 URL。

首先，我们将在 main.js 文件的顶部添加两个常量变量(第 1 行和第 2 行):

```
const API = 'http://api.openweathermap.org/data/2.5/weather?units=metric'
const KEY = '&APPID={Your API Key Here}' 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 VueJs 应用程序中，我们将创建两个方法:

*   `geolocation()`
*   `buildUrl(position)`

正如我之前所说，如果我们想对通过浏览器获得的纬度和经度做一些事情，地理定位需要有一个函数作为参数。方法`buildUrl(position)`就是，位置参数会用来提取这两个东西。

## 地理定位的方法

这些方法很容易理解，首先，我们将从地理位置 API 调用`getCurrentPosition`方法。传递的两个参数是回调函数——一个是如果成功(`this.buildUrl`)，另一个是如果返回错误(`this.geoError`)。

```
 geolocation() {
      navigator.geolocation.getCurrentPosition(this.buildUrl, this.geoError);
    },
    buildUrl(position) {
      const lat = position.coords.latitude;
      const lon = position.coords.longitude;

      this.getWeather(API + '&lat=' + lat + '&lon=' + lon + KEY);
    },
    geoError(error) {
      this.getWeather(API + '&lat=0&lon=0' + KEY);
    } 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这些方法非常简单，我们只是根据获取用户位置的成功程度来构建 API URL，如果不成功，我们就使用 0，0 作为位置。如果你知道它在哪里，就可以加分。

我们需要对 VueJs 应用程序做的最后一个更改是替换在`beforeMount()`上调用的方法。我们需要打电话给`this.geolocation()`，而不是打电话给`this.getWeather()`。

我们的`main.js`文件将如下所示:

```
const API = 'http://api.openweathermap.org/data/2.5/weather?units=metric'
const KEY = '&APPID=9ef28fe3735a820928decffcf48f5c3f'

let weatherApp = new Vue({
  el: '#app',
  data: {
    currentTemp: '',
    minTemp: '',
    maxTemp:'',
    sunrise: '',
    sunset: '',
    pressure: '',
    humidity: '',
    wind: '',
    overcast: '', 
    icon: '',
    location: ''
  },
  methods: {
    getWeather(url) {
      axios
        .get(url)
        .then(response => {
          this.currentTemp = response.data.main.temp;
          this.minTemp = response.data.main.temp_min;
          this.maxTemp = response.data.main.temp_max;
          this.pressure = response.data.main.pressure;
          this.humidity = response.data.main.humidity + '%';
          this.wind = response.data.wind.speed + 'm/s';
          this.overcast = response.data.weather[0].description;
          this.icon = "images/" + response.data.weather[0].icon.slice(0, 2) + ".svg";
          this.sunrise = new Date(response.data.sys.sunrise*1000).toLocaleTimeString("en-GB").slice(0,5);
          this.sunset = new Date(response.data.sys.sunset*1000).toLocaleTimeString("en-GB").slice(0,5);
      })
      .catch(error => {
        console.log(error);
      });
    },
    geolocation() {
      navigator.geolocation.getCurrentPosition(this.buildUrl, this.geoError);
    },
    buildUrl(position) {
      const lat = position.coords.latitude;
      const lon = position.coords.longitude;

      this.getWeather(API + '&lat=' + lat + '&lon=' + lon + KEY);
    },
    geoError(error) {
      this.getWeather(API + '&lat=0&lon=0' + KEY);
    }
  },
  beforeMount() {
    this.geolocation();
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

这是一个关于如何使用 VueJs 的地理定位 API 的非常简短的教程。我决定写这篇教程的原因是因为我有很多问题试图让它在我的[报价和天气登录页面](https://github.com/FabioRosado/landing-page)上工作，所以希望这能在你遇到困难时帮助你。

最后，我们可能应该改变应用程序，让它显示当前位置，以便我们的用户知道他们得到了哪些天气数据。我将把这个挑战留给你来完成——如果你添加了位置，你可能也需要修改一下 CSS。

*注意:你可以[在这里获取更新后的 main.js 文件。](https://github.com/FabioRosado/fabiorosado.github.io/tree/master/assets/files/weather-tutorial%20part%20II)T3】*