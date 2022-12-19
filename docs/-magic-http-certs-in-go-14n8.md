# 🎩💫Go 中的神奇 HTTP 证书

> 原文：<https://dev.to/samthor/-magic-http-certs-in-go-14n8>

*更新:我已经发布了 [https-forward](https://github.com/samthor/https-forward) (你可以用 snap 在大多数 Linuxes】上安装它)，它透明地为内部‘哑’服务提供 https 证书。*

像许多人一样，我对 HTTPS/SSL 在 web 上无处不在的采用感到非常高兴。但这并非偶然——像 Let's Encrypt 这样的免费工具推动了证书的采用，而像 App Engine 这样的 PAAS(平台即服务)现在只是自动发出证书。

假设你正在编写自己的服务器。有一个包和习语，它会在你自己的代码中给你同样的体验。

> 如果你在前端运行一个为你处理 HTTPS 的网络服务器——就像 [App Engine Flex](https://cloud.google.com/appengine/docs/flexible/go/quickstart) 所做的那样，它只是要求你监听:8080— *这篇博文不是给你的*，你的提供商正在处理你的证书。
> 现在停止阅读！

* * *

您需要的软件包是 [`golang.org/x/crypto/acme/autocert`](https://godoc.org/golang.org/x/crypto/acme/autocert) ，而且使用起来非常简单。我们来看看如何:

```
 // add your listeners via http.Handle("/path", handlerObject)
    listener := autocert.NewListener("yourdomain.com")
    log.Fatal(http.Serve(listener, nil)) 
```

Enter fullscreen mode Exit fullscreen mode

## 加长版

但是有几个原因可能会让您自己指定配置。稍微长一点的设置类似于:

```
 certManager := autocert.Manager{
        Prompt:     autocert.AcceptTOS,
        HostPolicy: autocert.HostWhitelist("yourdomainname.com"),
        Cache:      autocert.DirCache("cache-path"),
    }
    server := &http.Server{
        Addr: ":https",
        TLSConfig: &tls.Config{
            GetCertificate: certManager.GetCertificate,
                        NextProtos:     []string{acme.ALPNProto},
        },
    }
    // add your listeners via http.Handle("/path", handlerObject)
    log.Fatal(server.ListenAndServeTLS("", "")) 
```

Enter fullscreen mode Exit fullscreen mode

<small>(完整的源代码你可以下载并运行，[看这里⤵️💻](https://gist.github.com/samthor/5ff8cfac1f80b03dfe5a9be62b29d7f2) )</small>

不管采用什么方法，您的服务器都将运行在端口 443 上(这个*有*发生:进程在这个端口上回调您)，并自动与 Let's Encrypt 对话以提供证书。

如果您遇到问题:

*   确保域被正确配置为指向您的服务器，并且记住您不能只运行`wget localhost`——您需要指定完整的域
*   对于额外的域名(如一个“ [www .](http://www.)“前缀)，只需将它们添加到`NewListener`或`HostWhitelist`即可

为了加分，你还应该收听普通的老式 HTTP。`autocert`包提供了一个内置的助手，将用户重定向到 HTTPS:

```
 go func() {
        h := certManager.HTTPHandler(nil)
        log.Fatal(http.ListenAndServe(":http", h))
    }() 
```

Enter fullscreen mode Exit fullscreen mode

这两个处理程序是我如何为我的测试域服务的。☕🍨

* * *

# 洞穴

如果您直接在自己的机器上托管自己的软件(虚拟化或未虚拟化)，那么有必要列出一些关于 web 服务器的注意事项和想法。

## 用旧围棋版本建造

Ubuntu 16.04 附带 1.6 版本。从 2018 年 4 月起，`autocert`需要更高的版本(你会得到关于缺少`context`的错误)。

安装稍后 Go [的说明在这里](https://github.com/golang/go/wiki/Ubuntu)。

## 监听系统端口

在*nix 上，如果您想监听端口 80 和 443，您的 Go 二进制文件需要以特权用户的身份运行(例如`root`)。这通常是个坏主意。

您可以使用`setcap`来赋予您的二进制文件特权。每次构建`server`时，您都需要授予`CAP_NET_BIND_SERVICE`功能，这允许二进制文件监听系统端口(0-1024):

```
sudo setcap CAP_NET_BIND_SERVICE+ep server 
```

Enter fullscreen mode Exit fullscreen mode

任何运行这个二进制文件的用户现在都被允许监听正确的端口，例如，你可以以`nobody`的身份运行你的二进制文件。

## 缓存需要可写

`autocert.Manager`使用的缓存文件夹不能在用户之间共享(这对测试来说是一个挑战)，它的内部错误消息也不是很好。

我倾向于对每个用户使用一致的缓存路径。所以基于当前用户名生成一个路径，而不是硬编码:

```
import (
    "path/filepath"
    "os"
    "os/user"
)

func cacheDir() (dir string) {
    if u, _ := user.Current(); u != nil {
        dir = filepath.Join(os.TempDir(), "cache-golang-autocert-" + u.Username)
        if err := os.MkdirAll(dir, 0700); err == nil {
            return dir
        }
    }
    return ""
} 
```

Enter fullscreen mode Exit fullscreen mode

你不需要*提供缓存，但是移除它将会减慢启动速度，并且你的服务器将不能适应让我们加密停机。*

## 发送 HSTS 报头

虽然本文顶部的示例包括一个将用户重定向到您的 HTTPS 监听器的纯 HTTP 处理程序，但理想情况下，您希望指示用户的浏览器为您完成这项工作，并避免延迟(和/或安全隐患)。

通过在每个请求的*上返回一个 HSTS 报头，你指示客户端的浏览器只能通过 HTTPS 与你对话。为了确保在接下来的六个月里做到这一点，请加上:* 

```
 http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Strict-Transport-Security", "max-age=15768000 ; includeSubDomains")
        // ... rest of handler here
    }) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# ⚙️启动时使用 SystemD 运行

如果你没有使用 Snap 这样的帮助服务，那么你需要在启动时使用它。您可以使用 SystemD 来实现这一点。

让我们创建一个可以添加到`/etc/systemd/system`的服务文件。这是我的`httpd.service` :

```
[Unit]
Description=Go webserver
After=network.target

[Service]
ExecStart=/home/sam/http/server  # path to binary
WorkingDirectory=/home/sam/http  # folder for binary
User=nobody
Group=nogroup
ProtectSystem=yes
AmbientCapabilities=CAP_NET_BIND_SERVICE  # lets `nobody` user bind ports 80, 443

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了服务文件，就可以运行:

```
sudo systemctl start httpd

# and see its output:
sudo journalctl -f -u httpd

# and enable on boot:
sudo systemctl enable httpd 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。

* * *

我希望这是有用的，至少作为人们学习如何开始使用 certs 的参考指南！如果这篇文章有用，请点击其中一个心形👉下面的❤️按钮，或者在推特上告诉我。