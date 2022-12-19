# 使用代理时，file_get_contents 依赖于以前的调用

> 原文：<https://dev.to/c33s/filegetcontents-depend-on-previous-calls-when-using-a-proxy-1l83>

非常有趣的是，如果你连续做两个`file_get_contents`，如果你使用代理服务器，第二个依赖于第一个。

运行以下脚本:

```
<?php
$stream_default_opts = array(
  'http'=>array(
    'proxy'=>"tcp://5.9.78.28:3128",
    'request_fulluri' => true,
  )
);

stream_context_set_default($stream_default_opts);

file_get_contents("https://www.google.com", false); 
file_get_contents("https://getcomposer.org", false); 
```

Enter fullscreen mode Exit fullscreen mode

导致:

```
PHP Warning:  file_get_contents(https://www.google.com): failed to open stream: HTTP request failed! HTTP/1.0 404 Not Found
 in foo.php on line 21
PHP Warning:  file_get_contents(): Peer certificate CN=`getcomposer.org' did not match expected CN=`www.google.com' in foo.php on line 22
PHP Warning:  file_get_contents(https://getcomposer.org): failed to open stream: Cannot connect to HTTPS server through proxy in foo.php on line 22 
```

Enter fullscreen mode Exit fullscreen mode

忽略 404 部分，它只是我在这个演示中使用的公共代理。我在使用我的代理(当然不是 404)时得到错误(对等证书错误)

*   首次发布于:[https://www . Reddit . com/r/PHPhelp/comments/8ai 694/file _ get _ contents _ depend _ previous _ calls _ when/](https://www.reddit.com/r/PHPhelp/comments/8ai694/file_get_contents_depend_on_previous_calls_when/)
*   [https://stackoverflow.com/a/49717338](https://stackoverflow.com/a/49717338)
*   错误报告:[https://bugs.php.net/bug.php?id=76196](https://bugs.php.net/bug.php?id=76196)
*   Gratisography 的封面图片[https://www . pexels . com/photo/woman-dropped-fail-failure-4091/](https://www.pexels.com/photo/woman-dropped-fail-failure-4091/)