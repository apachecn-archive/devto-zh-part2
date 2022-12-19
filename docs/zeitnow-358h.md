# 指定要部署到 zeit now 的文件时的注意事项

> 原文：<https://dev.to/dala00/zeitnow-358h>

在 Zeit 的[Now](https://zeit.co/now) 中部署 Nuxt.js 的 APP 应用时，可以像官方手册中一样，用 now 命令简单地进行部署。 只是，有不顺利的模式。

[现在部署- Nuxt.js](https://nuxtjs.org/faq/now-deployment/)

## Symptoms

如果在部署后进行确认，则会显示安装 Nuxt 时的状态屏幕，而不是您创建的 APP 应用程序。

## Reason

. gitignore 指定的文件通常在部署到 Now 时会被忽略，但可以通过设置强制上传。 具体来说，可以通过在名为 now.json 的文件中设定 files 来实现。

```
{  "files":  {  "secret.js"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

恐怕，我觉得这样只会上传这个文件了。 package.json 可能很重要，所以会自动上传，Nuxt 会以默认状态安装并显示。

## Method of setting up

通过在 files 中指定所有文件夹和文件解决了。 (可能也有不需要的)

```
{  "files":  {  "assets",  "components",  "layouts",  "middleware",  "pages",  "plugins",  "static",  "store",  "secret.js",  "nuxt.config.js",  "yarn.lock"  }  } 
```

Enter fullscreen mode Exit fullscreen mode