# 使用 react-路由器 v4 的谷歌分析

> 原文：<https://dev.to/nodefiend/google-analytics-with-react-router-v4-1eo>

*图片由:[https://unsplash.com/@davidclode](https://unsplash.com/@davidclode)T3】*

问题是:

Google analytics 需要一个页面 URL 来跟踪 GA 仪表板中的分析。单页应用程序只有模拟过渡，并不像经典网页那样改变 URL。

解决方案是:

使用 React-router，我们可以访问强大的[历史 API](https://developer.mozilla.org/en-US/docs/Web/API/History) ,它使用浏览器来操纵 URL，使其看起来和运行起来像一个真实的页面更改。

来源:

[如何让谷歌分析在单页应用中发挥作用(SPA)](http://tech.webinterpret.com/how-to-make-google-analytics-work-in-a-single-page-application-spa/)

**T2`index.html`**

在`index.html`页面的标题中，加载脚本以加载 google analytics

```
 <script>  
    (function (i, s, o, g, r, a, m) {
        i['GoogleAnalyticsObject'] = r; i[r] = i[r] || function () {
            (i[r].q = i[r].q || []).push(arguments)
        }, i[r].l = 1 * new Date(); a = s.createElement(o),
        m = s.getElementsByTagName(o)[0]; a.async = 1; a.src = g; m.parentNode.insertBefore(a, m)
    })(window, document, 'script', '//www.google-analytics.com/analytics.js', 'ga');
</script> 
```

**T2`app.js`**

在`componentDidMount()`上的`app.js`内部创建一个 GA 脚本实例，并将其附加到窗口，这样我们就可以随时访问它。

```
 componentDidMount(){

    window.ga('create', 'UA-XXXXXXX-X', 'auto');

} 
```

**T2`app.js`**

创建一个方法，接收一个`url`并设置页面视图，然后将视图发送到 google analytics:

```
 setPageAndSendToGA = url => {
    window.ga('set', 'page', url);
    window.ga('send', 'pageview');
  }; 
```

接下来，创建一个接收由`this.props.location`提供的位置的方法。

构建一个条件，每当传入一个特定的 URI 路径时触发我们的`setPageAndSendToGA()`。

```
...

  trackGoogleAnalytics = location => {
    if (window.ga) {
      if (location.pathname == '/sign-up') {
        let url = location.pathname;
        this.setPageAndSendToGA(url);
      }

      if (location.search.length == 10) {

        // here we are using search Params, so we concat it to the url string

        let url = location.pathname + location.search;
        this.setPageAndSendToGA(url);
      }
    }
  };

... 
```

**app.js**

然后在`render()`中我们调用这个函数，它将在每次页面`location`改变时触发

```
 render(){

 this.trackGoogleAnalytics(this.props.location);

} 
```

查看源代码中列出的文章，要获得更详细的解释，这只是我为我们的应用程序使用的改编实现。

巨魔我:

*   可能有一个更好的时间来运行这个函数，而不是渲染。对于我们的用例，这个渲染在`app.js`的最上面的组件中

*   可能有更好的方法来监听`browserRouter`对象中的页面变化，而不是使用`props.location`

*   请在下面的评论中批评我，这样我就可以学习了！或者对文章进行调整。