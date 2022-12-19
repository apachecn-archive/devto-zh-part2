# 带有数据或参数的本地 Javascript AJAX POST 请求

> 原文：<https://dev.to/kuntoaji/native-javascript-ajax-post-request-with-data-or-parameters-233k>

如果我们不关心来自服务器的响应，带有数据/参数的原生 Javascript AJAX POST 请求非常简单。

```
var request = new XMLHttpRequest();
request.open('POST', '/example', true);
request.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded; charset=UTF-8');
request.send(data); 
```

变量是 XHR 请求中要发送的数据体。这可以是:

*   文档，在这种情况下，它在发送之前被序列化。
*   BodyInit，根据 Fetch 规范，它可以是 Blob、BufferSource、FormData、URLSearchParams、ReadableStream 或 USVString 对象。

我试着用 [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) object 发送数据，但是它不能与我的 Sinatra 应用程序一起工作。所以我使用 [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) 来发送数据。

下面是我的带有参数的本地 Javascript AJAX POST 请求的例子。

```
var mydata = document.getElementById('mydata');
var myresponse = document.getElementById('myresponse');
var data = 'mydata=' + mydata.value;

var request = new XMLHttpRequest();
request.open('POST', 'http://example.com/mypath', true);
request.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded; charset=UTF-8');

request.onreadystatechange = function() {
  if (this.readyState == XMLHttpRequest.DONE && this.status == 200) {
    console.log('succeed');
    myresponse.value = request.responseText;
  } else {
    console.log('server error');
  }
};

request.onerror = function() {
  console.log('something went wrong');
};

request.send(data); 
```

更多信息:

*   [XMLHttpRequest.send()](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/send)
*   [格式数据](https://developer.mozilla.org/en-US/docs/Web/API/FormData)
*   [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)