# 为 Jekyll 博客建立一个统计页面

> 原文：<https://dev.to/raymondcamden/building-a-stats-page-for-jekyll-blogs-4ief>

当我在 Hugo 上运行这个博客时，我建立了自己的小统计脚本([一个简单的统计脚本 Hugo](https://www.raymondcamden.com/2016/12/01/a-simple-stats-script-for-hugo))来帮助我从高层次上看我的博客。几个月前我转换成了[杰基尔](https://jekyllrb.com/)，最近我开始在我的网站上重建这种支持。接下来是我自己尝试创建一个脚本来报告基于 Jekyll 的博客内容。我下面要展示的所有代码都在我的 GitHub repo 上，我会在最后分享具体的链接。这只是一个开始，我有更多的统计我想补充一些想法，但我很想听听你的想法，所以给我一行。

首先也是最重要的一点——我决定*不*想用我在雨果剧本中用过的方法。在那篇文章中，我使用一个本地节点脚本来生成一组 JSON 数据。然后，我将它复制到一个简单的 web 应用程序中，该应用程序使用 JavaScript 来呈现数据。相反，我想要的是不需要任何手工操作的东西。为此，我写了两个脚本。

第一个是使用 Liquid (Jekyll 的模板引擎)输出原始数据的`stats.json`文件。第二个剧本是`stats.md`。这将使用 Vue.js 加载 JSON，然后将其呈现出来。我们先来看看 JSON 脚本。

在我展示代码之前，让我展示输出:

```
{
    "totalPosts":15,
    "totalCategories": 8,
    "totalTags": 6,
    "totalWords": 13757,
    "averageWordsPerPost": 917,
    "firstPost": {
        "title":"Adding a Recent Content Component to VuePress",
        "published":"2018-05-09",
        "url":"/2018/05/09/adding-a-recent-content-component-to-vuepress"
    },
    "lastPost": {
        "title":"Building a Stats Page for Jekyll Blogs",
        "published":"2018-07-21",
        "url":"/2018/07/21/building-a-stats-page-for-jekyll-blogs"
    },
    "postsPerCategory":[
            { "name": "static sites", "size":3 }
            ,
            { "name": "misc", "size":1 }
            ,
            { "name": "serverless", "size":2 }
            ,
            { "name": "ColdFusion", "size":1 }
            ,
            { "name": "Uncategorized", "size":1 }
            ,
            { "name": "development", "size":3 }
            ,
            { "name": "javascript", "size":1 }
            ,
            { "name": "Serverless", "size":3 }
    ],
    "postsPerTag":[
            { "name": "vuejs", "size":4 }
            ,
            { "name": "development", "size":3 }
            ,
            { "name": "webtask", "size":2 }
            ,
            { "name": "visual studio code", "size":1 }
            ,
            { "name": "azure", "size":3 }
            ,
            { "name": "jekyll", "size":1 }  
    ],

    "dates":"2018-07-21,2018-07-16,2018-07-06,2018-07-02,2018-06-18,2018-06-15,2018-06-13,2018-06-11,2018-06-07,2018-06-04,2018-05-22,2018-05-21,2018-05-16,2018-05-15,2018-05-09"

} 
```

虽然从值的名称来看应该很明显，但是让我来看一下统计数据:

*   帖子总数、类别总数和标签总数。在这三者中，只有总帖子是真正有趣的，但我认为记录你有多少标签和类别是有意义的。太多可能反映出缺乏编辑重点。
*   总的来说是 100%的愚蠢。每篇文章的平均字数肯定更相关。
*   从简单的历史角度来看，第一篇和最后一篇文章很有趣。
*   每个类别和标签的文章让你知道你的内容集中在哪里。
*   最后-日期列表。这就是我遇到液体问题的地方。我想创建一个“索引”,表示每年、每月、每周等的帖子。这在液体中变得异常困难。然后我想——如果我简单地输出所有的日期并让客户机处理它会怎么样？我担心数据的大小，但是即使在我的有近 6000 个条目的博客上，JSON 也只有大约 68K。我的 JSON 中确实有很多空白(我在上面已经去掉了)，所以还有改进的空间，但是现在我很满意。

现在我们来看看这背后的脚本:

```
---
layout: null
---

{% assign totalWords = 0 %}
{% assign dateOb = '' %}

{% for post in site.posts %}
    {% assign postWords = post.content | number_of_words %}
    {% assign totalWords = totalWords | plus: postWords %}
    {% assign pd = post.date | date: "%Y-%m-%d" %}
    {% unless forloop.first %}
        {% assign dateOb = dateOb | append: "," %}
    {% endunless %}
    {% assign dateOb = dateOb | append: pd %}
{% endfor %}

{% assign avgWords = totalWords | divided_by: site.posts.size %}

{
    "totalPosts":{{ site.posts.size }},
    "totalCategories": {{ site.categories.size }},
    "totalTags": {{ site.tags.size }},
    "totalWords": {{ totalWords }},
    "averageWordsPerPost": {{ avgWords }},
    "firstPost": {
        "title":"{{ site.posts.last.title }}",
        "published":"{{ site.posts.last.date | date: "%Y-%m-%d" }}",
        "url":"{{site.posts.last.url}}"
    },
    "lastPost": {
        "title":"{{ site.posts.first.title }}",
        "published":"{{ site.posts.first.date | date: "%Y-%m-%d" }}",
        "url":"{{site.posts.first.url}}"
    },
    "postsPerCategory":[
        {% for category in site.categories %}
            {% assign cat = category[0] %}
            {% unless forloop.first %},{% endunless %}
            { "name": "{{cat}}", "size":{{site.categories[cat].size}} }
        {% endfor %}
    ],
    "postsPerTag":[
        {% for tag in site.tags %}
            {% assign tagName = tag[0] %}
            {% unless forloop.first %},{% endunless %}
            { "name": "{{tagName}}", "size":{{site.tags[tagName].size}} }
        {% endfor %}
    ],
    "dates":"{{ dateOb }}"

} 
```

我从浏览每一篇文章开始，收集我的文字和数据。一旦有了这些，剩下的内容就很容易生成了。请注意，`site.posts`的`first`和`last`值是相反的，因为`site.posts`的时间顺序相反。(非常感谢来自[哲基尔论坛](https://talk.jekyllrb.com/t/question-about-site-posts-values/1972/7)的@ mmistakes。)

这就是“后端”——尽管要明确——当我发布我的站点时，它只运行一次，并作为原始 JSON 输出。你可以在这里看到输出[。现在为“前端”:](https://www.raymondcamden.com/stats.json) 

```
---
layout: page
title: "Stats"
---

<style>
[v-cloak] {display: none}
</style>

{% raw %}
<div id="app" v-cloak="">
    <table>
        <tr>
            <td width="30%">Total Posts:</td>
            <td width="70%">{{totalPosts}}</td>
        </tr>
        <tr>
        <td>First Post:</td>
        <td>
        <a :href="firstPost.url">{{firstPost.title}}</a> published {{firstPost.age}} on {{firstPost.date}}
        </td>
        </tr>
        <tr>
        <td>Last Post:</td>
        <td>
        <a :href="lastPost.url">{{lastPost.title}}</a> published {{lastPost.age}} on {{lastPost.date}}
        </td>
        </tr>
        <tr>
        <td>Total Words Written:</td>
        <td>{{totalWords}}</td>
        </tr>
        <tr>
        <td>Average Words per Post:</td>
        <td>{{avgWords}}</td>
        </tr>
    </table>

    <h3>Posts Per Year</h3>
    <table>
        <tr>
            <td>Year</td>
            <td>Number of Posts</td>
        </tr>
        <tr v-for="year in sortedYears">
            <td>{{year}}</td>
            <td>{{years[year]}}</td>
        </tr>
    </table>

    <h3>Posts Per Category</h3>
    <table>
        <tr>
            <td>Category</td>
            <td>Number of Posts</td>
        </tr>
        <tr v-for="cat in sortedCats">
            <td>{{cat.name}}</td>
            <td>{{cat.size}}</td>
        </tr>
    </table>

    <h3>Posts Per Tag</h3>
    <table>
        <tr>
            <td>Tag</td>
            <td>Number of Posts</td>
        </tr>
        <tr v-for="tag in sortedTags">
            <td>{{tag.name}}</td>
            <td>{{tag.size}}</td>
        </tr>
    </table>

</div>
{% endrawx %}

<script src="https://cdn.jsdelivr.net/npm/moment@2.22.2/moment.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script>
new Vue({
    el:'#app',
    data:{
        totalPosts:0,
        firstPost:{
            title:"",
            date:"",
            url:""
        },
        lastPost:{
            title:"",
            date:"",
            url:""
        },
        totalWords:0,
        avgWords:0,
        years:{},
        cats:[], 
        tags:[]
    },
    created:function() {
        fetch('/stats.json')
        .then(res => res.json())
        .then(res => {
            console.log(res);
            this.totalPosts = res.totalPosts;

            this.firstPost = {
                title:res.firstPost.title,
                date:res.firstPost.published,
                url:res.firstPost.url,
                age:moment(res.firstPost.published).fromNow()
            };

            this.lastPost = {
                title:res.lastPost.title,
                date:res.lastPost.published,
                url:res.lastPost.url,
                age:moment(res.lastPost.published).fromNow()
            };

            this.totalWords = res.totalWords;
            this.avgWords = res.averageWordsPerPost;

            let dates = res.dates.split(',');
            // process res.dates on the client site
            dates.forEach(d => {
                let year = new Date(d).getFullYear();
                if(!this.years[year]) Vue.set(this.years,year,0);
                Vue.set(this.years,year, this.years[year]+1);
            });

            this.cats = res.postsPerCategory;
            this.tags = res.postsPerTag;

        }).catch(e => {
            console.error(e);
        });
    },
    computed:{
        sortedCats:function() {
            return this.cats.sort((a,b) => {
                if(a.name < b.name) return -1;
                if(a.name > b.name) return 1;
                return 0;
            });
        },
        sortedTags:function() {
            return this.tags.sort((a,b) => {
                if(a.name < b.name) return -1;
                if(a.name > b.name) return 1;
                return 0;
            });
        },
        sortedYears:function() {
            return Object.keys(this.years).sort();
        }
    }
});
</script> 
```

(注意——由于试图在源代码中向浏览器呈现液体内容的问题，我将上面的一个`endraw`标记重命名为`endrawx`。在 GitHub 中是正确的。)所以这是一个相当琐碎的 Vue 应用。我取出 JSON，然后开始赋值。我唯一真正做的工作是解析日期。现在我只是呈现一个“每年”的统计，但我可能会添加一个“每月”和“每道”的表格。你可以在这里查看我博客的统计结果:【https://www.raymondcamden.com/stats[。](https://www.raymondcamden.com/stats)

如果您自己想要代码，您可以从这里获得这两个脚本:

*   [https://github . com/cfjedimaster/raymondcamden 2018/blob/master/stats . JSON](https://github.com/cfjedimaster/raymondcamden2018/blob/master/stats.json)
*   [https://github . com/cfjedimaster/raymondcamden 2018/blob/master/stats . MD](https://github.com/cfjedimaster/raymondcamden2018/blob/master/stats.md)

你觉得怎么样？你有什么要补充的吗？下面给我留个评论吧！