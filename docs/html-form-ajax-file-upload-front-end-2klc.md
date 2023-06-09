# HTML 表单 Ajax 文件上传(前端)

> 原文：<https://dev.to/imben1109/html-form-ajax-file-upload-front-end-2klc>

本文首发于[https://medium . com/self-learning/html-form-Ajax-file-upload-front-end-6199 c 4004003](https://medium.com/self-learning/html-form-ajax-file-upload-front-end-6199c4004003)

# 简介

HTML 表单对于 Web 应用程序来说非常重要，因为它旨在从用户那里收集数据。这将是非常容易的，如果 HTML 格式只是用传统的方法，让浏览器后端脚本来处理你传递的数据。如果你选择 Ajax 和 HTML 格式的文件控制，相对来说会非常困难。

*   HTML 表单
*   HTML 表单(文件控件)
*   HTML 表单(Ajax)
*   HTML 表单(文件控制和 Ajax)

# HTML 表单

HTML 表单对于 Web 应用程序来说非常重要，因为它旨在从用户那里收集数据。

```
<form action="{URL}" method="POST">
  <input type="text"/>
  <button type="submit">Submit</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

您可以选择 POST 或 GET 作为方法。默认编码是 application/x-www-form-urlencoded。它将使所有的字符在发送前被编码(空格被转换成“+”，特殊字符被转换成 ASCII 十六进制值)。

# HTML 表单(文件控件)

```
<form enctype="multipart/form-data" action="{URL}" method="POST">
  <input type="text"/>
  <input type="file"/>
  <button type="submit">Submit</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

enctype 指定提交给服务器时表单数据的编码方式。

# HTML 表单(Ajax)

```
<script>
  function doSubmit(){
    var input1 = document.getElementById("input1");
    var request = new XMLHttpRequest();
    request.open('POST', "http://localhost:8080/testSimpleRequest");
    request.setRequestHeader('content-type', 'application/json');
    request.send({
      "input1": input1
    });
  }
</script>
<form>
  <input type="text" id="input1"/>
  <button type="button" onclick="doSubmit()">Submit</button>
<form> 
```

Enter fullscreen mode Exit fullscreen mode

在现代 Ajax 调用中，HTTP 请求一般使用 JSON，而不是 application/x-www-form-urlencoded。

# HTML 表单(文件控件和 Ajax)

```
<script>
  function doSubmit(){
    // Form Data
    var formData = new FormData();

    var fileSelect = document.getElementById("fileSelect");
    if(fileSelect.files && fileSelect.files.length == 1){
     var file = fileSelect.files[0]
     formData.set("file", file , file.name);
    }

    var input1 = document.getElementById("input1");
    formData.set("input1", input1.value)
    // Http Request  
    var request = new XMLHttpRequest();
    request.open('POST', "http://localhost:8080/testMultipart");
    request.send(formData);
  }
</script>
<form>
  <input type="text" id="input1"/>
  <input type="file" id="fileSelect"/>
  <button type="button" onclick="doSubmit()">Submit</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

FormData JavaScript 对象用于为 XMLHttpRequest (Ajax)发送表单数据。FormData 将接受参数的文件、blob 和字符串。

备注:对于 FormData，append()和 set()方法略有不同。set()会用新值覆盖所有现有值，而 append()会将新值追加到 FormData 中，不管该键是否存在。

# 总结

[![Summary](img/244ccb5148843683e0d2b192b935d587.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xtC4aHoS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yi2ndgq7o0f6h3ksxg8y.png)