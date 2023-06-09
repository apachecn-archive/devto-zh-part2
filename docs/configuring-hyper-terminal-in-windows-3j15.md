# 在 Windows 中配置超级终端

> 原文：<https://dev.to/droidmakk/configuring-hyper-terminal-in-windows-3j15>

`So what is hyper?`

Hyper 是高度可扩展和可定制的📝跨平台的电子终端应用。

因此，我将分享我是如何将我的 windows bash 配置成尽可能具有交互性的。☝️

在我的发展阶段。我可以说**前超**和**后超**的特权。因为我的工作大部分都与终端有关，所以以前会打开一堆命令提示窗口。

hyper 进入我的生活后，我的生活变得更有条理了。因为他们说这是完全可定制的，我开始看到插件和我们可以用它做什么。

所以我做了一些改变，安装了一些插件，比如超能力，超边界，超 cwd。

这一切做完之后。我看了一下 ZSH MAC 用户终端。这在 windows 中是不可能的，因为你必须设置 PS1 变量，这在命令提示符下是不会发生的。PS:也没有彩色输出。也许我不知道该怎么做！

所以我找到了一种方法，可以在 git bash 中实现。这让我脸上露出了有趣的笑容😁。在我的超级配置中，将终端改为 git-command 和一些额外的参数来启动它。然后编辑了 Git bash 的 PS1。

然后，出了点问题，我没有得到像分支图标/错误符号这样的特殊字符。在做了一些研究后，我发现字体 **Fira 代码**。刚安装后把超级配置中的字体家族改成了 **Fira 码**。现在看看它是什么样子。

[![Hyper Look](img/d60318b67692ae8e06a5bf7e2263f05b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e-i6CLIw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://imgur.com/YCYjobR.gif)

这是我的配置文件。[链接](https://gist.github.com/droidMakk/f975a21b9d41578b19382ee102efc24d)

```
// Future versions of Hyper may add additional config options,
// which will not automatically be merged into this file.
// See https://hyper.is#cfg for all currently supported options.
module.exports = {
    config: {
        // choose either `'stable'` for receiving highly polished,
        // or `'canary'` for less polished but more frequent updates
        updateChannel: 'stable',
        // default font size in pixels for all tabs
        fontSize: 12,
        // font family with optional fallbacks
        fontFamily: "Fira Code",
        // default font weight: 'normal' or 'bold'
        fontWeight: 'normal',
        // font weight for bold characters: 'normal' or 'bold'
        fontWeightBold: 'bold',
        // terminal cursor background color and opacity (hex, rgb, hsl, hsv, hwb or cmyk)
        cursorColor: 'rgba(248,28,229,0.8)',
        // terminal text color under BLOCK cursor
        cursorAccentColor: '#000',
        // `'BEAM'` for |, `'UNDERLINE'` for _, `'BLOCK'` for █
        cursorShape: 'BLOCK',
        // set to `true` (without backticks and without quotes) for blinking cursor
        cursorBlink: true,
        // color of the text
        foregroundColor: '#fff',
        // terminal background color
        // opacity is only supported on macOS
        backgroundColor: '#000',
        // terminal selection color
        selectionColor: 'rgba(248,28,229,0.3)',
        // border color (window, tabs)
        borderColor: '#333',
        // custom CSS to embed in the main window
        css: '',
        // custom CSS to embed in the terminal window
        termCSS: '',
        // if you're using a Linux setup which show native menus, set to false
        // default: `true` on Linux, `true` on Windows, ignored on macOS
        showHamburgerMenu: '',
        // set to `false` (without backticks and without quotes) if you want to hide the minimize, maximize and close buttons
        // additionally, set to `'left'` if you want them on the left, like in Ubuntu
        // default: `true` (without backticks and without quotes) on Windows and Linux, ignored on macOS
        showWindowControls: 'left',
        // custom padding (CSS format, i.e.: `top right bottom left`)
        padding: '12px 14px',
        // the full list. if you're going to provide the full color palette,
        // including the 6 x 6 color cubes and the grayscale map, just provide
        // an array here instead of a color map object
        colors: {
        black: '#000000',
        red: '#C51E14',
        green: '#1DC121',
        yellow: '#C7C329',
        blue: '#0A2FC4',
        magenta: '#C839C5',
        cyan: '#20C5C6',
        white: '#C7C7C7',
        lightBlack: '#686868',
        lightRed: '#FD6F6B',
        lightGreen: '#67F86F',
        lightYellow: '#FFFA72',
        lightBlue: '#6A76FB',
        lightMagenta: '#FD7CFC',
        lightCyan: '#68FDFE',
        lightWhite: '#FFFFFF',
        },
        // the shell to run when spawning a new session (i.e. /usr/local/bin/fish)
        // if left empty, your system's login shell will be used by default
        //
        // Windows
        // - Make sure to use a full path if the binary name doesn't work
        // - Remove `--login` in shellArgs
        //
        // Bash on Windows
        // - Example: `C:\\Windows\\System32\\bash.exe`
        //
        // PowerShell on Windows
        // - Example: `C:\\WINDOWS\\System32\\WindowsPowerShell\\v1.0\\powershell.exe`
        shell: "C:\\Program Files\\Git\\git-cmd.exe",
        // for setting shell arguments (i.e. for using interactive shellArgs: `['-i']`)
        // by default `['--login']` will be used
        shellArgs: ['--command=usr/bin/bash.exe', '-l', '-i'],
        // for environment variables
        env: { TERM: 'cygwin' },
        // set to `false` for no bell
        bell: 'SOUND',
        // if `true` (without backticks and without quotes), selected text will automatically be copied to the clipboard
        copyOnSelect: false,
        // if `true` (without backticks and without quotes), hyper will be set as the default protocol client for SSH
        defaultSSHApp: true,
        // if `true` (without backticks and without quotes), on right click selected text will be copied or pasted if no
        // selection is present (`true` by default on Windows and disables the context menu feature)
        // quickEdit: true,
        // URL to custom bell
        // bellSoundURL: 'http://example.com/bell.mp3',
        // for advanced config flags please refer to https://hyper.is/#cfg
        opacity: 0.96
    },
    // a list of plugins to fetch and install from npm
    // format: [@org/]project[#version]
    // examples:
    // `hyperpower`
    // `@company/project`
    // `project#1.0.1`
    plugins: [
    "hyper-snazzy",
    "hyper-systray",
    "hyper-opacity",
    "hyper-tab-icons",
    "hyperborder"
    ],
    // in development, you can create a directory under
    // `~/.hyper_plugins/local/` and include it here
    // to load it and avoid it being `npm install`ed
    localPlugins: [],
    keymaps: {
    // Example
    // 'window:devtools': 'cmd+alt+o',
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 资源

我发现一些在定制过程中有用的链接😉

*   [Fira 代码](https://github.com/tonsky/FiraCode)
*   [Oh My Git](https://github.com/arialdomartini/oh-my-git)
*   [牛逼-超](https://github.com/bnb/awesome-hyper)
*   [超级配置](https://gist.github.com/coco-napky/404220405435b3d0373e37ec43e54a23)