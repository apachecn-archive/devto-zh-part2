# 寻找实用程序 Bash 脚本的酷拉请求

> 原文：<https://dev.to/aviaryan/looking-for-cool-pull-requests-for-utility-bash-scripts-25ea>

所以在 Hacktoberfest 的前夕，我启动了一个新的 repo，叫做 [utility-bash-scripts](https://github.com/aviaryan/utility-bash-scripts) 。它是一个脚本集合，可以通过简单地调用一个命令来做有用的事情。

这些剧本在我的道路上已经有很多年了，我想我应该与世界分享它。因此，我开源了我的大部分脚本集，并添加了一些文档，以便其他人可以使用它，也可以为它做出贡献。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [阿维亚安](https://github.com/aviaryan) / [实用工具-bash-脚本](https://github.com/aviaryan/utility-bash-scripts)

### 🤓有用的 bash 脚本，用一个命令就可以完成自动化任务

<article class="markdown-body entry-content p-5" itemprop="text">

# <g-emoji class="g-emoji" alias="nerd_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f913.png">🤓</g-emoji>实用 bash 脚本

[![Contributors needed](img/73a6d4c3dde0474f575e603e2fbb7b12.png) ](https://raw.githubusercontent.com/aviaryan/utility-bash-scripts/master/.github/CONTRIBUTING.md) [ ![Build Status](img/9df20536b9111461e26f6414b75d08a6.png)](https://travis-ci.org/aviaryan/utility-bash-scripts)

实用程序 bash 脚本，用一个命令就可以完成自动化任务。我们有脚本下载 youtube 视频，从 youtube 下载音乐，转换媒体文件等。

贡献并添加您的秘密脚本。

## <g-emoji class="g-emoji" alias="memo" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4dd.png">📝</g-emoji>注意事项

下载脚本下载到`~/Downloads/`文件夹。视频下载到`~/Downloads/Videos`，音频下载到`~/Downloads/Music`。

为了获得最佳结果，将这个 git repo 克隆到您计算机上的一个固定位置，并将其添加到`$PATH`。

```
cd ~
git clone https://github.com/aviaryan/utility-bash-scripts.git utility-scripts
cd utility-scripts
export PATH="$(pwd):$PATH"
```

## <g-emoji class="g-emoji" alias="scroll" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4dc.png">📜</g-emoji>脚本

### <g-emoji class="g-emoji" alias="small_red_triangle_down" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f53b.png">🔻</g-emoji>从 YouTube 下载 MP4 格式的视频

脚本:[YouTube-视频](https://raw.githubusercontent.com/aviaryan/utility-bash-scripts/master/youtube-video)
依赖: [youtube-dl](https://github.com/rg3/youtube-dl) ， [ffmpeg](https://www.ffmpeg.org/) ， [aria2c](https://aria2.github.io/) (可选)

```
youtube-video "https://www.youtube.com/watch?v=HgfojLtSBTM"
```

### <g-emoji class="g-emoji" alias="twisted_rightwards_arrows" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f500.png">🔀</g-emoji>将视频和音频融合在一起

脚本:[vamerge](https://raw.githubusercontent.com/aviaryan/utility-bash-scripts/master/vamerge)T2】从属: [ffmpeg](https://www.ffmpeg.org/)

```
vamerge <path to video file> <path to audio file>
# the order is important, first video, then audio
```

…</article>

[View on GitHub](https://github.com/aviaryan/utility-bash-scripts)

现在，我在开发社区寻找任何有兴趣向这个回购发送拉请求的同行。我真的很想看看你用了哪些我还没有意识到的很酷的脚本。