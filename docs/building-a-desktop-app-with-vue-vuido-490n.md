# 用 Vue 构建桌面应用:Vuido

> 原文：<https://dev.to/n_tepluhina/building-a-desktop-app-with-vue-vuido-490n>

**更新:** [用电子构建 Vue 桌面 app](https://dev.to/vuevixens/building-a-desktop-app-with-vue-electron-3pl)

我爱 [Vue](https://vuejs.org/) 。这是一个很好的框架，可以让你构建很棒的 web 应用程序。但真正神奇的是，你不仅仅局限于网络。你可以简单地使用 [Weex](https://weex.incubator.apache.org/) 或 [NativeScript-Vue](https://nativescript-vue.org/) 制作原生移动应用；但是也有一种方法可以构建桌面应用程序。为此可以选择[电子](https://electronjs.org/)或 [Vuido](https://vuido.mimec.org/) 库。在这篇文章中，我将描述 Vuido 方法，在下一篇文章中，我将尝试用 electronic 构建相同的应用程序。

> Vuido 是一个基于 Vue.js 创建本地桌面应用的框架，vue . js 由 micham ciński 创建。使用 Vuido 的应用程序可以在 Windows、OS X 和 Linux 上运行，使用原生 GUI 组件，并且不需要电子。
> 
> 在幕后，Vuido 使用了为每个桌面平台提供原生 GUI 组件的 [libui](https://github.com/andlabs/libui) 库，以及 Node.js 的 [libui-node](https://github.com/parro-it/libui-node) 绑定

## 💻我们要建造什么

例如，我们将制作一个简单的应用程序来检查您选择的城市的当前天气。我们将使用 [OpenWeatherMap](https://openweathermap.org/api) API 来获取实际数据。

如果你只想检查最终代码，这里是[这里是](https://github.com/NataliaTepluhina/Vue-Desktop-App/tree/vuido)。

## 🛠️安装

正如 Vuido 文档中所述，开始开发桌面应用程序有一些先决条件。对于不同的平台，它们是不同的:

### 💡Windows 操作系统

*   `windows-build-tools`
*   `Visual C++ Redistributable Package for Visual Studio 2013`

### 💡Linux 操作系统

*   `build-essential`
*   `GTK+ 3​`

### 💡OSX

*   `Xcode`

我使用 OSX 进行开发，现在我已经安装了 Xcode。

另外，你还需要安装 [vue-cli](https://cli.vuejs.org/) (如果你使用的是 Vue CLI 3，你还需要`@vue/cli-init`)。

要创建一个新项目，运行以下命令:

```
vue init mimecorg/vuido-webpack-template my-project 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，你可以在你的`src`文件夹中找到`MainWindow.vue`组件，代码如下:

```
<template>
  <Window title="some-app" width="400" height="100" margined v-on:close="exit">
    <Box>
      <Text>Welcome to your Vuido application!</Text>
    </Box>
  </Window>
</template>

<script>
export default {
  methods: {
    exit() {
      this.$exit();
    },
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

如果您依次运行`build`和`start`任务，您将会看到一个窗口，显示非常基本的桌面应用程序:

[![Screenshot1](img/db072d1438fbbe7410ac4a94d207290d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yw09BS6P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/zGGTPyv.png)

现在我们准备构建一些更有趣的东西😅

## 💅搭建应用程序

关于 Vuido，你需要知道的第一件事是它使用了本地组件。所以既没有我们熟悉的 HTML 标签，也没有 CSS 样式——只有一组与不同桌面平台兼容的原生 GUI 组件。用 Vuido 构建的应用程序将在每个平台上都具有原生的外观和感觉。

这可能被认为是有利也有弊——你不能构建一个具有非常定制的外观的应用程序，但是它将比用 electronic 构建的应用程序更加轻量级，并且工作速度更快。

内置组件的完整列表可在 Vuido 文档的本节中找到。

我最初的想法是构建一个应用程序来显示用户选择的城市的天气，这样我就可以测试简单的用户交互和 API 调用。我首先需要的是一个带按钮的输入框。另外，我将窗口大小改为`400x150px` :

```
<Window title="Weather" width="400" height="150" margined v-on:close="exit">
    <Box padded>
        <Box horizontal padded>
        <TextInput stretchy></TextInput>
        <Button>Search</Button>
    </Box>
    </Box>
</Window> 
```

Enter fullscreen mode Exit fullscreen mode

要将输入字段与按钮水平对齐并在它们之间添加填充，我们需要一个具有`horizontal`和`padded`属性的`<Box>`容器。`Box`类似于 HTML `div`，它作为一个包装器来包含和对齐组件。

`TextInput`是一个输入字段，它的`stretchy`属性意味着它将拉伸以填充可用空间。

现在我们的应用程序是这样的:

[![Screenshot2](img/731393b696ffbf3b089195d25c0aab78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---vfa3h6m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/PFjEZnD.png)

让我们为组件数据添加一个`query`属性，并将其设置为输入字段的`v-model`。此外，当没有查询字符串时，我们需要禁用按钮，这对我来说很棘手，因为我已经尝试了一个熟悉的`disabled`属性——但在 Vuido 中，你应该使用 [`enabled`](https://vuido.mimec.org/usage/common-attributes) one！所以现在我们的输入框看起来像这样:

```
<Box horizontal padded>
    <TextInput v-model="query" stretchy></TextInput>
    <Button :enabled="!!query">Search</Button>
</Box> 
```

Enter fullscreen mode Exit fullscreen mode

## 🔗进行 API 调用

现在的想法是用一个给定的查询字符串作为一个城市来获取当前的天气状况。

为了获取天气数据，我使用了 [OpenWeatherMap](https://openweathermap.org/api) API。它提供了很多不同的东西，但我们只需要第`Current weather data`章。你可以点击查看 JSON 响应[的例子。](https://samples.openweathermap.org/data/2.5/weather?q=London,uk&appid=b6907d289e10d714a6e88b30761fae22)

所以，为了开始获取数据，我们需要添加一个 axios 库:

```
npm install --save axios 
```

Enter fullscreen mode Exit fullscreen mode

然后导入并设置一个基础 URL 和 OpenWeatherMap API 关键变量:

```
import axios from 'axios';
axios.defaults.baseURL = 'http://api.openweathermap.org/data/2.5'
const apiKey = process.env.API_KEY; 
```

Enter fullscreen mode Exit fullscreen mode

在这之后，让我们为天气数据添加一些新的属性和一个从 API 获取它的方法:

```
export default {
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
    };
  },
  methods: {
    exit() {
      this.$exit();
    },
    showWeather() {
      axios
        .get(
          `/weather?q=${this.query}&units=metric&&appid=${apiKey}`,
        )
        .then(response => {
          this.city = response.data.name;
          this.country = response.data.sys.country;
          this.weatherDescription = response.data.weather[0].description;
          this.temp = response.data.main.temp;
          this.tempMin = response.data.main.temp_min;
          this.tempMax = response.data.main.temp_max;
          this.humidity = response.data.main.humidity;
          this.error = false;
        })
        .catch(() => {
          this.error = true;
          this.city = '';
        });
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候为按钮附加一个新方法，并更改模板以显示所有给定的数据，或者如果查询不匹配任何现有的城市，则显示一个错误

```
<Window title="Weather in your city" width="400" height="150" margined v-on:close="exit">
    <Box padded>
    <Box horizontal padded>
        <TextInput stretchy v-model="query"/>
        <Button :enabled="!!query" @click="showWeather">Search</Button>
    </Box>
    <Separator horizontal/>
    <Group margined>
        <Box padded>
          <Text v-if="error">There is no such city in the database</Text>
          <Box v-if="!!city">
            <Box padded horizontal>
              <Text stretchy>{{city}}, {{country}}</Text>
              <Text>{{temp}}&deg;C</Text>
            </Box>
            <Text>{{weatherDescription}}</Text>
            <Separator horizontal/>
            <Box padded horizontal>
              <Text stretchy>Min: {{tempMin}}&deg;C</Text>
              <Text stretchy>Max: {{tempMax}}&deg;C</Text>
              <Text stretchy>Humidity: {{humidity}}%</Text>
            </Box>
          </Box>
        </Box>
    </Group>
    </Box>
</Window> 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，第一个盒子是我们在前一章中创建的输入容器。下面有一个`Separator`——一条水平线，在视觉上分隔小部件。接下来是`Group`——这是一个容器，它为内容提供了一个边框和标题。

在`Group`中，您可以看到已经见过的组件组合:`Text`用于简单的文本内容，`Box`作为容器，`Separator`。现在应用程序看起来是这样的:

[![Screenshot3](img/8d66557c50d0b53a4ddb1e41681021f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3_Q_5V3t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/jNwLyGy.png)

## 📦包装

在我看来，打包 Vuido 驱动的应用程序的最好和最简单的方法是库作者推荐的方法。他推荐使用自己的库 [LaunchUI](https://github.com/mimecorg/launchui) 和 [LaunchUI Packager](https://github.com/mimecorg/launchui-packager) 来打包应用程序并分发给最终用户。

我已经在全球范围内安装了 LaunchUI 打包程序:

```
npm install --global launchui-packager 
```

Enter fullscreen mode Exit fullscreen mode

然后，我在应用程序根文件夹中运行了以下命令:

```
launchui-packager weather-app 1.0 dist/main.min.js 
```

Enter fullscreen mode Exit fullscreen mode

在上面的命令中，`weather-app`是应用程序名，`1.0`是版本，`dist/main.min.js`是捆绑文件的路径。

在这之后一个文件夹与我的申请！包的大小是 39Mb，比作者承诺的大了一点(文档中提到了大约 20Mb ),但也不算大。

[![Screenshot4](img/4d59b8f214cdd10218e5a9cf78f9548f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z_6wLyjs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/2dbgk9L.png)

如果你尝试运行它，你可能会注意到它开始真的很快(0.1 秒左右)。

## 🌟结论

**优点:**

*   易于构建
*   与电子供电的应用程序相比，提供较小的封装尺寸
*   有案可稽

**缺点**

*   没有酷的款式😕
*   仍未发布(当前版本为 0.2.0)

如果你需要一个具有基本外观的快速小应用程序，Vuido 看起来是一个不错的选择。它有一个清晰的文档，将来内置组件的列表可能会增加。