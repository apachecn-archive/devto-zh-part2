# 静态信号(移动到静态站点)

> 原文：<https://dev.to/teej/a-signal-in-the-static-moving-to-a-static-site-2icn>

大约一年前，我开始重建我的网站，在 PHP 上长大的我选择了 Craft CMS，当时它还在测试阶段。我觉得在它还在开发的时候学习它会很有趣，也许还可以创建我自己的插件。

哈哈。笑话。我没有。虽然我试过，但从来没有真正需要任何额外的东西。

我还使用 Laravel Forge 在我将任何变更推送到主分支时自动部署。直到今天，这仍然是一个很好的方法，但是有很多活动的部分。我有一个 repo，一个需要照看的服务器(比如我更新 PHP 的时候坏了一切)，一个需要备份的数据库，一个需要保持更新的带有一些插件的 CMS。当然，我不可能在第一天后就碰它，它仍然会像以前一样工作，但那不是我。如果它没坏，去修理它。

这就是静态站点生成器的魅力所在。就网站而言，所有东西都在一个地方。内容存在于降价文件中，这使得内容非常容易移植。在看到一些关于 Hugo 的推荐后，我决定使用 Hugo，在大约一天的时间里，我复制了整个网站，包括 CSS 和 JS。为了复制内容，我利用 Craft 的[元素 API](https://github.com/craftcms/element-api) 插件来获取我所有帖子的 JSON 对象。插件的配置文件如下所示:

```
<?php

use craft\elements\Entry;
use craft\helpers\UrlHelper;

return [
  'endpoints' => [
    'posts.json' => [
      'elementType' => Entry::class,
      'criteria' => ['section' => 'posts'],
      'transformer' => function(Entry $entry) {
        return [
          'title' => $entry->title,
          'url' => $entry->url,
          'date_published' => $entry->postDate->format(\DateTime::ATOM),
          'slug' => $entry->slug,
          'body' => $entry->postContent,
          'categories' => $entry->categories->all()
        ];
      },
    ]
  ]
]; 
```

Enter fullscreen mode Exit fullscreen mode

所以当它点击`https://my-site.com/posts.json`时，它返回了我需要的所有东西。我把它保存到一个文件中，这样可以快速生成降价版本:

```
const posts = require('./data.json')
const fs = require('fs')

function buildCategory({ title }) {
  return `- ${title}`
}

function buildFrontMatter(post) {
  return `---
title: "${post.title.replace(/"/g, '\'')}"
date: ${post.date_published} draft: false
categories: \n${post.categories.map(buildCategory).join(`\n`)} ---`
}

posts.data.forEach(post => {
  let content = `${buildFrontMatter(post)}\n${post.body}`

  fs.writeFile(`../path/to/final/content/posts/${post.slug}.md`, content, err => {
    if(err) {
      return console.log(err);
    }

    console.log("The file was saved!");
  }); 
}) 
```

Enter fullscreen mode Exit fullscreen mode

我需要在上面运行`sudo node index.js`,因为它需要创建文件，但是一旦生成的降价文件在正确的文件夹中，我就不必去碰它们了。

一旦完成，我就跳到 [Netlify](https://www.netlify.com/) 并连接上它。在一切正常工作之前，我需要做一些配置。这主要是因为我的资产存在于一个子文件夹的主题中。为了让 Netlify 知道这一点，我必须在根目录下创建一个新的`package.json`,用一个`build`命令来完成以下任务:

```
[build]
publish = "public"
command = "hugo --minify && npm run build" 
```

Enter fullscreen mode Exit fullscreen mode

然后，我的根目录`package.json`中的构建命令深入到它需要的文件夹中，并运行更多的命令:

```
"scripts":  {  "build":  "cd ./themes/nua && npm install && npm run production"  } 
```

Enter fullscreen mode Exit fullscreen mode

之后就一帆风顺了。利默里克刚刚赢得了全爱尔兰高中投掷决赛，所以我要去拿瓶啤酒。今天取得了两次胜利。干杯，agus Luimneach Abú！！！