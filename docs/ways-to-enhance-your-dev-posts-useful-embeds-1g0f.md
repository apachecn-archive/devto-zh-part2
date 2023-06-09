# 增强你的开发帖子的方法！有用的嵌入。

> 原文：<https://dev.to/devteam/ways-to-enhance-your-dev-posts-useful-embeds-1g0f>

按照字母顺序，这里是我们当前的液体标签嵌入选项，以及您需要在帖子中包含它们的语法:)

# 1。密码笔

你需要钢笔的完整链接:

`{% codepen https://codepen.io/cassidoo/pen/NbOzKg %}`
[https://codepen.io/cassidoo/embed/NbOzKg?height=600&default-tab=result&embed-version=2](https://codepen.io/cassidoo/embed/NbOzKg?height=600&default-tab=result&embed-version=2)T3】

# 2。偏差

### a)注释

你需要在评论 url/permalink 的末尾添加 id:
`{% devcomment 40fh %}`

[![kayis profile image](img/e662378fc6ec45f59882254ec51a89b0.png) ](/kayis) [ K (he/him) ](/kayis) • [<time datetime="2018-06-23T21:04:25Z"> Jun 23 '18 </time> • Edited on <time datetime="2018-06-23T21:09:27Z">Jun 23</time>](https://dev.to/kayis/comment/40fh) 

是的，咖喱食品非常适合这个。箭头语法有助于这一点。

```
valueChange = (key) => {
  return function (e) {
    var obj= {};
    state[key] = e.target.value; //<-- this is a bug btw.
    this.setState(obj);
  }.bind(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
valueChange = key => e => this.setState(oldState => ({
  ...oldState,
  [key]: e.target.value
})) 
```

Enter fullscreen mode Exit fullscreen mode

### b)播客插曲

仅适用于在[https://dev.to/pod](https://dev.to/pod)找到的播客，你需要完整链接。如果你想把你的播客包括进来，请发邮件给我们( [yo@dev.to](mailto:yo@dev.to) )。
`{% podcast https://dev.to/basecspodcast/s2e2--queues-irl %}`

 [# S2:E2 -“队列 IRL”](/basecspodcast/s2e2--queues-irl)  [## Base.cs 播客](/basecspodcast) ![play](img/a050b8a9eefb2d39a5b05718b562d873.png) ![pause](img/7c6666d3dc1fc4d4c80a4ac7094bea6e.png) ![Base.cs Podcast](img/394107fa0a72cbdb4e706d54fc63451f.png)

<audio id="audio" data-episode="s2e2--queues-irl" data-podcast="basecspodcast"><source src="https://dts.podtrac.com/redirect.mp3/media.blubrry.com/basecs_podcast/content.blubrry.com/basecs_podcast/BP_S2E2_EP10.mp3" type="audio/mpeg"> Your browser does not support the audio element.</audio>

[![S2:E2 - "Queues IRL"](img/9b9f1e90415296f4b5709285f75827f9.png) ![animated volume bars](img/ac7b6f98be11df3dc276473b9bcc0146.png) ](/basecspodcast/s2e2--queues-irl)  ![play](img/a050b8a9eefb2d39a5b05718b562d873.png) ![pause](img/7c6666d3dc1fc4d4c80a4ac7094bea6e.png)     ![volume](img/ef9e7c54b19fd971897507e0eeec97b6.png)   <input aria-label="Volume" type="range" name="points" id="volumeslider" value="50" min="0" max="100" data-show-value="true">    ![volume-mute](img/0aa7142411eaec14969395a9618eb6ab.png)  1x  initializing... × 

### c)岗位

你需要这篇文章的完整链接:
`{% link https://dev.to/kazz/boost-your-productivity-using-markdown-1be %}`

## 文章不再可用

### d)用户

你只需要一个用户名:
`{% user ben %}`

[![ben image](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben)

## [本·哈尔彭](/ben) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;User&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:1,&quot;name&quot;:&quot;Ben Halpern&quot;}" class="crayons-btn follow-action-button whitespace-nowrap  " aria-label="Follow user: Ben Halpern" aria-pressed="false">跟随</button>

[A Canadian software developer who thinks he’s funny. He/Him.](/ben)

# 3。开源代码库

### 一)要诀

你需要要点的完整链接:
`{% gist https://gist.github.com/QuincyLarson/4bb1682ce590dc42402b2edddbca7aaa %}`