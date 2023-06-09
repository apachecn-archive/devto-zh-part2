# 用 VanillaJS 构建一个轻量级的互联网检测功能

> 原文：<https://dev.to/ojanti/build-a-lightweight-internet-detect-feature-with-vanillajs--2jep>

点击查看现场演示[。
Github 上的完整代码](https://ojanti.github.io/Internet-Detect-Feature-with-Javascript/)[这里](https://github.com/Ojanti/Internet-Detect-Feature-with-Javascript)。
最初发布于[我的博客](http://ojanti.com/blog/Build-a-lightweight-Internet-Detect-Feature-with-VanilaJS)。

我最近开发了一个应用程序，主要由西非某些地区的客户使用。在测试该应用程序时，我们收到了一些关于它不能正常工作的投诉。经过进一步调查，我们注意到提出的大多数问题是由于互联网连接失败或不良。软件的顺利运行很大程度上依赖于稳定的互联网。

因此，在构建 autosave 或 temporary storage 等更强大的功能之前，我的任务是实现一个轻量级的互联网检测功能，该功能可以检查互联网连接，并在出现故障/恢复以及两者的后果时通知用户。主要要求是:

1.  它必须是轻量级的，并且可以快速实现
2.  不应该因为 1 而难看(没有 javascript 提醒 lol)
3.  它应该具有最小的依赖性，甚至没有依赖性(超出 web 应用程序的基本/常规需求)

我决定使用 VanillaJS，一旦应用程序的任何视图运行，它将首先执行。我将 ping 远程服务器上的一个文件，并检查响应 HTTP 状态。如果没有成功，即 HTTP 状态码> = 200 & < 304\. [引用这里的](http://httpstatuses.com/)，那么我会通知用户，并在检查的同时提供一些建议。如果最终恢复了互联网连接，应用程序会通知用户这一情况。

下面是核心函数:

```
var wasInternetConnectionBad = '';      //used to show restored internet notification only once
var internetCheckActions = function() {
    var xhr = new XMLHttpRequest();
    var file = "/img/nKekEaz4i6jeRtvxZRnY.jpg";     // the remote file to check for. You should replace this with yours
    var randomNum = Math.round(Math.random() * 10000);      //initiating a random value to  revv the file

    xhr.open('HEAD', file + "?rand=" + randomNum, true);
    xhr.send();

    xhr.addEventListener("readystatechange", processRequest, false);

    function processRequest(e) {
        if (xhr.readyState == 4) {
            if (xhr.status >= 200 && xhr.status < 304) {
                if (wasInternetConnectionBad == "1") {
                    //  Internet connection is GOOD. Do what ever you like here
                }
                wasInternetConnectionBad = '';      //Clear flag
            } else {
                //  Internet connection is BAD. Do what ever you like here
                wasInternetConnectionBad = '1';
            }
        }
    }
} 
```

我决定在 DOM 内容加载时调用这个函数，并且每隔 5 秒调用一次。你可以随心所欲地改变它

```
 // Invoke when DOM content has loaded
document.addEventListener('DOMContentLoaded', function () {
    internetCheckActions();
});

//& every subsequent 5 seconds
var checkInternetConnection = function() {
    setInterval(function () {
        internetCheckActions();
    }, 5000);
}
checkInternetConnection(); 
```

上面的代码基本上就是你需要的。您可以到此结束，或者继续查看更完整的解决方案。

下一步是找出如何以一种好的方式通知用户事情的状态。这是我通常调用一个奇特的 Jquery 通知插件的地方，但是我们不能这样做。所以我构建了一个非常轻量级且可扩展的通知小部件。酷的是，它可以用于其他实用功能。

首先让我们为通知创建 HTML 标记。理想情况下，它应该作为 BODY 标签的第一个子标签插入，并作为页面内容的宿主元素的兄弟。

```
<img src="https://cdnjs.cloudflare.com/ajax/libs/slippry/1.4img/sy-loader.gif" width="1" height="1"
     style="position:absolute; z-index:-2;"> <!-- Attepmting to preload an animated loader image which will be used later. NOTE, different browser behave differently  -->
<div class="rawNotificationArea" id="rawNotificationArea">
    <div class="notification_message"></div>
</div> 
```

然后，在 head 标记中内联添加这个 CSS 片段，以样式化我们的通知小部件

```
<style>
    .rawNotificationArea {
        position: fixed;
        top: 2px;
        left: 0;
        width: 100%;
        text-align: center;
        padding: 10px 0;
        display: none;
        z-index: 99999999;
    }

    .rawNotificationArea .notification_message {
        max-width: 50%;
        border: solid thin #888888;
        color: #333;
        background-color: #DCDCDC;
        text-align: center;
        padding: 5px 15px;
        border-radius: 4px;
        box-shadow: 2px 3px 20px rgba(0, 0, 0, 0.17);
        display: inline-block;
        text-align: center;
        font-size: 14px;
        letter-spacing: 1px;
    }

    .rawNotificationArea .notification_message.warning {
        background-color: #fcf8e3;
        border-color: #faf2cc;
        color: #8a6d3b;
    }

    .rawNotificationArea .notification_message.success {
        background-color: #dff0d8;
        border-color: #d0e9c6;
        color: #3c763d;
    }

    .rawNotificationArea .notification_message.info {
        background-color: #d9edf7;
        border-color: #bcdff1;
        color: #31708f;
    }

    .rawNotificationArea .notification_message.danger, .rawNotificationArea .notification_message.error {
        background-color: #f2dede;
        border-color: #ebcccc;
        color: #a94442;
    }
</style> 
```

和小部件的 JS。

```
// Notification Widget
var nativeNotification = {
    fadeEl: function() {
        return (document.getElementById('content_body'));
    },
    messageHolder: function() {
        return (document.getElementById('rawNotificationArea'));
    },
    contentFade: function() {
        this.fadeEl().style.opacity = "0.5";
    },
    contentUnfade: function() {
        this.fadeEl().style.opacity = "1.0";
    },
    notify: function(message, tone) {
        this.messageHolder().innerHTML = '<span class="notification_message ' + tone + '">' + message + '</span>';
        this.messageHolder().style.display = "block";
    },
    unotify: function() {
        while (this.messageHolder().firstChild) {
            this.messageHolder().removeChild(this.messageHolder().firstChild);
        }
    },
    timedUnotify: function(time) {
        setTimeout(function() {
            nativeNotification.unotify();
        }, time);
    }
}; 
```

所以在我们的核心函数中使用它，我们最终会得到这样的东西

```
 //Detect internet status amd motify user
var wasInternetConnectionBad = '';      //used to show restored internet notification only once
var internetCheckActions = function() {
    var xhr = new XMLHttpRequest();
    var file = "/img/nKekEaz4i6jeRtvxZRnY.jpg"; // the remote file to check for. You should replace this with yours
    var randomNum = Math.round(Math.random() * 10000); //initiating a random value to  revv the file

    xhr.open('HEAD', file + "?rand=" + randomNum, true);
    xhr.send();

    xhr.addEventListener("readystatechange", processRequest, false);

    function processRequest(e) {
        if (xhr.readyState == 4) {
            if (xhr.status >= 200 && xhr.status < 304) {
                if (wasInternetConnectionBad == "1") {
                    nativeNotification.contentUnfade();
                    nativeNotification.notify("Your internet connection has been restored", 'info');
                    nativeNotification.timedUnotify(5000);
                }
                wasInternetConnectionBad = '';
            } else {
                nativeNotification.contentFade();
                nativeNotification.notify("We've detected a problem with your internet connection.\n Some functions may not work as expected<div><strong>Retrying <img src='https://cdnjs.cloudflare.com/ajax/libs/slippry/1.4img/sy-loader.gif' width='20' height='20'></strong></div>", 'warning');
                wasInternetConnectionBad = '1';
            }
        }
    }
} 
```

点击查看现场演示[。
Github 上的完整代码](https://ojanti.github.io/Internet-Detect-Feature-with-Javascript/)[这里](https://github.com/Ojanti/Internet-Detect-Feature-with-Javascript)。

页（page 的缩写）S

1.  这是权宜之计。我期待着人们可以改进这一点的有趣方式，甚至更好、更有效的解决方案
2.  可能有待改进的地方包括*-将它构建成一个易于使用的 JS 插件*-尝试其他方法来加速用于检查连通性的文件。当前该文件正在使用 querystring 进行修订。在此查看更多信息[这里](http://www.stevesouders.com/blog/2008/08/23/revving-filenames-dont-use-querystring/)
3.  不确定，但我假设这里使用的方法(即使用 web 客户端 ping 一个文件以重复调用静态文件)是[独立的](http://www.restapitutorial.com/lessons/idempotency.html)..我也期待着这方面的有趣对话