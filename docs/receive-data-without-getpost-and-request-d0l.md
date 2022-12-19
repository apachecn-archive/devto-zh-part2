# 在没有$_GET、$_POST 和$_REQUEST 的情况下接收数据

> 原文：<https://dev.to/sandeepkamboj12/receive-data-without-getpost-and-request-d0l>

在 php 中，也有一些方法或功能可以用来接收数据，而不需要 GET、POST 和 REQUEST。

也就是

```
$input_date_from_client = file_get_contents('php://input'); 
```

Enter fullscreen mode Exit fullscreen mode