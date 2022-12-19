# 如何在 Manjaro Linux 中安装 Node.js

> 原文：<https://dev.to/lobo_tuerto/how-to-install-nodejs-in-manjaro-linux--5ha4>

* * *

你可以在 [lobotuerto 的笔记——如何在 Manjaro Linux 中安装 Node.js 查看这篇文章的最新更新版本。](https://lobotuerto.com/blog/how-to-install-nodejs-in-manjaro-linux/)

* * *

# 安装过程

开发环境中的版本管理器规则。

所以我们会通过 [nvm](https://github.com/creationix/nvm) 安装 [Node.js](https://nodejs.org/en/) 。

从 [AUR](https://wiki.archlinux.org/index.php/Arch_User_Repository) :
安装`nvm`包

```
pacaur -S nvm 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的终端:

```
echo 'source /usr/share/nvm/init-nvm.sh' >> ~/.bashrc
exec $SHELL 
```

Enter fullscreen mode Exit fullscreen mode

列出 **Node.js** :
的所有可用版本

```
nvm ls-remote 
```

Enter fullscreen mode Exit fullscreen mode

让我们安装最新的 **9** 系列:

```
nvm install 9
node -v #v9.8.0
npm -v #5.6.0 
```

Enter fullscreen mode Exit fullscreen mode

全局添加[纱](https://yarnpkg.com/en/)包经理:

```
npm install -g yarn 
```

Enter fullscreen mode Exit fullscreen mode

我们需要将**纱线的**二进制代码添加到`~/.bashrc`中的`$PATH`中。
中的
一个端子:

```
echo 'export PATH=$PATH:~/.yarn/bin' >> ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

如果你是 Vue.js 的粉丝，你为什么不应该成为粉丝呢？:)—安装 [vue-cli](https://github.com/vuejs/vue-cli) :

```
yarn global add vue-cli 
```

Enter fullscreen mode Exit fullscreen mode

# 链接

*   [拱门里的 node . js](https://wiki.archlinux.org/index.php/Node.js)