# 打开灯:Hugo vs Hugo 配置文件

> 原文：<https://dev.to/hugo__df/switching-the-lights-on-hugo-vs-hugo-config-files-2m8f>

> 一个简单的 Hugo 博客设置

让[codewithhugo.com](https://codewithhugo.com)运转起来的故事。

**TL；**博士如下:

*   我总是对我的博客设置赞不绝口，它简单、快捷、好用
*   我用了 Hugo T1，“世界上最快的网站建设框架”，一个静态网站生成器
*   我用了一个主题， [casper-two](https://github.com/eueung/hugo-casper-two) ，这是雨果港的【https://ghost.org/】的[默认主题](https://ghost.org/)
*   我部署到 Cloudflare *后面的 GitHub 页面。

在部署传奇中有一个更新，在这里阅读:[“一个关于在 GitHub 发生灾难时迁移到 Netlify 的小案例研究，以 Cloudflare 为特色”](https://codewithhugo.com/a-tiny-case-study-about-migrating-to-netlify-when-disaster-strikes-at-github-featuring-cloudflare/)

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

*   [为什么我没有建立自己的网站🏃‍♂️](#why-i-didn-t-build-my-own-website)
*   [挑选一个主题并超越🖼的小事](#picking-a-theme-and-overriding-little-things)
*   [启用语法高亮显示🎨](#enabling-syntax-highlighting)
*   [形象和事物🤳](#image-and-things)
    *   [使用 imagemagick 调整大小](#resize-using-imagemagick)
    *   [将 PNG 转换为 JPG](#convert-png-to-jpg)
*   [部署还有更多](#deployment-and-more)
    *   [部署脚本](#deployment-script)
    *   [“清理”节点和 npm 脚本🙄](#cleaning-up-with-node-and-npm-scripts)
        *   [缩小和优化 CSS](#minify-and-optimise-css)
        *   [Imageoptim](#imageoptim)
    *   [在开发模式下运行](#running-in-development-mode)
*   [Cloudflare 设置](#cloudflare-setup)

## 为什么我没有建立自己的网站🏃‍♂️

我不是设计师，和 Hugo 一起写代码并不是让我学习或者炫耀我的网站制作技能，而是让我坚持不懈地写内容。
这就是为什么我用了一个模板，Hugo 和 GitHub 页面。这是一个静态网站，前面有一个 CDN (Cloudflare ),所以几乎不会出错。这意味着我*有*关注内容。

## 挑一个主题，压倒一点小事🖼

[卡斯珀两个](https://github.com/eueung/hugo-casper-two)，[ghost.org](https://ghost.org)默认主题是牛逼。
我做了一些调整，你可以在我的`static/overrides.css` :
中看到

```
.site-title {
    font-weight: 200;
    font-size: 8rem;
}
.collection-title {
    text-transform: uppercase;
    font-size: 6rem;
    font-weight: 300;
}
.site-header:before {
    background: rgba(0, 0, 0, 0.5);
}
// This was only needed when I enabled pygments for syntax highlighting
.site-wrapper {
    min-height: auto;
}
.highlight {
    width: 100%;
} 
```

在`config.toml` :
中添加那个东西

```
[params]
    customCSS = "overrides.css" 
```

我写的 CSS 就这么多🙂。

## 启用语法高亮显示🎨

codewithhugo.com 有一段时间没有语法高亮显示，😕我甚至开始抱怨。

[![<blockquote><p>When <a href="https://twitter.com/ThePracticalDev?ref_src=twsrc%5Etfw">@ThePracticalDev</a> has better syntax highlighting than your blog 😂😂 does anyone  know how to sort out <a href="https://twitter.com/GoHugoIO?ref_src=twsrc%5Etfw">@GoHugoIO</a> syntax highlighting <a href="https://t.co/PBJAolNlDY">https://t.co/PBJAolNlDY</a> <a href="https://t.co/2PXyY7l0dV">pic.twitter.com/2PXyY7l0dV</a></p>&mdash; Hugo Di Francesco (@hugo__df) <a href="https://twitter.com/hugo__df/status/984699817531203584?ref_src=twsrc%5Etfw">13 April 2018</a></blockquote>](img/11be08e88401220bee7049fd21eeee18.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y_wtpm9b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_3F94311F9467F8A3B0176D80603BB5F981B49B6D0E353DBB8DC8DC3DFEB48211_1533578484452_image.png)

我试了几次来启用它，它的本意是简单到只需在`config.toml` :
中输入以下内容

```
pygmentsUseClassic=false
pygmentsCodefences=true
pygmentsUseClasses=true 
```

这让我困惑了几个月，最终我发现我是在做这件事

```
[params]
  pygmentsUseClassic=false
  pygmentsCodefences=true
  pygmentsUseClasses=true 
```

戴上眼罩的经典案例。
作为这个过程的一部分，我让`highlight.js`开始工作，但是为了保持“这是一个静态的网站，所以几乎没有什么会出错”,使用客户端库来突出显示内容感觉有点不对。
因为语法高亮是在编译时完成的，所以`layouts/partials/js.html` :
中没有`highlight.js` JS

```
<!-- <script type="text/javascript" src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/highlight.min.js"></script> --> 
```

如果你想让它看起来更好，我们需要生成`syntax.css` :

```
$ hugo gen chromastyles --style=monokai > ./static/syntax.css 
```

并将其挂在`config.toml` :

```
[params]
  customCSS = [
    "overrides.css",
    "syntax.css"
  ] 
```

## 形象和事物🤳

### 使用 imagemagick 调整大小

使用
创建一个`/static/img`文件夹

```
$ mkdir -p static/img 
```

然后我们可以调整我将要使用的任何图片的大小(通常是封面照片):

```
$ convert my-image.jpg -resize 1500x1500 ./static/img/my-image.jpg 
```

### 将 PNG 转换为 JPG

我用炼金术将巴布亚新几内亚转换成 https://dawnlabs.io/alchemy/的 JPG，虽然我认为 ImageMagick 也能做到。

## 部署加更

### 部署脚本

`./scripts/deploy.sh`，摘自[雨果文件](https://gohugo.io/hosting-and-deployment/hosting-on-github/#put-it-into-a-script-1) :

```
#!/bin/bash
echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"
# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`
# Go To Public folder
cd public
# Add changes to git.
git add .
# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
    then msg="$1"
fi git commit -m "$msg"
# Push source and build repos.
git push origin master
# Come Back up to the Project Root
cd .. 
```

### “清理”节点和 npm 脚本🙄

这是完整的`package.json`(减去不必要的东西):

```
{  "name":  "codewithhugo",  "description":  "Repo for content of codewithhugo.com.",  "config":  {  "syntax":  "static/syntax",  "overrides":  "static/overrides",  "images":  "static/img"  },  "scripts":  {  "build":  "concurrently --names \"SYNTAX,OVERRIDES,IMAGES\" -c \"bgBlue.bold,bgMagenta.bold,bgYellow.bold\"  \"npm:build:css:syntax\"  \"npm:build:css:overrides\"  \"npm run build:imageoptim\"",  "predeploy":  "npm run build",  "deploy":  "./scripts/deploy.sh",  "serve":  "./scripts/serve.sh",  "start":  "npm run dev",  "dev":  "concurrently --names \"CSS,HUGO\" -c \"bgBlue.bold,bgMagenta.bold\"  \"npm:watch:css\"  \"npm:serve\"",  "watch:css":  "concurrently \"npm run build:css:syntax -- --watch --map inline\"  \"npm run build:css:overrides -- --watch --map inline\"",  "build:css:syntax":  "csso -i $npm_package_config_syntax.css -o $npm_package_config_syntax.min.css --stat",  "build:css:overrides":  "csso -i $npm_package_config_overrides.css -o $npm_package_config_overrides.min.css --stat",  "build:imageoptim":  "imageoptim $npm_package_config_images"  },  "author":  "Hugo Di Francesco",  "homepage":  "https://github.com/HugoDF/codewithhugo#readme",  "devDependencies":  {  "concurrently":  "^3.6.0",  "csso-cli":  "^1.1.0",  "imageoptim-cli":  "^2.0.3"  }  } 
```

让我们看看当我运行`npm run deploy`时会发生什么，这三个任务是主要的:

```
"build":  "concurrently --names \"SYNTAX,OVERRIDES,IMAGES\" -c \"bgBlue.bold,bgMagenta.bold,bgYellow.bold\"  \"npm:build:css:syntax\"  \"npm:build:css:overrides\"  \"npm run build:imageoptim\"",  "predeploy":  "npm run build",  "deploy":  "./scripts/deploy.sh", 
```

`npm`的一个特性是，如果你有一个名为`something`的脚本，你可以定义另一个脚本`presomething`，它将在`something`被调用之前运行。所以在部署之前，我们`npm run build`。
`npm run build`与`concurrently`([https://www.npmjs.com/package/concurrently](https://www.npmjs.com/package/concurrently))并行运行几个任务:

*   `npm:build:css:syntax`是`concurrently`—`npm run build:css:syntax`的特定简称
*   `npm:build:css:overrides`是`concurrently`—`npm run build:css:overrides`的特定简称
*   `npm run build:imageoptim`

#### 缩小并优化 CSS

```
"build:css:syntax":  "csso -i $npm_package_config_syntax.css -o $npm_package_config_syntax.min.css --stat",  "build:css:overrides":  "csso -i $npm_package_config_overrides.css -o $npm_package_config_overrides.min.css --stat", 
```

`build:css:syntax`和`build:css:overrides`在不同的文件上运行相同的命令。即文件是`$npm_package_config_syntax.css`和`$npm_package_config_overrides.css`，它们插入 npm_config 语法并评估为`static/syntax.css`和`static/overrides.css`(参见`package.json`中的`config.syntax`和`config.overrides`)。我们使用[https://github.com/css/csso-cli](https://github.com/css/csso-cli)，这几乎只是缩小了 CSS。

优化后的 CSS 文件输出为`static/syntax.min.css`和`static/overrides.min.css`，我也在`config.toml` :
中更新了它们

```
[params]
  customCSS = [
    "overrides.min.css",
    "syntax.min.css"
  ] 
```

#### Imageoptim

类似的方法用于`build:imageoptim` :

```
"build:imageoptim":  "imageoptim $npm_package_config_images" 
```

它对`$npm_package_config_images`(扩展到`static/img`)的内容运行 [imageoptim-cli](https://github.com/JamieMason/ImageOptim-CLI) 。

### 在开发模式下运行

现在我已经有了一个构建步骤…问题是我还需要观察开发过程中的变化等等，这是下面的脚本，来自`package.json` :

```
"serve":  "./scripts/serve.sh",  "start":  "npm run dev",  "dev":  "concurrently --names \"CSS,HUGO\" -c \"bgBlue.bold,bgMagenta.bold\"  \"npm:watch:css\"  \"npm:serve\"",  "watch:css":  "concurrently \"npm run build:css:syntax -- --watch --map inline\"  \"npm run build:css:overrides -- --watch --map inline\"", 
```

`./scripts/serve.sh` :

```
hugo serve . -F
# -D will serve even draft posts
# -F will serve future posts 
```

当我的文章还没有准备好发布时，我喜欢不必更改日期，所以我使用了`-F`标志。如果我不想某个帖子出现在开发中，我会在 frontmatter 中使用`draft: true`。
`npm run dev`只是在手表模式下运行`csso-cli`任务以及 Hugo dev 服务器。

# 获取完整的 RSS 源😄

默认的 Hugo RSS feed 没有完整的文章内容，只有摘要(在我的例子中，它会显示在主页上)。
这样做的问题是 RSS 源没有完整的帖子内容。
我知道这是个问题，但像往常一样，我从未抽出时间去解决它，直到我在推广它时让自己难堪😄：

[![<blockquote><p>If you have a blog and write/share useful things (big or small) about Web design / front-end dev and you provide an RSS feed to the blog, please respond to this tweet w/ the URL for it — I’d like to add more useful content feeds to my reader. 🙌🏻💃</p>&mdash; Sara Soueidan (@SaraSoueidan) <a href="https://twitter.com/SaraSoueidan/status/1020288178597613569?ref_src=twsrc%5Etfw">20 July 2018</a></blockquote>](img/b4a118db2041d8dda394cb110f5a2e88.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4qQKC2oa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_3F94311F9467F8A3B0176D80603BB5F981B49B6D0E353DBB8DC8DC3DFEB48211_1533580523884_image.png)

我对此的回答是:

> 嘿，我在[https://codewithhugo.com](https://t.co/tkBYwl47Q6)写文章，我不认为我得到的 RSS 订阅实际上有完整的帖子🙈
> 
> — Hugo Di Francesco ([@hugo__df](https://dev.to/hugo__df) ) 21 July 2018

为了解决这个问题，我找到了[https://randomgeekery . org/2017/09/15/full-content-Hugo-feeds/](https://randomgeekery.org/2017/09/15/full-content-hugo-feeds/)(那里有我们正在做的事情的完整解释)。本质上，它可以归结为创建下面的
`layouts/_default/rss.xml` :

```
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
    <channel>
    {{ if eq  .Title  .Site.Title }}{{ .Site.Title }}{{ else }}{{ with .Title }}{{.}} on {{ end }}{{ .Site.Title }}{{ end }}
    <link>{{ .Permalink }}</link>
    <description>Recent content {{ if ne  .Title  .Site.Title }}{{ with .Title }}in {{.}} {{ end }}{{ end }}on {{ .Site.Title }}</description>
    <generator>Hugo -- gohugo.io</generator>{{ with .Site.LanguageCode }}
    <language>{{.}}</language>{{end}}{{ with .Site.Author.email }}
    <managingEditor>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</managingEditor>{{end}}{{ with .Site.Author.email }}
    <webMaster>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</webMaster>{{end}}{{ with .Site.Copyright }}
    <copyright>{{.}}</copyright>{{end}}{{ if not .Date.IsZero }}
    <lastBuildDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</lastBuildDate>{{ end }}
    {{ with .OutputFormats.Get "RSS" }}
        {{ printf "<atom:link href=%q rel=\"self\" type=%q />" .Permalink .MediaType | safeHTML }}
    {{ end }}
    {{ range .Pages }}
    <item>
        {{ .Title }}
        <link>{{ .Permalink }}</link>
        <pubDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</pubDate>
        {{ with .Site.Author.email }}<author>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</author>{{end}}
        <guid>{{ .Permalink }}</guid>
        <description>{{ .Content | html }}</description>
    </item>
    {{ end }}
    </channel>
</rss> 
```

这只是默认模板，除了代替:

```
<description>{{ .Summary | html }}</description> 
```

我们做:

```
<description>{{ .Content | html }}</description> 
```

这使得构建的`index.xml`文件很大，因为它放入了每个帖子的完整内容，所以再次在[https://randomgeekery . org/2017/09/15/full-content-Hugo-feeds/](https://randomgeekery.org/2017/09/15/full-content-hugo-feeds/)上，我决定将提要条目的数量限制为 15 个(而不是那个帖子中建议的 20 个)，这是在`config.toml` :
中的更新

```
rssLimit = 15 
```

这完全归功于这篇文章[https://randomgeekery . org/2017/09/15/full-content-Hugo-feeds/](https://randomgeekery.org/2017/09/15/full-content-hugo-feeds/)，这篇文章实际上解释了所做的事情的方式和原因。

## 云闪设置

我打开了几个设置，特别是强制 HTTPS:cloud flare Dashboard > Crypto > Always use HTTPS > on，在同一个部分 Crypto >自动 HTTPS 重写> On。

我将缓存>浏览器缓存过期时间增加到 8 天(可能/应该更多，但我不想对我的资产进行版本控制),并打开缓存>始终在线，这样即使 GitHub 页面崩溃，Cloudflare 仍将为我的内容提供服务，一切都会好的。

除了几张额外的图片和在`config.toml`中设置字符串，这差不多就是我为[codewithhugo.com](https://codewithhugo.com)设置的全部 Hugo 了。

帕克·惠特森