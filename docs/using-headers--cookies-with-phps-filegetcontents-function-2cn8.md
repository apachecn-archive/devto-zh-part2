# 在 PHP 的 file_get_contents()函数中使用头文件和 Cookies

> 原文：<https://dev.to/jake/using-headers--cookies-with-phps-filegetcontents-function-2cn8>

> 这个解决方案最初是由一位同事联系到我的，所有的荣誉都归于这条推文。

[![image](../Images/a16a4774fad7e0e0a6a71e2d6eae6f36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZJ8L15Xn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s3pgwxogb92n2r4py2fh.png)

您知道使用流上下文，您可以在使用 php 的 file_get_contents()函数发出 HTTP 请求时设置头吗？

```
$context = stream_create_context([
    "http" => [
        "method" => "GET",
        "header" => "Accept-languange: en\r\n" .
        "Cookie: foo=bar\r\n"
    ]
]);

$file = file_get_contents('https://example.com', false, $context); 
```

Enter fullscreen mode Exit fullscreen mode