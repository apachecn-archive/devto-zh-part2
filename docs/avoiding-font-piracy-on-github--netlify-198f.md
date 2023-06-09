# 避免 GitHub & Netlify 上的字体盗版

> 原文：<https://dev.to/fabe/avoiding-font-piracy-on-github--netlify-198f>

在阅读了*[GitHub 如何成为网络上最大的字体盗版网站(以及如何修复)](https://pixelambacht.nl/2017/github-font-piracy/)* 之后，我想知道如何才能避免在 GitHub 上上传/发布字体文件，考虑到我的大多数网站都是由 Netlify 部署的，并且要求所有文件都在存储库中。

起初，我试图用 7z 档案加密字体。不幸的是，7zip 不能在 Netlify 部署中使用，所以我决定在构建项目之前尝试从一个秘密的远程位置下载一个归档文件:

```
"scripts":  {  "build":  "npm run fonts:fetch && gatsby build",  "fonts:fetch":  "wget -O Fonts.zip $SECRET_FONTS_URL; unzip Fonts.zip -d ./static/fonts; rm Fonts.zip",  } 
```

我运行一个名为`fonts:fetch`的 NPM 脚本，它将从一个环境变量中指定的秘密位置下载一个 zip 文件。然后可以将这个 zip 文件提取到所需的位置，Netlify 可以在构建过程中使用它。

在我部署设置中，我可以设置秘密 URL:

[![](img/f70e92f89175899cf77965e79521f9cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nWfJ06yJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t3gv6j83e577im57v8wp.png)

...这确保了没有字体文件的痕迹留在存储库中！👮‍♂️