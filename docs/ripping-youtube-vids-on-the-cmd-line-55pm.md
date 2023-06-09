# 在 cmd 线上抓取 Youtube 视频

> 原文：<https://dev.to/tedhagos/ripping-youtube-vids-on-the-cmd-line-55pm>

在翻我的旧 linux 和 macOS 备忘单时，我发现了这个，`youtube-dl`；它可以让你抓取 youtube 视频并下载。

```
sudo apt-get update && sudo apt-get install youtube-dl
youtube-dl <youtube vid URL> 
```

Enter fullscreen mode Exit fullscreen mode

macOS 上也有。用 brew 的话，用这个
就能搞定

```
brew update
brew install youtube-dl

youtube-dl <youtube vid URL> 
```

Enter fullscreen mode Exit fullscreen mode

我想你也可以用 Cygwin 在 Windows 中做到这一点，但我还没有尝试过。

**更新**:

[![zeerorg image](img/f1b8a54f0f2777d92875fb156363aa64.png)](/zeerorg)

## [里沙布·笈多](/zeerorg) <button name="button" type="button" data-info="{&quot;id&quot;:50212,&quot;className&quot;:&quot;user&quot;,&quot;name&quot;:&quot;Rishabh Gupta&quot;,&quot;style&quot;:&quot;full&quot;}" class="crayons-btn follow-action-button whitespace-nowrap  " aria-label="Follow user: Rishabh Gupta">跟随</button>

[I'm a software developer interested in programming language theory, system architecture and security.](/zeerorg)

在评论中补充。Windows 用户也可以通过 Python 包来获得。你可以从 [rg3/youtube-dl](https://rg3.github.io/youtube-dl/) 获取 EXE