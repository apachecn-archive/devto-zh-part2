# Vue.js 和 Chart.js 天气 API 示例

> 原文：<https://dev.to/changoman/vuejs-and-chartjs-weather-api-example-1e7>

[![](../Images/9f443bf3d5857a6efbae450f4a422348.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jY65Ej4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/am3zlaek3rv73oyjf2pf.jpg)

*本帖最初发表于[codebushi.com](https://codebushi.com/vuejs-with-chartjs-api-example/)T3】*

最近，有人要求我在现有的 Wordpress 网站上添加一些精美的图表。在过去，对于这样的任务，我会选择 jQuery 或 Angular.js，但我决定尝试流行的 [Vue.js](https://vuejs.org/) 。Vue.js 是一个渐进式 javascript 框架，这意味着将其添加到现有网站并开始使用非常容易。对于本教程，我将从一个简单的 HTML 样板文件开始，并在底部的一个`<script>`标签中添加 Vue.js。我们将点击 OpenWeatherMap API 来获取一些天气数据，并用 Chart.js 绘制温度图表。

如果你是 Vue.js 的新手，我强烈建议你先浏览一下他们的[介绍指南](https://vuejs.org/v2/guide/)。它涵盖了一些重要的基础知识和 Vue 应用程序的结构。

#### [Vue.js 和 Chart.js 天气 API 示例](http://vuejs-chartjs.surge.sh/) <small>( [查看来源](https://github.com/codebushi/vuejs-chartjs-example) )</small>

在我们开始之前，我想指出 Vue.js 也有一个 [CLI](https://cli.vuejs.org/) ，它的工作方式类似于 React 的 Create-React-App。Vue CLI 从一开始就非常适合用 Vue.js 构建的 JavaScript 应用。然而，出于我们的目的，我们将把 Vue 添加到“现有站点”,不需要使用 CLI。这也意味着我们不需要担心任何 Webpack 配置或构建/部署命令。

我们现有的 HTML 文件如下所示:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="ie=edge">
    Vue.js Example With Chart.js
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.2/css/bootstrap.min.css" integrity="sha384-Smlep5jCw/wG7hdkwQ/Z5nLIefveQRIY9nfy6xoR1uRYBtpZgI6339F5dgvm/e9B" crossorigin="anonymous">
  </head>
  <body>
    <div id="app">
      <div class="container">
        <div class="my-5">
          <form>
            <div class="row">
              <div class="col-md-6 offset-md-3">
                <h5>Enter A City:</h5>
                <div class="input-group">
                  <input type="text" class="form-control">
                  <div class="input-group-append">
                    <button class="btn btn-outline-secondary" type="submit">Submit</button>
                  </div>
                </div>
              </div>
            </div>
          </form>
        </div>
        <div class="my-5">
          <div class="alert alert-info">
            Loading...
          </div>
          <div>
            <canvas id="myChart"></canvas>
          </div>
        </div>
      </div>
    </div>
  </body>
</html> 
```

这是一个简单的 HTML 文件，带有一些引导网格和表单元素。想象这是任何一个你需要工作的现有网站。

### 添加 Vue.js 和 Chart.js

为了使用 Vue.js 和其他 JavaScript 工具，我们需要将它们的`<script>`标签添加到页面中。如果你可以访问网站的整个模板，这些可以添加在标签之后。如果你不想在你站点的每个页面上加载这些脚本，它们可以被添加到你正在处理的页面的底部。

```
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.13.0/moment.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.18.0/axios.min.js"></script>
<script src="main.js"></script> 
```

在这里，我们加载 Vue.js、Moment.js 进行日期/时间格式化，Chart.js、Axios 进行 API 请求，main.js 包含我们自己的代码。注意，这些脚本是一个接一个加载的，这并不理想。使用 Vue CLI 的好处之一是能够捆绑所有必要的 JavaScript 文件并优化加载，但我们在使用旧网站时并不总是有这种奢侈。

### 使用 Axios 从 API 获取天气数据

这个应用程序的第一步是从 OpenWeatherMap API 获取一些天气数据。我选择使用 [5 天预测](https://openweathermap.org/forecast5)，因为它在免费层上可用。您可以免费注册 OpenWeatherMap 来获得自己的 appid 密钥，每个查询结束时都需要它。

在我们的`main.js`中，我们将初始化我们的 Vue 应用程序，并创建一个获取数据的方法。

```
var app = new Vue({
  el: "#app",
  data: {
    chart: null,
    city: "",
    dates: [],
    temps: [],
    loading: false,
    errored: false
  },
  methods: {
    getData: function() {

      this.loading = true;

      axios
        .get("https://api.openweathermap.org/data/2.5/forecast", {
          params: {
            q: this.city,
            units: "imperial",
            appid: "Your OpenWeatherMap Key here"
          }
        })
        .then(response => {
          console.log(response)
        })
        .catch(error => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => (this.loading = false));
    }
  }
}); 
```

`getData`方法包含我们将要进行的 Axios 查询。我们需要的唯一缺少的字段是`this.city`,我们需要从表单中得到它。让我们将 HTML 与这个 Vue 应用程序连接起来。

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="ie=edge">
    Vue.js Example With Chart.js
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.2/css/bootstrap.min.css" integrity="sha384-Smlep5jCw/wG7hdkwQ/Z5nLIefveQRIY9nfy6xoR1uRYBtpZgI6339F5dgvm/e9B" crossorigin="anonymous">
  </head>
  <body>
    <div id="app">
      <div class="container">
        <div class="my-5">
          <form v-on:submit.prevent="getData">
            <div class="row">
              <div class="col-md-6 offset-md-3">
                <h5>Enter A City:</h5>
                <div class="input-group">
                  <input type="text" class="form-control" v-model="city">
                  <div class="input-group-append">
                    <button class="btn btn-outline-secondary" type="submit">Submit</button>
                  </div>
                </div>
              </div>
            </div>
          </form>
        </div>
        <div class="my-5">
          <div class="alert alert-info" v-show="loading">
            Loading...
          </div>
          <div v-show="chart != null">
            <canvas id="myChart"></canvas>
          </div>
        </div>
      </div>
    </div>
  </body>
  <script src="https://cdn.jsdelivr.net/npm/vue"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.13.0/moment.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.18.0/axios.min.js"></script>
  <script src="main.js"></script>
</html> 
```

正如你所看到的，大部分的 HTML 没有改变。唯一需要添加的部分是各种`v-`属性。让我们快速检查一下变化。

```
<form v-on:submit.prevent="getData"> 
```

这一行将我们的表单挂接到我们的`getData`方法，并在表单提交时触发。表单提交的默认动作是重新加载页面，因此`.prevent`的行为类似于`event.preventDefault()`。

```
<input type="text" class="form-control" v-model="city"> 
```

这将输入字段绑定到我们的 Vue 应用程序的`data.city`，然后可以通过`this.city`在`getData`方法中访问。这就是我们如何根据用户输入来改变城市。

```
<div class="alert alert-info" v-show="loading">
Loading...
</div> 
```

部分是可选的，但是它有助于更好的用户界面体验。当应用程序处于加载状态时，它会显示加载警告，一旦加载完成，在查询成功获得数据后，它会隐藏。

有了这些内容，您可以尝试我们的查询。输入一个城市并提交表单。打开您的控制台，您应该会看到来自 API 的数据日志！

### 格式化 Chart.js 的数据

一旦我们得到了数据，我们就需要将它组织成数组供 Chart.js 使用。我们将有一个 x 轴的日期数组和一个 y 轴的温度值数组。

在我们的`main.js`中，将`console.log(response)`替换为以下内容:

```
.then(response => {
  this.dates = response.data.list.map(list => {
    return list.dt_txt;
  });

  this.temps = response.data.list.map(list => {
    return list.main.temp;
  });
}) 
```

这将我们的`data.dates`和`data.temps`设置为映射的数组。注意，我们在`.then()`中使用了一个箭头函数，它正确地绑定了`this.dates`和`this.temps`。如果您不能使用箭头功能，请务必在您的功能后添加`.bind(this)`。

### 用 Chart.js 绘制数据图表

一旦数据被格式化，我们可以初始化一个新的图表并插入数据。当使用 Chart.js 或任何其他图表库时，大部分的跑腿工作是正确地格式化图表。我强烈推荐浏览一下 [Chart.js 文档](http://www.chartjs.org/docs/latest/),查看它们的样本，看看有哪些不同的选项。根据所绘制的数据，可能需要调整工具提示和 X 或 Y 轴来显示数据的正确单位。

我已经为这个演示浏览并格式化了 Chart.js 选项，请随意查看这些选项并进行任何必要的更改。

我们最终的`main.js`文件现在看起来像这样:

```
var app = new Vue({
  el: "#app",
  data: {
    chart: null,
    city: "",
    dates: [],
    temps: [],
    loading: false,
    errored: false
  },
  methods: {
    getData: function() {
      this.loading = true;

      if (this.chart != null) {
        this.chart.destroy();
      }

      axios
        .get("https://api.openweathermap.org/data/2.5/forecast", {
          params: {
            q: this.city,
            units: "imperial",
            appid: "fd3150a661c1ddc90d3aefdec0400de4"
          }
        })
        .then(response => {
          this.dates = response.data.list.map(list => {
            return list.dt_txt;
          });

          this.temps = response.data.list.map(list => {
            return list.main.temp;
          });

          var ctx = document.getElementById("myChart");
          this.chart = new Chart(ctx, {
            type: "line",
            data: {
              labels: this.dates,
              datasets: [
                {
                  label: "Avg. Temp",
                  backgroundColor: "rgba(54, 162, 235, 0.5)",
                  borderColor: "rgb(54, 162, 235)",
                  fill: false,
                  data: this.temps
                }
              ]
            },
            options: {
              title: {
                display: true,
                text: "Temperature with Chart.js"
              },
              tooltips: {
                callbacks: {
                  label: function(tooltipItem, data) {
                    var label =
                      data.datasets[tooltipItem.datasetIndex].label || "";

                    if (label) {
                      label += ": ";
                    }

                    label += Math.floor(tooltipItem.yLabel);
                    return label + "°F";
                  }
                }
              },
              scales: {
                xAxes: [
                  {
                    type: "time",
                    time: {
                      unit: "hour",
                      displayFormats: {
                        hour: "M/DD @ hA"
                      },
                      tooltipFormat: "MMM. DD @ hA"
                    },
                    scaleLabel: {
                      display: true,
                      labelString: "Date/Time"
                    }
                  }
                ],
                yAxes: [
                  {
                    scaleLabel: {
                      display: true,
                      labelString: "Temperature (°F)"
                    },
                    ticks: {
                      callback: function(value, index, values) {
                        return value + "°F";
                      }
                    }
                  }
                ]
              }
            }
          });
        })
        .catch(error => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => (this.loading = false));
    }
  }
}); 
```

图表的大部分是由选项组成的，在它们被设置成你喜欢的样子后，从来没有真正改变过。发生变化的部分是`data.labels`和`data.datasets`。您可以看到，我们将`this.dates`和`this.temps`数组插入图表的这些区域。

我们还为新创建的图表设置了`this.chart`。注意 Axios 查询之前的这个新条件:

```
if (this.chart != null) {
  this.chart.destroy();
} 
```

这在不止一次查询城市时使用，它将在制作新图表之前销毁旧图表，以避免任何数据重叠。

就是这样！该应用程序现在可以根据你输入的城市获取数据，并绘制出气温图表。尝试使用不同的 Chart.js 选项，或者添加一个从 F 到 c 的切换。

#### [Vue.js 和 Chart.js 天气 API 示例](http://vuejs-chartjs.surge.sh/) <small>( [查看来源](https://github.com/codebushi/vuejs-chartjs-example) )</small>