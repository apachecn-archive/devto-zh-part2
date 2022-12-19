# Node.js 将 JSON 发送到本地客户端

> 原文：<https://dev.to/binarydigit/nodejs-send-json-to-local-client-29cd>

嘿大家好！以下是我的设置和问题:

我已经创建了一个 Node.js/express 服务器来认证一个 API。我们使用的 API 需要通过 base64 编码的 HTTPS 进行基本授权。我已经通过使用 npm“请求”模块完成了这个任务。我进行了身份验证，并在请求响应的主体中获得了我期望的 JSON(万岁！).

我遇到的问题是，我如何将收到的 JSON 发送到本地服务器的客户端？我想获取这个 JSON，用 ajax 调用发送给我的本地客户机，并将其附加到我的 DOM 中，最终创建一个仪表板并做一些有趣的事情。

下面是一些 server.js 代码:

```
let request = require('request');
let username = 'username@email.com';
let password = 'password@123';

let options = {
  url: 'https://apiblah.com',
  auth: {
    user: username,
    password: password
  }
};

request(options, function (err, res, body) {
  if (err) {
    console.log(err)
    return
  }
  let jsonData = JSON.parse(body); 
  console.log(jsonData); <strong>//In my console, I do see the json I want to see, hooray! 
 But how do I get jsonData to send to client outside of request?</strong> 
});

// 

app.post('/', function(req, res){
    <strong>//I send request results to client side? unsure here</strong>
 });

app.get('/', function(req, res){   //render index.html
    res.render('index');
});

My client.js: 

$.ajax({
        url: "http://localhost:3000/",  // the local Node server
        method: 'GET',
        success: function(data){
            console.log(data); //display data in cosole to see if I receive it
        } 
```

Enter fullscreen mode Exit fullscreen mode

非常感谢！