# 获取数据的初学者指南(AJAX，Fetch API & Async/Await)

> 原文：<https://dev.to/bjhaid_93/beginners-guide-to-fetching-data-with-ajax-fetch-api--asyncawait-3m1l>

## 简介

在本教程中，我将解释如何使用 AJAX、Fetch API 和 Async/Await 等 web 技术从外部 API 异步获取数据。

## 如何从外部 API 获取数据

### **阿贾克斯**

AJAX 代表 Asynchronous Javascript 和 XML，它是一套从客户端或服务器异步发送和接收数据的 web 技术，它在后台完成，不需要重新加载网页，JSON(Javascript 对象表示法)实际上已经取代了 XML(可扩展标记语言)，大多数 API 都返回 JSON 数据，AJAX 也可以返回纯文本。

### 下面是对 AJAX 工作方式的描述

[![a](img/e1461c0a14bd0323ca1a95eb014566d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xgVyrrP4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/azyz8o8wd62fn84zhxsk.gif) 
[请求](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)通过 AJAX 调用发送，JSON 格式的数据从服务器异步获取，页面内容更新无需重新加载您的网页，我们可以从本地机器或服务器获取数据，公共 API。
我将在下面的代码中演示如何从 [Github API](https://api.github.com/users) 获取数据，这是一个使用 AJAX 的外部 API。

```
 //Create the XHR Object
    let xhr = new XMLHttpRequest;
    //Call the open function, GET-type of request, url, true-asynchronous
    xhr.open('GET', 'https://api.github.com/users', true)
    //call the onload 
    xhr.onload = function() 
        {
            //check if the status is 200(means everything is okay)
            if (this.status === 200) 
                {
                    //return server response as an object with JSON.parse
                    console.log(JSON.parse(this.responseText));
        }
                }
    //call send
    xhr.send();
    //Common Types of HTTP Statuses
    // 200: OK
    // 404: ERROR
    // 403: FORBIDDEN 
```

Enter fullscreen mode Exit fullscreen mode

下面是数据
[![a](img/f7005a16f65c80bb4fde90ed4e8c7948.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ZfXmq2---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b5o4tjuhnatrc7vb1dhy.png)

### **获取 API**

它是处理 [HTTPRequest](https://www.w3schools.com/xml/xml_http.asp) 的最新标准，它是窗口对象的一部分，我们也可以很容易地从外部 API 获取数据。Fetch 返回[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
我将在下面的代码中演示如何从 [Github API](https://api.github.com/users) 获取数据，这是一个带有 Fetch API 的外部 API。

```
 //call the fetch function
    fetch('https://api.github.com/users')
    .then(res => res.json())//response type
    .then(data => console.log(data)); //log the data; 
```

Enter fullscreen mode Exit fullscreen mode

下面是数据
[![](img/2c2cee8e0bc7175054b925a0a0682527.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QOL2-fh_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h3d0gvpvp9zlbupe2jo0.png)

### **异步/等待**

它是 ES7 标准的一部分，现在完全在 Chrome 中实现，我们将 async 添加到函数中，它返回一个[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。
我将在下面的代码中演示如何从 [Github API](https://api.github.com/users) 获取数据，这是一个带有 Async/Await 的外部 API。

```
 async function getData() 
        {
            //await the response of the fetch call
           let response = await fetch('https://api.github.com/users');
            //proceed once the first promise is resolved.
           let data = await response.json()
            //proceed only when the second promise is resolved
            return data;
        }
//call getData function
getData()
.then(data => console.log(data));//log the data 
```

Enter fullscreen mode Exit fullscreen mode

下面是数据
[![a](img/904d3192c952bcf1eb3b69df658cc95a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pZlIDC_j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c28cudlld8qsnzx109ur.png)

### **注**

这三种方法中的任何一种都可以用于获取数据，我实际上更喜欢使用 Fetch API，除了这三种方法之外，还有几种方法(axios、superagent e.t.c ),在本文中不讨论，API 是不同的，请求的获取方式和响应的提供方式也不同，我们为外部 API 提供了文档来指导您。我希望你喜欢这个教程，感谢阅读。