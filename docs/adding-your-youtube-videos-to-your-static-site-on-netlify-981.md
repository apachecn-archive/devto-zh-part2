# 将您的 YouTube 视频添加到 Netlify 上的静态站点

> 原文：<https://dev.to/raymondcamden/adding-your-youtube-videos-to-your-static-site-on-netlify-981>

本月早些时候，我写了一篇博文，演示了如何使用客户端 JavaScript 在你的网站上呈现你的 YouTube 视频列表([“使用 Vue.js 将你的 YouTube 视频添加到你的静态网站”](https://dev.to/raymondcamden/adding-your-youtube-videos-to-your-static-site-with-vuejs-30o3-temp-slug-7124918))。这工作得很好，没有使用任何“花哨”的 JavaScript，这对旧浏览器来说是有问题的(技术上来说`fetch`有点现代，但你可以用任何其他 HTTP 调用来代替它),但我很好奇是否有一种方法可以在我用 [Jekyll](https://jekyllrb.com/) 构建的静态站点中实现。

虽然可以构建一个插件来执行 HTTP 调用以生成输出，但是我不想走这条路，因为，嗯，Ruby，然后我想起了 Phil Hawksworth 曾经演示过的东西。菲尔是 [Netlify](https://www.netlify.com/) 的开发者拥护者，我用它来托管和部署这个网站。我经常在这个博客(和推特)上对他们赞不绝口，所以我是他们的粉丝。菲尔写了一篇关于他如何向他的静态站点添加评论的文章:[向我的 Jekyll Build 添加静态评论系统](https://www.hawksworx.com/blog/adding-a-static-comments-system-to-my-jekyll-build/)。

他的想法非常棒。他编写了一个 Gulp 脚本，通过一个简单的 API 获取他的评论数据，将它写到一个 Jekyll 数据文件中，然后当他的网站建立起来时，该数据文件用于驱动他的评论内容。Netlify 允许你在部署时指定一个构建命令，通常我只使用`jekyll`，但是通过使用 Gulp 脚本，你可以做更多的事情。

这里有一个简单的例子。我没有为我自己的网站这样做，因为我有点胆小，所以我只是在命令行为 Jekyll 生成了一个默认的新博客。接下来，我写了我的吞咽脚本。(我已经好几年没写 Gulp 剧本了，有错见谅。这里的任何好东西都来自菲尔的原始剧本。)

```
var gulp = require('gulp');
var gutil = require('gulp-util');
var shell = require('gulp-shell');
var yaml = require('json2yaml');
var fs = require('fs');
var runSequence = require('run-sequence')

var paths = {
    source:'site/',
    deploy: 'site/dist'
};

// build jekyll
gulp.task('jekyll', function() {
    return gulp.src('', {quiet: false})
    .pipe(shell([
        'rm -rf ' + paths.deploy,
        'jekyll build -s '+paths.source + ' -d '+paths.deploy
    ]));
});

// Get YT videos
gulp.task("get:videos", function() {

    console.log("Getting YT videos");
    //rss url for your videos
    const url = 'https://www.youtube.com/feeds/videos.xml?channel_id=UC8KROrnEHSnnV3z5J_FoSIg';

    let Parser = require('rss-parser');
    let parser = new Parser();

    let videos = [];

    (async () => {
        let feed = await parser.parseURL(url);

        feed.items.forEach(item => {
            //create a videoId from id
            item.videoId = item.id.split(":").pop();
            videos.push(item);
        });

        let ymlText = yaml.stringify(videos);

        fs.writeFile('./site/_data/videos.yml', ymlText, function(err) {
            if(err) {
                console.log(err);
            } else {
                console.log("Video data saved.");
            }
        });

    })();

});

gulp.task('build', function(callback) {
  runSequence(
    'get:videos',
    'jekyll',
    callback
  );
});

// The default task.
gulp.task('default', ['build']); 
```

这里重要的一点是`get:videos`任务。我使用了 npm 的 RSS 解析器，名为，wait it，`rss-parser`，它工作得很好，除了它似乎没有获得我自己的代码在[客户端版本](https://dev.to/raymondcamden/adding-your-youtube-videos-to-your-static-site-with-vuejs-30o3-temp-slug-7124918)中的属性之一，特别是在嵌入中使用的`videoId`。幸运的是，我可以手动得到它，正如你在这里看到的:

```
//create a videoId from id
item.videoId = item.id.split(":").pop(); 
```

我特别喜欢为哲基尔输出 YAML 是多么容易

```
let ymlText = yaml.stringify(videos); 
```

一旦它被写成数据文件，就很容易在模板中使用:

```
---
layout: page
title: "Videos"
permalink: /videos/
---

{% for video in site.data.videos %}

<iframe id="ytplayer" type="text/html" width="640" height="360" src="https://www.youtube.com/embed/{{video.videoId}}?autoplay=0&origin=http://example.com" frameborder="0"></iframe>

{% endfor %} 
```

最后一步是在我的网站的网络设置中指定吞咽任务:

[![Netlify settings](img/7052af3666d5688a9c756e6d888359b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ap8hja21--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/08/netlify1.jpg)

就是这样！如果你想真正看到这个，你可以在这里查看现场:[https://fervent-beaver-e5dc28.netlify.com/videos/](https://fervent-beaver-e5dc28.netlify.com/videos/)。这个演示的源代码在这里:【https://github.com/cfjedimaster/jekyllnetlifydemo T2】