# 纱线全局包装安装位置

> 原文：<https://dev.to/c33s/yarn-global-package-install-location-16p4>

## 命令

### 全局安装包

```
yarn global add ladon
npm install -g ladon 
```

Enter fullscreen mode Exit fullscreen mode

### 更新一个包

```
npm update ladon --global
yarn global upgrade ladon 
```

Enter fullscreen mode Exit fullscreen mode

## 纱线全局安装位置

*   窗口`%LOCALAPPDATA%\Yarn\config\global`例如:`C:\Users\username\AppData\Local\Yarn\config\global`
*   OSX 和非根 Linux `~/.config/yarn/global`
*   Linux，如果以 root 用户身份登录`/usr/local/share/.config/yarn/global`

另外，不要忘记将纱箱文件夹添加到您的路径中:

例如:

```
C:\Users\username\AppData\Local\Yarn\bin 
```

Enter fullscreen mode Exit fullscreen mode

## 链接

*   [https://github.com/yarnpkg/yarn/issues/2049](https://github.com/yarnpkg/yarn/issues/2049)
*   [https://yarnpkg.com/en/docs/cli/global](https://yarnpkg.com/en/docs/cli/global)
*   封面图片由 Susan np4/Susan Mielke[https://pix abay . com/en/cat-young-animal-quickly-wildcat-2083492/](https://pixabay.com/en/cat-young-animal-curious-wildcat-2083492/)