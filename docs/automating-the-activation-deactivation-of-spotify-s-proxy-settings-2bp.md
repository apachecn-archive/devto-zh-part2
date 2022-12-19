# 自动激活/停用 Spotify 的代理设置

> 原文：<https://dev.to/jlengrand/automating-the-activation-deactivation-of-spotify-s-proxy-settings-2bp>

[![Automating the activation / deactivation of Spotify's proxy settings](img/53c313eb5df1a746eff879929168b0b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AlS25d_w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1504509546545-e000b4a62425%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

我是 **[Spotify](https://www.spotify.com/)** 的**超级**粉丝。我每天都用它，一用就是几个小时。虽然我喜欢胖客户端，但我发现网页版太容易出错且不稳定。

在 ING，我们使用 HTTP 代理，我已经配置了 Spotify 来通过代理。在家里，我什么都不用。

这意味着每天我都要回到我的设置，禁用/启用代理，重启 Spotify。这很讨厌。

令人失望的是，我开始在家里使用网络播放器。

最后，它让我够累了，所以我决定更深入地挖掘这个问题，找到一个解决方案。这必须能够自动化。

第一步是找到配置文件的位置。在我的 Macbook 上，它位于**$ HOME/Library/Application Support/Spotify/prefs**。

在这个文件里，我看到一行: **network.proxy.mode=1** 。

剩下的就是儿戏了

这是我用来启用/禁用 spotify 代理设置的最终脚本。我把它叫做**spoti proxy**T2

```
#!/bin/sh

ON_OFF=$1
SPOTIFY_PREFS="$HOME/Library/Application Support/Spotify/prefs"
PROXY_ON="network.proxy.mode=2" #HTTP Proxy
PROXY_OFF="network.proxy.mode=1" #No proxy

if [! -f "$SPOTIFY_PREFS"]; then
    echo "Spotify preference file not found. Exiting"
    exit 1
fi

if ["$ON_OFF" = "on"]
then
    echo "Activating Spotify proxy settings"
    sed -i -e "s/$PROXY_OFF/$PROXY_ON/g" "$SPOTIFY_PREFS"
elif ["$ON_OFF" = "off"]
then
    echo "Deactivating Spotify proxy settings"
    sed -i -e "s/$PROXY_ON/$PROXY_OFF/g" "$SPOTIFY_PREFS"
elif ["$ON_OFF" = "show"]
then
    cat "$SPOTIFY_PREFS"
    exit 1
else
    echo "Invalid command entered. Please use 'spotiProxy on' or 'spotiProxy off'"
    exit 1
fi

number_processes=$(pgrep Spotify | wc -l)
if [$number_processes -gt 1] 
then
    echo "Restarting Spotify"
    osascript -e 'quit app "Spotify"'
    sleep 1
    open -a Spotify
fi 
```

Enter fullscreen mode Exit fullscreen mode

它的用法很简单:

*   用`spotiProxy on`(或 off)调用它
*   它会对你的设置进行相应的设置，如果 Spotify 已经在运行，它会重启 Spotify。
*   尽情享受吧！

每当我回到家或办公室，这个脚本就会自动运行。没什么可做的了。欢乐！

如果你想了解更多信息，你可以查看相关的 [Spotify 论坛帖子](https://community.spotify.com/t5/Desktop-Mac/Script-to-change-settings/m-p/4446246#M531825)或[github 上的要点](https://gist.github.com/jlengrand/ec2fff0f741ae0a59a7f203d9ffee348)。

下次见！