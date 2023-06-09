# 将最近的内容组件添加到 vexpress

> 原文：<https://dev.to/raymondcamden/adding-a-recent-content-component-to-vuepress-5bnp>

几周前，一个新的静态站点生成器发布了， [VuePress](https://vuepress.vuejs.org/) 。基于 Vue.js，我立刻对它产生了兴趣。我只使用了几个小时，主要是为我想在[工作](https://goextend.io/)时构建的东西做原型，但我想我会分享我写的一小段代码。你应该假设这可能是错误的做法。正如我所说的，我对 VuePress 很陌生，但当我有所进展时，我喜欢分享它。

想象一下，你正在创建一个博客，或者一本烹饪书，你想在你的主页或者侧边导航中显示最新的内容。你会怎么做？

首先，VuePress 通过一个-wait it-$ site 变量提供对整个站点的访问。所以理论上，你可以这样做:

```
### Recent Cool Crap

<ul>
<li v-for="page in $site.pages">{{ page.title }}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是当你需要添加排序和过滤时就不行了。例如，在博客中，你可能只想要最新的博客条目。您不想包含主页或联系人页面。从理论上讲，你可能会在你的`v-for`那里发疯，但是当一个组件可以代替的时候，为什么要把你的布局弄得一团糟呢？

VuePress 支持[定制的全局组件](https://vuepress.vuejs.org/guide/using-vue.html#using-components),只需在。vue press/组件文件夹。在我的测试中，似乎我需要重启服务器才能让 VuePress 识别它，但这可能是另一个问题。对我来说，我创建了一个名为 RecentArticles.vue 的文件，然后让我把它放到主页:

```
## Recent Additions

<RecentArticles/>

Now let's look at the component.

<template>
<div>
    <ul>
        <li v-for="post in recentFiles">
            <a :href="post.path">{{post.title}}</a>
        </li>
    </ul>
</div>
</template>

<script>
export default {
    data() {
        return {};
    }, 
    computed:{
        recentFiles() {
            let files = this.$site.pages.filter(p => {
                return p.path.indexOf('/posts/') >= 0;
            }).sort((a,b) => {
                let aDate = new Date(a.frontmatter.published).getTime();
                let bDate = new Date(b.frontmatter.published).getTime();
                let diff = aDate - bDate;
                if(diff < 0) return -1;
                if(diff > 0) return 1;
                return 0;
            }).slice(0,5);

            return files;
        }
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

顶部只是处理显示，在我的例子中是一个基本的无序列表。关键位是计算属性`recentFiles`。为了实现这一点，我必须做一些假设。

首先，我假设所有的博客条目都在一个名为 posts 的路径中。VuePress 通过 path 属性返回每个页面的位置。我检查路径中的`/posts/`,以表明这个特定的文件是一篇文章。你还可以看到每个文件的封面，如果你想的话，你也可以在上面做个标记。

第二，我然后排序。我假设每篇文章在前面都会有一个`published`值。(我很高兴 VuePress 让我把 JSON 用于前台，而不仅仅是 YAML。)我解析发布的值，然后排序。

最后，我返回前五名。理论上，我可以将这个参数传递给组件，并将其默认为 5。事实上，我很确定我会这么做。见鬼，我甚至可以将它重新用于 RSS 提要。(有人写了一篇关于如何构建无 UI 组件的文章，但是我很难找到那个链接。如果我有，我会在下面发表评论。)

无论如何，希望这是有帮助的。如果我更多地使用 VuePress，我会分享更多的技巧！