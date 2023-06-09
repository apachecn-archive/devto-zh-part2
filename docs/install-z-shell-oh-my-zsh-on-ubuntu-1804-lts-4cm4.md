# 在 LTS 的 Ubuntu 18.04 上安装 Z-shell

> 原文：<https://dev.to/mskian/install-z-shell-oh-my-zsh-on-ubuntu-1804-lts-4cm4>

[![Z-shell for Ubuntu](img/b93cb81d66bbc30b28888c5322bb6b37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WMKb-PNA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qmdocrps1ngl6v1ox29n.png)

*   更新软件包

```
sudo apt-get update
sudo apt upgrade 
```

Enter fullscreen mode Exit fullscreen mode

*   安装必备软件包(ZSH、电力线和电力线字体)

```
sudo apt install zsh
sudo apt-get install powerline fonts-powerline 
```

Enter fullscreen mode Exit fullscreen mode

*   克隆我的 Zsh 响应

```
git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh 
```

Enter fullscreen mode Exit fullscreen mode

*   创建新的 ZSH 配置文件

```
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc 
```

Enter fullscreen mode Exit fullscreen mode

*   为你的终端设置一个奇特的主题——使用 nano 编辑器打开`.zshrc`文件

```
nano .zshrc 
```

Enter fullscreen mode Exit fullscreen mode

*   在`.zshrc`文件中找到行`ZSH_THEME="robbyrussell"`用`agnoster`主题替换`robbyrussell`(CTRL+X&回车保存)

```
ZSH_THEME="agnoster" 
```

Enter fullscreen mode Exit fullscreen mode

*   更改您的默认外壳

```
chsh -s /bin/zsh 
```

Enter fullscreen mode Exit fullscreen mode

*   更新&卸载 oh-my-zsh 访问-[https://github.com/robbyrussell/oh-my-zsh#manual-updates](https://github.com/robbyrussell/oh-my-zsh#manual-updates)

```
cd .oh-my-zsh
upgrade_oh_my_zsh 
```

Enter fullscreen mode Exit fullscreen mode

### 想要语法高亮？为我的天啊安装 ZSH 语法高亮

*   克隆 ZSH 语法高亮显示

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git "$HOME/.zsh-syntax-highlighting" --depth 1 
```

Enter fullscreen mode Exit fullscreen mode

*   在`.zshrc`配置中添加语法高亮显示

```
echo "source $HOME/.zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> "$HOME/.zshrc" 
```

Enter fullscreen mode Exit fullscreen mode

#### 恢复默认外壳

```
chsh -s /bin/bash 
```

Enter fullscreen mode Exit fullscreen mode