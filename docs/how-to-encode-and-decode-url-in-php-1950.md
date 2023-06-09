# 如何在 PHP 中编码和解码 URL

> 原文：<https://dev.to/nilan/how-to-encode-and-decode-url-in-php-1950>

这篇文章解释了如何使用 PHP 对 URL 进行编码和解码。PHP 通过提供一些内置函数来支持 URL 的编码和解码。在将 URL 数据发送到查询字符串或可能动态处理此 URL 数据的函数之前，需要进行编码。然后，在目标 PHP 页面或函数中接收到数据后，这些数据将被解码成原始形式。

## **PHP 编解码 URL 示例**:

首先，让我们创建一个 HTML 文件，并将其保存为`sample.html`

**sample.html **T2】

```
<form method="post" action="encode_decode.php">
               <input type="text" name="url" placeholder="Enter URL To Encode">
               <input type="submit" name="encode_url" value="ENCODE">
            </form><form method="post" action="encode_decode.php">
               <input type="text" name="url" placeholder="Enter URL To Decode">
               <input type="submit" name="decode_url" value="DECODE">
            </form> 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们创建一个 PHP 文件来编码和解码 URL。

**encode_decode.php**

```
<?php if(isset($_POST['encode_url']))
   {
    $url = $_POST['url'];
    $encodedUrl = urlencode($url);
    $converted_url=$encodedUrl;
   }   

   if(isset($_POST['decode_url']))
   {
    $url = $_POST['url'];
    $decodedUrl = urldecode($url);
    $converted_url=$decodedUrl;
   }
?> 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看用 PHP 编码和解码 URL 的简单例子:

```
$url = "https://www.example.com/p/selenium.html";
//encoding URL
$encodedUrl = urlencode($url);
echo $encodedUrl;
//Prints: https%3A%2F%2Fwww.example.com%2Fp%2Fselenium.html

//decoding URL
echo urldecode($encodedUrl);
//Prints: https://www.example.com/p/selenium.html 
```

Enter fullscreen mode Exit fullscreen mode

这都是关于在 PHP 中编码和解码 URL。感谢您阅读这篇文章，如果您有任何问题，对这篇文章有其他更好的有用的解决方案，请在评论区写下您的留言。

帖子[如何在 PHP](https://stacktips.com/tutorials/php/encode-and-decode-url-in-php) 中编码和解码 URL 最早出现在 [Stacktips](https://stacktips.com) 上。