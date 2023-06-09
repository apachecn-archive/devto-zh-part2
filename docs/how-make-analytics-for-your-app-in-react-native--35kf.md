# 如何在 React Native 中对你的应用进行分析？

> 原文：<https://dev.to/gaserd/how-make-analytics-for-your-app-in-react-native--35kf>

嗨，我是谢尔盖，我喜欢地道的反应。

最近，我需要在 React Native 中为我的应用程序进行分析，我制作了小脚本。

1.  去[https://appmetrica . yandex . ru](https://appmetrica.yandex.ru)，是的这是俄罗斯的分析服务，但是他很酷。

2.  注册并创建“metrica”

3.  编写这段代码

```
const API_KEY = 'WRITE_THIS_YOUR_KEY'
const BOTAN_URL = 'https://api.botan.io/track';
const DEFAULT_NAME = 'Message';

function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
  //better you use really UID in your app
}

export default function sendEvent(message, name = DEFAULT_NAME) {
    return fetch(`${BOTAN_URL}/?token=${API_KEY}&name=${name}&uid=${getRandomInt(0,9999)}`, {
        headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json'
          },
          method: "POST",
          body: JSON.stringify(message)
    })
    .then(res => res.json())
    .then(data => {
      return data
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  搞定了。

如何使用这个功能？

```
componentDidMount() {
        getAllCategories()
        .then(data => {

            let categories = data.response.category

            sendEvent({},'loadCategories')

            this.setState({
                categories : categories,
                end_loading_categories : false
            })
        })
        .catch(() => {

            sendEvent({},'loadCategoriesError',() => {})

            this.setState({
                end_loading_categories : false
            })
        })
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是它在图表上的样子。

[![Charts](img/9b9eb2181b20b503ccf18dc60f5689ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VziOO1b8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vhkqdxxbs2kn5t9zybmx.png)