# 我如何备份/同步我的点文件和应用程序

> 原文：<https://dev.to/biros/how-i-backupsync-my-dotfiles--apps-o0b>

我关心的是自动备份我的配置文件(又名点文件)并生成所有已安装应用的快照，无论是通过我的发行版的包管理器还是通过第三方包管理器，如`npm`或`pip`。这是能够在新安装的电脑上自动同步我的应用程序的第一步(还有待完成)。

## 备份点文件

我所做的是在我的目录下创建一个 git repo，因为我所有的点文件都在这里。但是由于我不想推送所有其他文件(图片、下载、桌面...)，我放了一个只有通配符(`*`)的`.gitignore`文件。这样，当我想在回购中添加一个文件时，我必须强制使用:`git add -f newfile`。

```
cd ~
git init
echo "*" > .gitignore
git add -f .gitignore
git add -f .bashrc
git add -f .zshrc
git add -f .config/fish
git add -f .tmux.conf
git add -f .SpaceVim.d/init.toml
git commit -m "First commit"
git remote add origin git@github.com:username/mydotfiles
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

⚠️:但是要知道，这对你的敏感数据可能是危险的。比如不要推你的`~/.ssh`配置！

因此，在计算机全新安装或崩溃的情况下，您有一个快速的方法来检索您的配置。

下一步是对你的应用程序做同样的事情。

## 生成应用程序的快照

关于应用程序，这并不容易。有几个包管理器来安装新的应用程序并不罕见。我个人用其中的 7 个:`npm`、`cargo`、`ghc-pkg`、`composer`、`gem`、`pip`和`pacman`，我的 Manjaro linux 发行版的经理。它们都有自己的语法来安装、删除或更新软件包。但是它们都有自己的语法来输出已安装软件包的列表。

所以基本上，我只是使用下面的 bash 脚本来生成所有软件包管理器中所有已安装软件包的列表:

```
#!/bin/bash

echo "Generating the lists of explicitly installed packages in ~/.backup"

pacman -Qe > ~/.backup/pacman_packages || echo "pacman failed"
gem list > ~/.backup/gem_packages || echo "gem failed"
npm list -g --depth=0 > ~/.backup/npm_packages || echo "npm failed"
pip list > ~/.backup/pip_packages || echo "pip failed"
cargo --list | tail -n +2 | tr -d " " > ~/.backup/cargo_packages || echo "cargo failed"
ghc-pkg list > ~/.backup/ghc-pkg_packages || echo "ghc-pkg failed"
composer global show | cut -d ' ' -f1 > ~/.backup/composer_packages || echo "composer failed"

git add -f .backup

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

注意脚本末尾的`git add`命令。我这样做是为了自动将`~/.backup`文件夹添加到 git 索引中，以便将其推送到远程。当我把这个脚本放到`pre-commit`钩子中时，我的包列表会在我每次提交时与 git 同步:

```
biros on  master [!]
➜ git commit -m "Update conf"
Generating the lists of explicitly installed packages in ~/.backup
[master 27f73e4] Update conf
 1 file changed, 1 insertion(+) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

⏭️下一步:能够从备份中自动重新安装所有应用程序。

* * *

💡提示:你可以用[同步设置](http://atom.io/packages/sync-settings)插件同步你的 Atom 插件。

* * *

📚同一主题的其他资源:

[![amcsi image](img/ab471c0e4bde8ef4a5c2279d29f83559.png)](/amcsi) [## 分享。带外壳脚本的跨平台点文件

### 阿提拉·里梅 29 号 18 号 4 分钟前读完

#linux #dotfiles #git #bash](/amcsi/sharing-dotfiles-cross-platform-with-a-shell-script-o2j)