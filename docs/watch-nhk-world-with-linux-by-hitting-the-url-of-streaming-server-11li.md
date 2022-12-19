# 点击流媒体服务器的网址用 Linux 看 NHK 世界

> 原文：<https://dev.to/acro5piano/watch-nhk-world-with-linux-by-hitting-the-url-of-streaming-server-11li>

你看过 NHK 世界吗？

NHK，日本放送協会是日本的半官方广播机构。

它还提供 NHK 世界，世界范围的版本。它在互联网上播放与日本新闻、文化、旅游等相关的英语电影。我们可以在 windows 和 MacOS 上观看。

这里有个问题:我用 Linux 作为我的桌面环境，Chromium 作为我的默认浏览器，怎么看 NHK world？

现在，你不能通过 Linux 观看 NHK 世界。它会返回错误“此浏览器不支持实时流”。

别担心，我找到了用 Linux 看的方法。

# 如何

我们可以用 VLC 媒体播放器直接命中流媒体服务器的 URL。

*   打开[http://www3.nhk.or.jp/nhkworld/app/tv/hlslive_tv.xml](http://www3.nhk.or.jp/nhkworld/app/tv/hlslive_tv.xml)
*   复制`jstrm` ( `urls > main_url > jstrm`)的第一个元素。
*   打开 VLC 并选择菜单`Media > Network`
*   粘贴网址，点击`open`

我希望 NHK 允许我们通过各种操作系统观看，因为 NHK 是公共广播公司。

# 附录

我发现用 VLC 打开一个媒体服务器 URL 很容易。有时候我们可以录下来。

# Ref

[http://askubuntu . com/questions/704612/NHK-live-streaming-not-available-on-this-browser](http://askubuntu.com/questions/704612/nhk-live-streaming-not-available-on-this-browser)