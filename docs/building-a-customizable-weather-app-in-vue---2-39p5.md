# 在 Vue - 2 中构建可定制的天气应用程序

> 原文：<https://dev.to/raymondcamden/building-a-customizable-weather-app-in-vue---2-39p5>

前几天我分享了一个可定制的 Vue.js 天气 app 的初稿([在 Vue.js](https://dev.to/raymondcamden/building-a-customizable-weather-app-in-vuejs-2d5i-temp-slug-2599576) 中构建一个可定制的天气 App)。昨天我有一些时间来更新，我想我应该分享我的进展。

在初稿结束时，我的代码会获取你的位置，然后使用雅虎天气 API 获取天气预报。它没有做的是更新背景以匹配天气。为了支持这一点，我提出了下面的过程。

首先，我将采用天气类型，这是雅虎使用的一个代码，并将其“折叠”成几个特定的类型。雅虎支持近 50 种不同类型的天气，从热带风暴到沙尘。我认为将各种类型简化成一个更小的列表是有意义的。理论上，你可以用另一个服务替换 Yahoo，只要你返回相同的核心类型，那么剩下的代码就可以正常工作。

第二，我想让你(你是使用我的代码为你自己的个性化应用程序的人)可以很容易地提供图像。为了支持这一点，您可以按天气类型指定图像，如下所示:

```
images = {
    type1: [array of images],
    type2: [array of images]
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用数组，我们可以随机选择一个来保持事情有点意思，这样你就不会每次都看到相同的图片。

最后——为了让您更容易理解——我支持一个“包罗万象”的图像桶，如果代码找不到特定天气类型的图像，就可以使用它。或者拍摄，也许你不关心寻找特定类型天气的图像，而只是想要你孩子的随机照片，不管天气如何。这里有一个例子:

```
images = {
    "rain": ["kids_rain1.jpg", "kids_rain2.jpg"],
    "snow": ["snow_in_louisiana.jpg"],
    "catchall": [
        "kidsa.jpg",
        "kidsb.jpg",
        "kidsc.jpg"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这个，因为它很简单，也让你可以偷懒。见鬼，你甚至可以只用一张图片。让我们来看看更新后的代码。

```
const app = new Vue({
    el:'#app',
    data:{
        loading:true,
        lat:null,
        lon:null,
        location:null,
        temp:null,
        temp_low:null,
        temp_high:null,
        images:{
            "rain":[
                "clouds.jpg"
            ],
            "catchall":[
                "clouds.jpg"
            ]
        },
        selectedImage:null
    },
    created() {

        navigator.geolocation.getCurrentPosition(pos => {
            console.log('got coordinates', pos.coords);
            this.lat = pos.coords.latitude;
            this.lon = pos.coords.longitude;
            this.loadWeather();
        });

    },
    methods:{
        loadWeather() {

            axios.get(`https://query.yahooapis.com/v1/public/yql?q=select%20*%20from%20weather.forecast%20where%20woeid%20in%20(SELECT%20woeid%20FROM%20geo.places%20WHERE%20text%3D%22(${this.lat}%2C${this.lon})%22)&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys`)
            .then(res => {
                let weather = res.data.query.results.channel;
                console.log('response',weather);

                this.location = weather.location.city + ", " + weather.location.region;
                this.temp = weather.item.condition.temp;
                this.temp_low = weather.item.forecast[0].low;
                this.temp_high = weather.item.forecast[0].high;
                this.desc = weather.item.condition.text;

                //get the proper image
                this.selectedImage = this.getWeatherImage(weather);
                console.log(this.selectedImage);
                //reach out to the DOM, bad ray
                /*
                document.body.style.background = `url(${this.selectedImage})`;
                document.body.style['background-image'] = `
                linear-gradient(
                    rgba(1,0,0,0.5),
                    rgba(0,0,0,0.5)
                ), url(${this.selectedImage});`;
                */
                document.body.style.background = `url(${this.selectedImage})`;

                this.loading = false;

            })
            .catch(e => {
                console.error(e);
            });

        },
        getWeatherImage(d) {
            /*
            Alrighty then - so to be clear, this is VERY specific for Yahoo. Yahoo supports (https://developer.yahoo.com/weather/documentation.html)
            49 unique weather codes. We're going to use some logic to break them down into a smaller subset. So for example, fair(day) and fair(night) will just be fair. blowing snow, snow, flurries, etc will be snow. In theory, what we simplify down to could be a set list such that if
            we switch to another service, we still return the same core results. In theory.

            Note that I expect some people may not like the 'groupings' I made. Change it how you will! :)
            Some of these I just completely punted on, like smokey and dust
            */

            let weather = '';
            let code = d.item.condition.code;
            console.log('weather code is '+code);

            if(code >= 0 && code <= 4) weather = 'storm';
            if(code >= 5 && code <= 12) weather = 'rain';
            if(code >= 13 && code <= 16) weather = 'snow';
            if(code === 17 || code === 18) weather = 'rain'; // hail and sleet
            //todo: 19 dust
            if(code >= 20 && code <= 22) weather = 'foggy';
            if(code >= 23 && code <= 24) weather = 'windy';
            //todo: 25 cold (seriously - cold is a condition?)
            if(code >= 26 && code <= 30) weather = 'cloudy';
            if(code >= 31 && code <= 36) weather = 'clear'; // this include 36=hot
            if(code >= 37 && code <= 39) weather = 'storm';
            if(code === 40) weather = 'rain';
            if(code >= 41 && code <= 43) weather = 'snow';
            if(code === 44) weather = 'cloudy';
            if(code === 45) weather = 'storm';
            if(code === 46) weather = 'snow';
            if(code === 47) weather = 'storm';
            console.log('weather is '+weather);
            /*
            Ok, so now the logic is this.

            If the user specified this.images[TYPE], we expect it to be an an array and we 
            select a random one.

            Otherwise, we look for this.images.catchall, also an array, and pick randomly.
            */
            if(this.images[weather]) {
                return this.images[weather][getRandomInt(0, this.images[weather].length)];
            } else {
                return this.images['catchall'][getRandomInt(0, this.images['catchall'].length)];
            }
        }
    }

});

//Thank you, MDN
function getRandomInt(min, max) {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min)) + min; //The maximum is exclusive and the minimum is inclusive
} 
```

Enter fullscreen mode Exit fullscreen mode

上面重要的部分是`getWeatherImage`。你可以看到我将雅虎的多种天气类型转换成一个更简单的列表的逻辑。最后，我只是选择一个随机的图像。最后一个改动是更新背景:

```
document.body.style.background = `url(${this.selectedImage})`; 
```

Enter fullscreen mode Exit fullscreen mode

您可以在那里看到一些注释掉的代码。我用了一些 CSS 来使图像变暗，但是我不能动态更新 URL。我结束了撑船。如果有人能帮我解决这个问题，我会很高兴的。

你可以在这里找到这个版本的代码:[https://github . com/cfjedimaster/web demos/tree/master/vue weather/v2](https://github.com/cfjedimaster/webdemos/tree/master/vueweather/v2)

下一步是什么？嗯，理论上，我可以添加对多个位置的支持。我正在考虑，但是最后一个帖子没有得到任何评论，所以我不确定是否有人真的喜欢这个。我完全没意见(我写得很开心！)但是我不确定我会深入下去，除非有一些兴趣。让我知道你的想法。