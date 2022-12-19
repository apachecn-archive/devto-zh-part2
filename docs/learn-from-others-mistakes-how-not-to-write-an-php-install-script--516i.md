# 从别人的错误中学习:如何不写 PHP 安装脚本

> 原文：<https://dev.to/richard_klose/learn-from-others-mistakes-how-not-to-write-an-php-install-script--516i>

我最近在玩不同的图片库软件，主要是因为我在寻找一些可以在我的家庭网络中自己上传的东西。我以前使用苹果照片和 Lightroom，但这总是成为一个困难的部分，只要我想与我的妻子分享我的照片库，包括所有的标签，注释和元数据。我被 [koken](http://koken.me) 绊倒了，想试一试。

Koken 不是开源的，而是用 PHP 编写的，因此研究代码并了解其本质或多或少是很容易的。它所谓的[“Installer”](http://help.koken.me/customer/portal/articles/632102-installation)只是一个带有一些 HTML 和 JavaScript 的 PHP 脚本，所以我从那里开始。

“安装程序”文件的最后修改日期是 2017-02-24，这已经让我怀疑根据安全性它是否是最新的。往里面一看，我发现肯定不是。

# 过时的 jQuery (1.8.0)

安装过程的第一步是服务器兼容性检查。这是由 HTML 按钮点击触发的，它执行一个名为`test()`的 JavaScript 函数。这个函数使用 jQuerys `$.post()`函数来触发服务器兼容性检查的 PHP 部分。

```
...
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.8.0/jquery.min.js"></script>
... 
```

Enter fullscreen mode Exit fullscreen mode

```
function test() {
  ...
  $.post('index.php', {
    server_test: true,
    custom_magick_path: magick_path
    }, function(data) {
      ...
    }
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这里使用的 jQuery 版本是 1.8.0，它不仅已经有 6 年的历史，而且自 2018 年 1 月以来就有[个已知的安全漏洞](https://www.cvedetails.com/vulnerability-list/vendor_id-6538/product_id-11031/version_id-235566/Jquery-Jquery-1.8.0.html)。jQuery 的最新 1.x 版本在 2017-02-24(脚本的最后修改日期)是 [1.12.4](https://github.com/jquery/jquery/releases/tag/1.12.4) ，此时已经半年了。我希望至少有一个像 1.12.4 这样的非过时版本，但是由于这也有安全漏洞，应该使用更好的 jQuery > 3.0.0。

# 脚本本身必须可写

在服务器端，来自客户端的`server_test`变量由 PHP:
计算

```
if ($_POST)
{
  if (isset($_POST['database_check']))
  { ... }
  else if ($_POST['server_test'])
  {
    $current_dir = dirname(__FILE__);
    $writable = is_writable($current_dir) && is_writable(__FILE__);
    ...
    if (!$writable || (...))
    {
    $permissions['fail'][] = 'This directory does not have the necessary permissions to perform the install. Set the permissions on the koken folder and the koken/index.php file to 777.';
    }
    ...
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

所以这个脚本检查的第一件事是当前目录和脚本文件本身对于运行 web 服务器的用户是否是可写的。
尽管文件夹是可写的是完全有意义的，因为 koken 将被安装在这里，脚本本身肯定是不可写的。实施这一点意味着使用同一用户运行的任何进程都可以随时修改脚本。我们将在后面看到，所有的下载 URL 都被硬编码在脚本中，所以它们现在可以很容易地被修改并指向一些恶意的 URL。当然，这个脚本也可以被修改，以便用 PHP 做任何可能的事情。
我认为让脚本可写是必要的，因为它被称为`index.php`，以后可能会被 koken 的真实`index.php`所取代。一个更好的解决方案是给它另一个更具描述性的名字，比如`install.php`，并在安装后强迫用户手动删除它。这不仅可以确保安装脚本不可写，还可以帮助用户更好地理解这里发生了什么。

# 诡异下载

在`server_test`期间的下一步是下载 [PclZip](http://www.phpconcept.net/pclzip) (一个用于处理 Zip 文件的 PHP 库)。

```
list($download, $download_error) = download('https://s3.amazonaws.com/koken-installer/releases/pclzip.lib.txt', 'pclzip.lib.php'); 
```

Enter fullscreen mode Exit fullscreen mode

这真的很奇怪，因为`server_test`代码的目的应该是，测试服务器是否满足需求。但是为什么在任何真正相关的东西(例如 PHP 版本)被测试之前就下载这些东西呢？
也很奇怪，PclZip 不是从[官方发布下载](http://www.phpconcept.net/pclzip/pclzip-downloads)下载的，而是从 koken 的 aws 下载的。比较 koken 的版本和官方版本，可以发现它被修改了，但唯一的变化是删除了空行和文件末尾的结束 PHP 标记(`?>`)。
边注:他们这里用的 PclZip 版本(2.8)也过时了。这是值得注意的，因为下面的版本(2.8.1)包含了对 PHP 5.3 的修复，这是 koken 要求的最低 PHP 版本，所以在这里升级完全有意义。

# 诡异的下载启用了 MITM 攻击

通过深入研究前面看到的`download`函数，我发现了下面几行:

```
curl_setopt($cp, CURLOPT_FOLLOWLOCATION, true);
curl_setopt($cp, CURLOPT_SSL_VERIFYPEER, false); 
```

Enter fullscreen mode Exit fullscreen mode

这意味着下载 PclZip 的 HTTP 请求(以及可能在安装过程中稍后进行的其他下载)会跟随每个 HTTP 3xx 重定向，而不会验证响应的服务器的真实性。或者换句话说:“我会下载我在这个网址后面找到的任何东西，我不在乎你是谁，无论如何我都会拿走这个文件，即使你给我一些恶意的东西”。这为中间人攻击打开了方便之门。
尤其是现在，你可以[免费获得 SSL 证书](https://letsencrypt.org)，没有必要禁用这个验证。别这么做。

# 查看浏览器远远来不及

这不是一个安全问题，而是一个可用性问题。

```
... // the previous mentionend download of PclZip
$ua = strtolower($_SERVER['HTTP_USER_AGENT']);
... // several testing stuff here
if ( strpos($ua, 'msie') !== false || strpos($ua, 'internet explorer') !== false)
{
  $browser['fail'][] = 'The Koken beta does not currently support Internet Explorer. Please use Chrome, Safari or Firefox.';
} 
```

Enter fullscreen mode Exit fullscreen mode

不支持 Internet Explorer 也没关系，但是在客户端和服务器之间进行任何通信之前，浏览器中的一段 JavaScript 就已经可以完成这个测试了。这样，脚本已经下载了一些东西到你的服务器上，然后强迫你重新开始这个过程，仅仅是因为你使用了错误的浏览器。

# 更不安全的 HTTP(S)

稍后在`server_test` :

```
$loopback_test = koken_http_get('/json'); 
```

Enter fullscreen mode Exit fullscreen mode

```
function koken_http_get($url, $host_header = false)
{
  ...
  if ($protocol === 'https')
  {
    curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, 0);
    curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

说真的，koken 的人:请不要禁用 SSL 验证。这使得中间人攻击成为可能。 [取而代之获得免费的 SSL 证书](https://letsencrypt.org)，防止你的用户被黑客攻击。

# 结论

仅仅通过查看 koken 的安装脚本，我就对 koken 的人们如何处理安全问题有了一个初步的了解。他们似乎并不真正关心它。我发现:

*   他们使用带有已知错误和安全漏洞的过时依赖项。
*   他们强迫用户在他们的服务器上有一个可由运行 PHP 的用户写的 PHP 脚本，所以另一个 PHP 脚本可以修改它。
*   他们在知道 koken 是否会在服务器上运行之前就已经下载了。
*   他们在安装过程中禁止对下载的 SSL 证书进行验证。

这个脚本中还有更多的内容，但我会在这里停下来，因为这些是最让我怀疑的事情。

如果这是 koken 的人写软件的方式，我真的不推荐使用 koken。即使对摄影师来说这可能是一个不错的软件，但对你的服务器来说这是一个安全漏洞。

前几天我在推特上问过他们这件事，没有得到回应。

> ![Richard Klose profile image](../Images/47b239e69c5e4e27f5946baa685721ad.png)理查德·克洛泽[@理查德 _ 克洛泽](https://dev.to/richard_klose)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)[@ koken](https://twitter.com/koken)你为什么在你的生产安装脚本中禁用 https 的安全性？
> if($ protocol = = = ' https ')
> {
> curl _ setopt($ curl，CURLOPT_SSL_VERIFYHOST，0)；
> curl_setopt($curl，CURLOPT_SSL_VERIFYPEER，false)；
> }2018 年 6 月 08 日下午 14:12[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1005090137158627329)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1005090137158627329)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1005090137158627329)

> ![Richard Klose profile image](../Images/47b239e69c5e4e27f5946baa685721ad.png)理查德克洛泽[@理查德 _ 克洛泽](https://dev.to/richard_klose)![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)至今没有答案。看来 [@koken](https://twitter.com/koken) 不是死了就是他们不在乎安全。
> [twitter.com/richard_klose/…](https://t.co/NoRLW0TJnb)2018 年 6 月 13 日下午 17:40理查德·克洛泽[@理查德 _ 克洛泽](https://dev.to/richard_klose)@koken 你为什么在你的生产安装脚本中禁用 https 的安全性？if($ protocol = = = ' https '){ curl _ set opt($ curl，CURLOPT_SSL_VERIFYHOST，0)；curl_setopt($curl，CURLOPT_SSL_VERIFYPEER，false)；}【T29[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1006954359689760769)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1006954359689760769)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1006954359689760769)

In combination with a last modified date from more than a year ago and also no activity in their blog since a year I have truly no hopes that this situation will change.

我对所有开发人员的请求是:请多关心安全性，不要因为没有安全性而使你的(可能是好的)软件失败。