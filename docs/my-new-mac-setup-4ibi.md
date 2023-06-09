# 我的新 Mac 设置(2018-2020)

> 原文：<https://dev.to/swyx/my-new-mac-setup-4ibi>

> 我已经[在这里](https://dev.to/swyx/my-2021-new-mac-setup-1b1)更新了我 2021 年的清单。

我今天为工作设置了一台新的 Mac。以下是我立即做的:

*   浏览器:下载 Chrome，设置为默认。
*   登录到:
    *   推特
    *   Github(更多设置说明见下文)
    *   谷歌邮箱
*   设置:
    *   禁用除应用程序和系统偏好设置之外的所有杂项垃圾的 Spotlight 搜索
        *   包括[愚蠢的开发者选项](https://www.howtogeek.com/231829/how-to-disable-developer-search-results-in-spotlight-on-a-mac/)
    *   停用询问 Siri
    *   [大光标](https://www.lifewire.com/make-mac-mouse-pointer-bigger-2260808)
    *   触控板->滚动和缩放-自然关闭
    *   触控板->点击->查找和关闭数据检测器
    *   打开 Airdrop
    *   (如果使用 windows 键盘)重新映射 alt 和 cmd[https://super user . com/questions/158561/how-can-I-remap-windows-and-alt-keys-in-OS-x](https://superuser.com/questions/158561/how-can-i-remap-windows-and-alt-keys-in-os-x)
*   查找工具:
    *   显示文件扩展名
    *   显示点文件(只需按住 Cmd + Shift +。(Finder 窗口中的点)
    *   [显示路径栏](https://www.tekrevue.com/tip/show-path-finder-title-bar/)
*   键盘:
    *   [将 command+Q 重新映射到任何东西上](https://apple.stackexchange.com/questions/78948/how-to-disable-command-q-for-quit)
    *   将所选区域的图片复制到剪贴板-> Cmd+E
*   码头:
    *   移除 Dock 中的所有内容，除了 Finder、系统偏好设置和废纸篓
    *   打开隐藏
*   Chrome 扩展:

    *   来试试:[https://github.com/iamadamdev/bypass-paywalls-chrome/](https://github.com/iamadamdev/bypass-paywalls-chrome/)
    *   [https://github.com/round/Twitter-Links-beta](https://github.com/round/Twitter-Links-beta)
    *   墨菲斯黑暗主题
    *   1 密码
    *   [显示锚点](https://chrome.google.com/webstore/detail/display-anchors/poahndpaaanbpbeafbkploiobpiiieko/related?hl=en)
    *   [试剂债务工具](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
    *   [精制 Github](https://github.com/sindresorhus/refined-github)
    *   [代码复制](https://chrome.google.com/webstore/detail/codecopy/fkbfebkcoelajmhanocgppanfoojcdmg?hl=en)
    *   [视频速度控制器](https://chrome.google.com/webstore/detail/video-speed-controller/nffaoalbilbmmfgbnbgppjihopabppdk?hl=en)
    *   [Palettab](https://palettab.com/)
    *   [隐私獾](https://chrome.google.com/webstore/detail/privacy-badger/pkehgijcmpdhfbdbbnkijodmdjhbjlgp?hl=en-US)
    *   [重新约定时间](https://chrome.google.com/webstore/detail/privacy-badger/pkehgijcmpdhfbdbbnkijodmdjhbjlgp?hl=en-US)
    *   [子块原点](https://chrome.google.com/webstore/detail/ublock-origin/cjpalhdlnbpafiamejdnhcphjbkeiagm?hl=en)
    *   [八连接器](https://chrome.google.com/webstore/detail/octolinker/jlmafbaeoofdegohdhinkhilhclaklkp?hl=en)
    *   [异步渲染工具箱](https://github.com/sw-yx/async-render-toolbox)(这是我做的)
*   终端环境

    *   我的点文件(vimrc，zshrc，。git ignore _ global):[https://gist . github . com/SW-yx/7fa 1009 e 460 ECB 818 D5 E6 d 9 ca 4616 a 05](https://gist.github.com/sw-yx/7fa1009e460ecb818d5e6d9ca4616a05)
    *   [ZSH](https://ohmyz.sh/)(`git`的第一次使用会提示你安装 git——需要 15 分钟)
    *   `git config --global user.name "swyx"`
    *   `git config --global user.email shawnthe1@gmail.com`

        *   字体- [输电线的伤心欲绝](https://github.com/powerline/fonts/blob/master/Inconsolata/Inconsolata%20for%20Powerline.otf)
        *   [自我暗示](https://github.com/zsh-users/zsh-autosuggestions)
        *   [语法高亮显示](https://github.com/zsh-users/zsh-syntax-highlighting)
        *   可能需要 [chmod stuff](https://stackoverflow.com/questions/13762280/zsh-compinit-insecure-directories) 或者在每次会话开始时显示警告

        ```
        $ sudo chmod -R 755 /usr/local/share/zsh
        $ sudo chown -R root:staff /usr/local/share/zsh 
        ```

    *   [超级终端](https://hyper.is/)

        *   设置: [shell: '/bin/zsh'](https://gist.github.com/robertcoopercode/276d7cf66e9b0eea48c117fff1762a17#file-hyper-js-L60)
        *   设置:`fontFamily: '"Inconsolata for Powerline", Menlo, "DejaVu Sans Mono", Consolas, "Lucida Console", monospace',`
        *   [图](https://fig.io/) -终端的上下文感知自动完成。现在在等待名单上，但是我的版本是[这里](https://waitlist.withfig.com/download/691b465b-afb9-4776-b179-cc4d78e21345)
            *   布伦丹·费克(Fig 创始人)推荐的更多 CLI 工具——`bat`、`exa`、`ripgrep`以及其他 [Rust CLI 替代者](https://zaiste.net/posts/shell-commands-rust/)。还有 [zsh 缩写](https://github.com/momo-lab/zsh-abbrev-alias)
    *   [家酿](https://brew.sh/) -我在`brew list`有一堆更多的东西，但我不确定我积极使用什么。可以批量安装这些:`brew install $(cat packages.txt)`

    ```
    bat             gdbm            libuv           python@3.9
    brotli          gh              libyaml         readline
    c-ares          go              mpdecimal       ruby
    deno            gradle          nghttp2         sqlite
    diff-so-fancy   icu4c           node            xz
    fnm             jemalloc        openjdk         yarn
    fzf             libev           openssl@1.1     z 
    ```

    *   `brew install bat`
    *   [Github CLI](https://github.com/cli/cli) : `brew install github/gh/gh`
        *   你需要登录 git——如果你启用了 2fa，你就不能使用普通的 github 密码。尝试推送回购，并输入一个[个人访问令牌](https://stackoverflow.com/a/34919582)作为密码。
        *   然后运行`gh auth login`
        *   [添加 GitHub SSH 密钥](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)(非可选)
    *   `brew install [fzf](https://github.com/junegunn/fzf)`——这里的用法示例是
    *   [纱](https://yarnpkg.com/en/docs/install#mac-stable)注`brew install yarn --ignore-dependencies`自从我用 nvm
        *   您可能需要[解决 Mac OS Sierra](https://stackoverflow.com/questions/43780207/installing-node-with-brew-fails-on-mac-os-sierra) 并首先安装 node
    *   [`brew install z`](https://brewinstall.org/install-z-on-mac-with-brew/)——真的好试
    *   `brew install python`
    *   `brew install ruby`
    *   `brew install deno`
    *   `brew install gradle`
    *   `pip3 install --user powerline-status`
    *   转到中立文件夹并`git clone [https://github.com/powerline/fonts](https://github.com/powerline/fonts) && cd fonts && ./install.sh`
        *   `brew install node`[Node.js/NPM](https://nodejs.org/en/download/)
    *   `npm login`
    *   `sudo npm install netlify-cli -g`
    *   `npm i -g sign-bunny fortune-node parrotsay`
    *   `npm install -g undollar`用于移除美元
    *   `sudo npm install -g @aws-amplify/cli`
    *   `amplify configure`
    *   `sudo npm install -g trash-cli`
        *   [fnm](https://github.com/Schniz/fnm) 比 [nvm](https://github.com/creationix/nvm) : `curl -fsSL https://fnm.vercel.app/install | bash`或`brew install fnm`速度更快
        *   【蟒蛇】([https://www.anaconda.com/download/#macos](https://www.anaconda.com/download/#macos)
        *   [Docker 桌面](https://hub.docker.com/editions/community/docker-ce-desktop-mac/)
        *   `brew install java` - [Java 开发套件](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
        *   `brew install [go](https://golang.org/dl/)`别忘了`export PATH=$PATH:/usr/local/go/bin`
        *   `fontFamily: '"Inconsolata for Powerline", Menlo, "DejaVu Sans Mono", Consolas, "Lucida Console", monospace',`
        *   `brew install [diff-so-fancy](https://www.npmjs.com/package/diff-so-fancy)` - `git config --global core.pager "diff-so-fancy | less --tabs=4 -RFX"`

您还可以使用这个 bash 函数`dif() { git diff --color --no-index "$1" "$2" | diff-so-fancy; }`或 VSCode `code --diff file1.js file2.js`来区分。

也可以试试[https://github.com/dandavison/delta](https://github.com/dandavison/delta)

*   表情符号:[https://matthewpalmer.net/rocket/](https://matthewpalmer.net/rocket/)
*   密码管理员:[https://www.1password.com/](https://www.1password.com/)(我有公司账号)
*   窗口经理:[https://www.spectacleapp.com/](https://www.spectacleapp.com/)
    *   登录时启动
*   剪贴板管理器:[https://clipy-app.com/](https://clipy-app.com/)
*   织机:[https://www.loom.com/desktop](https://www.loom.com/desktop)
*   截图:[https://cleanshot.com/](https://cleanshot.com/)(之前用过的[https://zapier.com/zappy](https://zapier.com/zappy))
*   咖啡因(让麦克保持清醒进行会谈):[【https://intelliscapesolutions.com/apps/caffeine】](https://intelliscapesolutions.com/apps/caffeine)(曾经是[http://lightheadsw.com/caffeine/](http://lightheadsw.com/caffeine/))
*   视频拍摄:[https://getkap.co/](https://getkap.co/)
*   双屏:[https://www.duetdisplay.com/](https://www.duetdisplay.com/)
*   gif:[Licecap](https://www.cockos.com/licecap/)
*   [懈怠](https://slack.com/downloads/osx)或[不和](https://discord.com/)
*   OBS:[https://obsproject.com/](https://obsproject.com/)
*   天空字体:[https://www.fonts.com/web-fonts/google](https://www.fonts.com/web-fonts/google)
*   微软待办事项:[https://apps.apple.com/app/apple-store/id1274495053?mt=8](https://apps.apple.com/app/apple-store/id1274495053?mt=8)
*   舒展地:[https://hovancik.net/stretchly/](https://hovancik.net/stretchly/)
*   simple note:T1】https://apps.apple.com/us/app/simplenote/id692867256?ls=1&amp；mt=12
*   [超人为 Mac](http://superhuman.com/mac) 和T3】https://mail.superhuman.com
*   观念:[https://www.notion.so/desktop](https://www.notion.so/desktop)
    *   [https://chrome . Google . com/Webster/detail/concept-web clipper/knhegckgoiginebankhaalnibhilkk？hl=en](https://chrome.google.com/webstore/detail/notion-web-clipper/knheggckgoiihginacbkhaalnibhilkk?hl=en)
*   App 搜索/应用:[https://www.alfredapp.com/](https://www.alfredapp.com/)
    *   设置为阿尔弗雷德黑暗
    *   [空投到 iphone/ipad](https://github.com/swmoon203/CrossShare/blob/master/Alfred%20Workflow/Cross%20Share%20Airdrop.alfredworkflow)
    *   [纸杯蛋糕 Ipsum](http://www.packal.org/workflow/cupcake-ipsum)
*   编辑:下载 [VS 代码](https://code.visualstudio.com/download)(我以前用的是圈内人但是弹出窗口超级烦)。使用设置同步来跨机器同步
    *   必须设置电力线字体“Meslo LG M for power line”([下载](https://github.com/powerline/fonts/blob/master/Meslo%20Slashed/Meslo%20LG%20M%20Regular%20for%20Powerline.ttf))
    *   自动关闭标签 v0.5.6
    *   自动重命名标签 v0.0.15
    *   书签 v9.1.0
    *   代码-设置-同步 3.1.2 版
    *   chrome 调试器版本 4.10.2
    *   es7-react-js-snippets 1 . 8 . 7 版
    *   图 ql-for-vscode v1.12.1
    *   mdx v0.1.0
    *   更漂亮-vs code 1 . 6 . 1 版
    *   python v2018.9.2
    *   python v0.2.3
    *   彩虹括号 v0.0.6
    *   紫色阴影 3.17.0 版
    *   vscode-graphql v0.1.5
    *   vscode-import-cost v2.9.0
    *   vs code-style-components v 0 . 0 . 23
    *   vscode-wakatime v1.2.3
    *   人工智能完成
    *   [GitHub 副驾驶](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)

* * *

## 其他好的“新笔记本电脑设置”列表:

*   [https://github.com/minamarkham/formation](https://github.com/minamarkham/formation)
*   [塔尼亚·拉西亚的设置](https://www.taniarascia.com/setting-up-a-brand-new-mac-for-development/?ck_subscriber_id=591519942)
*   [尼克·尼斯的网络文件](https://github.com/nicknisi/dotfiles)
*   [Mathias Bynens macos 默认值](https://github.com/mathiasbynens/dotfiles/blob/master/.macos)