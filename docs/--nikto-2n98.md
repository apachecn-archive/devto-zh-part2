# web 服务器安全工具: NIKTO

> 原文：<https://dev.to/terceranexus6/--nikto-2n98>

“NIKTO”是 web 服务器安全的工具。
[![](img/2e8526c54278af78d38474db58885513.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pHqdn1Nm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.darkorg.net/wp-content/uploads/2017/09/nikto-e1506788710993.png)
公的“NIKTO”的根本是[这里](https://cirt.net/nikto2-docs/)。 但是，这篇报道是大致的！
“LINUX”的“NIKTO”的设备很容易。

```
$ sudo apt install nikto 
```

Enter fullscreen mode Exit fullscreen mode

啊，是吗？ = )
实际上，没有使用`apt-get`

“HOST”的扫描命令是`-h`。

(例如)

```
$ nikto -h 192.168.0.1 
```

Enter fullscreen mode Exit fullscreen mode

也是，“HTML”的语法

```
$ nikto -h localhost 
```

Enter fullscreen mode Exit fullscreen mode

这个是“HTTP”，然后是“HTTPS”。

```
$ nikto -h https://localhost
$ nikto -h localhost -ssl 
```

Enter fullscreen mode Exit fullscreen mode

但是，这个是唯一的“HTTPS”。 80 是默认端口！ 端口的命令是`-p`。

```
$ nikto -h 192.168.0.1 -p 443 
```

Enter fullscreen mode Exit fullscreen mode

啊，一起看比喻吧！

```
$ nikto -h https://example.jp -p 443, 50, 63, 24, 80 
```

Enter fullscreen mode Exit fullscreen mode

那个比喻有很多端口！ 那是可能的。 : thumbsup_tone3:
也可以使用 PROXY。 好厉害啊！
请编辑“nikto.conf”。 “PROXY 的命令是`-useproxy`

```
$ nikto -h 192.168.0.1 -p 50 -useproxy 
```

Enter fullscreen mode Exit fullscreen mode

啊！ 我做完了。 喜欢吗？