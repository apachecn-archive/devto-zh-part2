# Linux 主目录的子目录名称为英语

> 原文：<https://dev.to/nabbisen/linux--7hn>

### 前言

在安装带有桌面环境的 Linux 发行版时，默认情况下，通常会在主目录中创建几个目录。
存储桌面文件的目录、存储音乐文件的目录等。

[![](../Images/90768bd019a6563f34dc0f7fd97026ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QPrlBEIL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xe76yimztlwbxjxpg4ku.png)T3】

<center>Manjaro Linux の Cinnamon デスクトップの場合</center>

如果以“日语”设置进行安装，则这些目录的名称将为日语。
感觉就像桌面是“桌面”，下载文件的保存位置是“下载”。

介绍如何将这个名字改为英语(字母)。
只需执行一个命令就基本完成。

### Method of balancing

打开终端(终端)。
输入以下内容，然后按 Enter 键:

```
$  LANG=C xdg-user-dirs-gtk-update 
```

Enter fullscreen mode Exit fullscreen mode

**注意:开头的`$`表示由一般用户执行。 不需要输入。*

**补充:在少数情况下，如果将缺省 shell 环境设置为非 bash，按 Enter 键应该会出现错误。 键入`bash`，然后按 Enter 键。 在执行`exit`之前，可将外壳环境切换为 bash。 请在该状态下重新执行以上命令。*

会显示以下窗口，按右下方的“Update Names”。

[![](../Images/870757cfe0230f1ce9e9f1fb24bcd64f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ErH1ZQfj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sy7pk6pa842ayrcy8yo5.png)

在终端上输出以下内容，完成重命名。

```
Gtk-Message: 04:58:00.258: GtkDialog mapped without a transient parent. This is discouraged.
Moving DESKTOP directory from デスクトップ to Desktop
Moving DOWNLOAD directory from ダウンロード to Downloads
Moving TEMPLATES directory from テンプレート to Templates
Moving PUBLICSHARE directory from 公開 to Public
Moving DOCUMENTS directory from ドキュメント to Documents
Moving MUSIC directory from 音楽 to Music
Moving PICTURES directory from 画像 to Pictures
Moving VIDEOS directory from ビデオ to Videos 
```

Enter fullscreen mode Exit fullscreen mode

**补充:下次启动操作系统时，可能会显示以下窗口。 如果是这样，请选择“保留旧名称”。*

[![](../Images/cb2d3ff606e832a68a76d33fa0eee240.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p9n0SgyB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hv2qo86fhmpptfuoxgr1.png)

### 参考:设定的保存位置

保存在`~/.config/user-dirs.dirs`中:

```
$  cat ~/.config/user-dirs.dirs 
#  This file is written by xdg-user-dirs-update
#  If you want to change or add directories, just edit the line you're #  interested in. All local changes will be retained on the next run. #  Format is XDG_xxx_DIR="$HOME/yyy", where yyy is a shell-escaped #  homedir-relative path, or XDG_xxx_DIR="/yyy", where /yyy is an #  absolute path. No other format is supported. #  XDG_DESKTOP_DIR="$HOME/Desktop" XDG_DOWNLOAD_DIR="$HOME/Downloads" XDG_TEMPLATES_DIR="$HOME/Templates" XDG_PUBLICSHARE_DIR="$HOME/Public" XDG_DOCUMENTS_DIR="$HOME/Documents" XDG_MUSIC_DIR="$HOME/Music" XDG_PICTURES_DIR="$HOME/Pictures" XDG_VIDEOS_DIR="$HOME/Videos" 
```

Enter fullscreen mode Exit fullscreen mode

### 在结尾

字母名称目录的优点之一是，可以通过键触摸来跳转。
例如“下载”为“Downloads”，因此在键盘上按下名称的第一个文字“d”，就可以将焦点放在目录上。
(但是，如果还有其他以“d”开头的目录，则必须多次按“d”移动，或者持续输入直到成为“dow”等唯一字符串为止。 )
比基于 GUI 的鼠标操作更直接。

在终端(终端)上进行指令操作方面也非常方便:

```
$  cd ダウンロード # 半角入力と全角入力を切り替える必要があります
$  cd Downloads  # 半角入力だけで入力を完了できます 
```

Enter fullscreen mode Exit fullscreen mode