# 在 Windows 上开始 Nuxt.js

> 原文：<https://dev.to/bravotan/begining-nuxtjs-on-windows-267c>

安装:PowerShell 核心
[https://github.com/PowerShell/PowerShell/releases](https://github.com/PowerShell/PowerShell/releases)

在 PowerShell 上安装:Scoop 和 Node.js(Vue CLI，nuxt . js)
:

```
> iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
> scoop install nodejs
> npm install -g vue-cli
> vue init nuxt-community/starter-template <new project directory>
> cd <new project directory>
> scoop install yarn
> yarn
> yarn run dev 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。

又见:
勺:[https://scoop.sh/](https://scoop.sh/)T3】Vue CLI:[https://cli.vuejs.org/](https://cli.vuejs.org/)T6】纱:[https://yarnpkg.com/](https://yarnpkg.com/)T9】nuxt . js:[https://nuxtjs.org/](https://nuxtjs.org/)