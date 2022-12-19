# 组可写的错误权限— Laravel

> 原文：<https://dev.to/yemiwebby/bad-permissions-writeable-by-group--laravel-fm5>

<figure>[![](../Images/4ab583bc754011064e5f37ce544e0a85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P9_oksum--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/844/1%2AgUG4hDtn-Qvi5nMDcnJmMQ.jpeg) 

<figcaption>内部服务器错误</figcaption>

</figure>

如果你曾经在部署应用程序，尤其是 Laravel 应用程序到共享主机时遇到过这个错误，那么你就找到了正确的帖子。

因此，您只是将应用程序部署到服务器，而不是呈现内容，迎接您的是内部服务器错误。

(500)内部服务器错误可能由许多实例引起，包括但不限于无效的权限、无效的所有权、php.ini 或。htaccess 文件、脚本中的无效请求和其他。

一般来说，这不是服务器本身的问题，通常可以通过修改站点配置来解决。最好的方法是检查 cPanel 中的错误日志以获得特定的信息。

因此，如果错误日志显示目录可由组写入:

这表明该文件夹对某个文件夹的权限设置过高。要纠正这一点，需要将权限从 777 更改为 755。

目录和文件夹应该是 755。cgi-bin 文件夹中的可执行脚本必须是 755。图像、媒体和 HTML 之类的文本文件应该是 644。

*   文件— 644
*   CGI 脚本— 755
*   目录— 755

您可以使用文件管理器修改权限，该文件管理器位于 FTP 客户端 cPanel 的“文件”类别中，或者使用 SSH/Bash 中的 **chmod** 命令。

### **终注**

这个错误不是 Laravel 应用程序特有的，我只是把它作为我的标题，因为我在上传一个用 Laravel 构建的应用程序时遇到了这个错误。

我希望这篇文章对你有用，如果你有不同的方法来解决这样的问题，请在下面分享你的想法和评论。