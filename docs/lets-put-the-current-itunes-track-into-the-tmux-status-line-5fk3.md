# 让我们将当前的 iTunes 曲目放入 Tmux 状态行

> 原文：<https://dev.to/fenetikm/lets-put-the-current-itunes-track-into-the-tmux-status-line-5fk3>

当你到达终点时，当音乐真的很糟糕/摇滚时，知道音乐是为了什么会很好。在你的 Tmux 状态栏中加入这样的内容不是很好吗:

[![tmux status](img/3728281d5af49cd2251d7156e0c6d2af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oG5S86pW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zqsv4bhmtbo115rsojx3.png)

(答案是*是*)

## 我们开始吧

首先你需要把下面的内容放到一个脚本文件中，假设我们把它放到`~/.config/itunes_status.sh`中。

```
#!/usr/bin/env bash

ITUNES_TRACK=$(osascript <<EOF if appIsRunning("iTunes") then
  tell app "iTunes" to get the name of the current track
end if

on appIsRunning(appName)
    tell app "System Events" to (name of processes) contains appName
end appIsRunning EOF if [[ ! -z "$ITUNES_TRACK" ]]; then ITUNES_ARTIST=$(osascript <<EOF if appIsRunning("iTunes") then
      tell app "iTunes" to get the artist of the current track
  end if

  on appIsRunning(appName)
      tell app "System Events" to (name of processes) contains appName
  end appIsRunning EOF

  TRACK_LEN=${#ITUNES_TRACK}
  if [[ "$TRACK_LEN" -gt 30 ]]; then ITUNES_TRACK=`echo "$ITUNES_TRACK" | cut -c -30`
    ITUNES_TRACK+=...
  fi ARTIST_LEN=${#ITUNES_ARTIST}
  if [[ "$ARTIST_LEN" -gt 20 ]]; then ITUNES_ARTIST=`echo "$ITUNES_ARTIST" | cut -c -20`
    ITUNES_ARTIST+=...
  fi echo '#[fg=#99a4bc]♫#[fg=#b4b4b9]' "$ITUNES_TRACK" '#[fg=#787882]-#[fg=#b4b4b9]' "$ITUNES_ARTIST"
  exit else echo "#[fg=#787882]No music playing"
fi 
```

Enter fullscreen mode Exit fullscreen mode

本质上，这实现了以下功能:

*   检查 iTunes 是否正在运行，如果是，获取曲目的名称
*   如果我们有一个曲目名称，也抓住艺术家
*   如果曲目名称长度超过 30 个字符，请截断并插入省略号
*   如果艺术家姓名长度超过 20 个字符，请执行同样的操作
*   用一些颜色输出并隔开
*   否则写“没有音乐播放”

## 插入 Tmux 状态栏

好的，这很好，但是我们需要把它放到 Tmux 状态中。这可以通过在您的`.tmux.conf` :
中插入如下内容来实现

```
set -g status-right "#(~/.config/itunes_status.sh) #[fg=#57575e]│ #[fg=white]%d/%m/%Y %H:%M " 
```

Enter fullscreen mode Exit fullscreen mode

**注意**作为“奖励”我也把时间放在了 iTunes 曲目之后(没错，没错，这么丰厚的奖励)。如果你想知道那些颜色十六进制值是什么，它们来自[猎鹰](https://github.com/fenetikm/falcon)主题(由 moi 制作)。不要忘记，您还需要使这个脚本可执行。