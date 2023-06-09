# 用 Vue 构建桌面应用程序:电子

> 原文：<https://dev.to/n_tepluhina/building-a-desktop-app-with-vue-electron-3pl>

在我的[上一篇文章](https://dev.to/vuevixens/building-a-desktop-app-with-vue-vuido-490n)中，我描述了使用 [Vuido](https://vuido.mimec.org/) 用 [Vue](https://vuejs.org/) 框架构建一个桌面应用程序。它是一个很棒的库，可以用本地平台组件创建快速小型的桌面应用程序，但是它也有自己的缺点，比如没有 CSS 样式或图像支持。现在是时候尝试[电子](https://electronjs.org/)构建桌面 app 了。

> Electron 是 GitHub 开发的一个开源库，用于使用 HTML、CSS 和 JavaScript 构建跨平台的桌面应用程序。Electron 通过将 [Chromium](https://www.chromium.org/Home) 和 [Node.js](https://nodejs.org/en/) 组合成一个单一的运行时来实现这一点，应用程序可以打包用于 Mac、Windows 和 Linux

为了快速开始，我使用了由 [Greg Holguin](https://github.com/SimulatedGREG) 制作的 [electron-vue](https://github.com/SimulatedGREG/electron-vue) 样板。它为开发人员提供了 vue-cli 搭建、公共 vue 插件、打包程序、测试、开发工具和其他功能。

## 💻我们要建造什么

我们将构建与上一篇文章相同的应用程序:一个基于 [OpenWeatherMap API](https://openweathermap.org/api) 的应用程序，用于查看用户选择的城市的天气。

如果你只想检查最终的电子驱动应用程序代码，这里是。

## 🛠️安装

electron-vue 样板文件是作为 [VueCLI](https://github.com/vuejs/vue-cli) 2.x 的模板构建的，包括定制应用程序的选项。所以需要全球安装:

```
npm install -g vue-cli 
```

Enter fullscreen mode Exit fullscreen mode

如果你更喜欢使用 VueCLI 的最新版本，你也需要安装一个全局桥:

```
npm install -g @vue/cli @vue/cli-init 
```

Enter fullscreen mode Exit fullscreen mode

然后初始化你的项目:

```
vue init simulatedgreg/electron-vue weather-app 
```

Enter fullscreen mode Exit fullscreen mode

这将启动一个安装项目，在此过程中您需要做出几个选择。

[![Installation](img/c6a2bcc2a529478c212abaae66ab272c.png)T2】](https://i.giphy.com/media/Wy6PkAug1qc4jfg9uq/giphy.gif)

酷的是如果你需要一些常用的插件和库比如 [axios](https://github.com/axios/axios) ，你可以在安装过程中挑选。

几乎所有的选择都很清楚，但有一个问题🤔：

[![Electron packager choice](img/f0eb47527596846667cb6d337a63de70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QDyu_IL0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vZlBez2.png)

我决定谷歌一下，在 StackOverflow 上找到了[这个有用的线索](https://stackoverflow.com/questions/37113815/electron-builder-vs-electron-packager)。根据它的说法，`electron-builder`对我来说似乎是一个更好的选择，所以我就跟着它走了。

项目设置好之后，你需要进入应用文件夹，运行`npm install`或`yarn install`，我们就可以开始了。

## 🔦了解应用程序结构

安装完成后，你可以看到在`src`文件夹中有两个文件夹:`main`和`renderer`。电子主流程需要第一个

> 根据 electron-vue 文档，在 electron 中，运行`package.json`的主脚本的进程被称为主进程。在主进程中运行的脚本可以通过创建网页来显示 GUI。

在`main`文件夹中有两个文件:`index.js`和`index.dev.js`。第一个是你的应用程序的主文件，在这个文件中`electron`启动。它也用作 webpack 的生产入口文件。所有主要流程工作都应从这里开始。

`index.dev.js`专用于安装`electron-debug` & `vue-devtools`时的开发。开发应用时不需要接触它。

`renderer`流程需要`renderer`文件夹。

> 由于 Electron 使用 Chromium 来显示网页，因此也使用了 Chromium 的多进程架构。电子版中的每个网页都运行在自己的进程中，这个进程被称为渲染器进程。

正如你可能注意到的，这是一个“普通”的 Vue 应用程序结构，有`assets`和`components`文件夹、`main.js`和`App.vue`文件。下面是后者的结构:

```
<template>
  <div id="app">
    <landing-page></landing-page>
  </div>
</template>

<script>
  import LandingPage from '@/components/LandingPage'

  export default {
    name: 'weather-app',
    components: {
      LandingPage
    }
  }
</script>

<style>
  /* CSS */
</style> 
```

Enter fullscreen mode Exit fullscreen mode

如果您尝试运行`dev`任务，您将得到以下结果:

[![Start app template](img/20d85cadda0127506f62c8274d33c2d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dN5mrGdh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ir6uLce.jpg)

所以有一个`landing-page`组件和 devtools 被打开。现在我们可以开始改变它了！

## 🕹️搭起一个 app

与 Vuido 不同，电子驱动的应用程序是用 HTML 标签构建的，而不是本地组件。所以我们将制作一个类似于通常的 web 应用程序的结构，并用 CSS 对其进行样式化。

> 注意:我没有故意安装任何 CSS 框架或组件库:我想在不添加任何不同依赖项的情况下比较包的大小。两个项目中唯一使用的库是 [axios](https://github.com/axios/axios) 。

第一步是去掉`landing-page`组件。然后我添加了一个简单的输入框和一个按钮:

```
<div id="app">
    <p>Enter the city name to check current weather in it</p>
    <section class="weather-input">
      <input type="text" v-model="query">
      <button :disabled="!query.length">Check</button>
    </section>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的应用程序看起来是这样的:

[![App with input only](img/862d21e1413f422e7239677891a56695.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RLeJMCLU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/LjIcTcu.png)

我们在数据中有一个`query`属性来处理用户输入，我们将用这个查询作为参数进行 API 调用。

## 🔗进行 API 调用

我使用了 [OpenWeatherMap 当前天气 API](https://openweathermap.org/current) 。它给了你很多不同的信息，你可以在这里查看 JSON 响应的例子。

在安装过程中，我们已经将`axios`包含到我们的应用程序中。我们来看看`src/renderer/main.js` :

```
import Vue from 'vue';
import axios from 'axios';
import App from './App';

if (!process.env.IS_WEB) Vue.use(require('vue-electron'));
Vue.http = Vue.prototype.$http = axios;
Vue.config.productionTip = false; 
```

Enter fullscreen mode Exit fullscreen mode

所以我们可以在组件实例中使用 axios 方法作为`this.$http`！这里我们唯一要添加的是 API 调用的基本 URL:

```
axios.defaults.baseURL = 'http://api.openweathermap.org/data/2.5'; 
```

Enter fullscreen mode Exit fullscreen mode

现在，在`App.vue`中，我们将创建一组数据属性来显示不同的天气数据:

```
data() {
    return {
      query: '',
      error: false,
      city: '',
      country: '',
      weatherDescription: '',
      temp: null,
      tempMin: null,
      tempMax: null,
      humidity: null,
      icon: '',
    };
}, 
```

Enter fullscreen mode Exit fullscreen mode

与 Vuido 版本相比，我增加了一个额外的属性，它是一个`icon`。API 提供了一个天气图标，但我们不能在 Vuido 应用程序中使用它，因为目前不支持显示图像。

让我们也创建一个获取数据的方法:

```
methods: {
  showWeather() {
    this.$http
      .get(`/weather?q=${this.query}&units=metric&&appid=${API_KEY}`)
      .then(response => {
        this.city = response.data.name;
        this.country = response.data.sys.country;
        this.weatherDescription = response.data.weather[0].description;
        this.temp = response.data.main.temp;
        this.tempMin = response.data.main.temp_min;
        this.tempMax = response.data.main.temp_max;
        this.humidity = response.data.main.humidity;
        this.icon = `http://openweathermap.org/img/w/${
          response.data.weather[0].icon
        }.png`;
        this.error = false;
      })
      .catch(() => {
        this.error = true;
        this.city = '';
      });
  },
}, 
```

Enter fullscreen mode Exit fullscreen mode

并添加到我们按钮的点击回调:

```
<button :disabled="!query.length" @click="showWeather">Check</button> 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您在输入字段中输入文本并点击按钮，您可以在`Network`选项卡中观察到 API 调用:

[![API call response](img/ebe56ff30ae44dd1f0a09b170b3ba024.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qCJELojf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1YyIUHN.png)

## 💅显示天气数据

让我们将这些数据添加到模板:

```
<template>
  <main id="app">
    <p>Enter the city name to check current weather in it</p>
    <section class="weather-input">
      <input type="text" v-model="query">
      <button :disabled="!query.length" @click="showWeather">Check</button>
    </section>
    <section v-if="error" class="weather-error">
      There is no such city in the database
    </section>
    <section v-if="city.length" class="weather-result">
      <h1>{{city}}, {{country}}</h1>
      <p><em>{{weatherDescription}}</em></p>
      <div class="weather-result__main">
        <img :src="icon" alt="Weather icon">
        <div class="weather-result__temp">
          {{temp}}&deg;C
        </div>
      </div>
      <div class="weather-result__details">
        <p>Min: {{tempMin}}&deg;C</p>
        <p>Max: {{tempMax}}&deg;C</p>
        <p>Humidity: {{humidity}}%</p>
      </div>
    </section>
  </main>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我们的应用视图:

[![App without styling](img/0fd2c518e8dc3a19436399c708cf8388.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GvTTWxN---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YQgdkOB.png)

太棒了，我们可以看到真实的天气了！但是看起来像是 1999 年...让我们给它添加一些 CSS 魔法(实际上，*很多*的 CSS 魔法)！

```
<style lang="scss">
* {
  margin: 0;
  padding: 0;
}
html,
body,
#app {
  height: 100%;
}

#app {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 16px;
  padding: 10px;
  background: rgb(212, 228, 239);
  background: -moz-radial-gradient(
    center,
    ellipse cover,
    rgba(212, 228, 239, 1) 0%,
    rgba(134, 174, 204, 1) 100%
  );
  background: -webkit-radial-gradient(
    center,
    ellipse cover,
    rgba(212, 228, 239, 1) 0%,
    rgba(134, 174, 204, 1) 100%
  );
  background: radial-gradient(
    ellipse at center,
    rgba(212, 228, 239, 1) 0%,
    rgba(134, 174, 204, 1) 100%
  );
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#d4e4ef', endColorstr='#86aecc',GradientType=1 ); /* IE6-9 fallback on horizontal gradient */
}

.weather-input {
  display: flex;
  align-items: center;
  padding: 20px 0;
}

.weather-result {
  text-align: center;
  &__main {
    display: flex;
    align-items: center;
    justify-content: center;
    padding-top: 5px;
    font-size: 1.3rem;
    font-weight: bold;
  }
  &__details {
    display: flex;
    align-items: center;
    justify-content: space-around;
    color: dimgray;
  }
}

.weather-error {
  color: red;
  font-weight: bold;
}

input {
  width: 75%;
  outline: none;
  height: 20px;
  font-size: 0.8rem;
}

button {
  display: block;
  width: 25%;
  height: 25px;
  outline: none;
  border-radius: 5px;
  white-space: nowrap;
  margin: 0 10px;
  font-size: 0.8rem;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们还有功能齐全的应用程序:

[![App ready](img/c497c40794a80528c21cc7faf0c160a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j1-cMhAB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/TepfQD2.jpg)

打包之前要做的最后一件事是缩小窗口大小。如果我们检查一个`src/main/index.js`文件，我们可以找到它的设置:

```
mainWindow = new BrowserWindow({
    height: 563,
    useContentSize: true,
    width: 1000
}) 
```

Enter fullscreen mode Exit fullscreen mode

让我们改变宽度为`450`和高度为`250`

## 📦包装

好消息:您可以将您的应用程序构建为 web 应用程序！如果您运行一个`build:web`任务，您将在一个`dist`文件夹中找到一个构建 web 应用程序。

但是让我们回到桌面应用程序，运行`build`任务。因此，在`build`文件夹(对我来说是`mac`)中，你会有一个以你的平台命名的文件夹，并且里面有一个应用程序文件。和它的大小...哇，133 Mb！

[![App size](img/9ea9104bbc53dd8dd8845adbe87916fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kJIscIWg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OTCGUdQ.png)

这么小的应用就很多了！此外，如果你尝试运行它，你会发现它的启动速度比 Vuido 驱动的应用程序慢一些。

最终外观:

[![Final look](img/55f4f5f0e070509c669476b0e4d5581f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0kcvMxfC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cKq2mOB.png)

## 🌟结论

**优点:**

*   易于开始
*   好医生
*   提供 web 应用程序构建
*   可以通过 CSS 样式定制

**缺点**

*   非常大的包装尺寸
*   比用本地 GUI 组件构建的应用程序要慢

如果您的应用需要独特的外观，并且您不太关心封装尺寸和性能，那么 Electron-vue 是一个不错的选择。

## 更新

如果你的网络应用是用 Vue CLI 3 构建的，你可以简单地用 [Vue CLI 插件电子构建器](https://nklayman.github.io/vue-cli-plugin-electron-builder/guide/)把它变成一个桌面应用。你只需要在你的项目根文件夹中运行以下命令:

```
vue add electron-builder 
```

Enter fullscreen mode Exit fullscreen mode

完成后，你将有两个额外的 npm 任务:`serve:electron`和`build:electron`来使用桌面应用程序。