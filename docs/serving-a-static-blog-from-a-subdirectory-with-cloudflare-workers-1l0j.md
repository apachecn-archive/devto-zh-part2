# 使用 Cloudflare Workers 从子目录提供静态博客服务

> 原文：<https://dev.to/statushero/serving-a-static-blog-from-a-subdirectory-with-cloudflare-workers-1l0j>

几年前我们第一次发表博客时，我们决定用 Jekyll。它是简单的，基于 Ruby 的，成熟的，并且很好地完成了这一任务。我们坚持了下来。

[根据专家的说法](https://moz.com/blog/subdomains-vs-subfolders-rel-canonical-vs-301-how-to-structure-links-optimally-for-seo-whiteboard-friday)，将你的博客放在子目录(`statushero.com/blog`)而不是子域(`blog.statushero.com`)中有一个 SEO 优势。有些人(包括谷歌)声称，当你刚开始写博客时，这种差别可以忽略不计，但在当时这似乎很容易实现，为什么不呢。

### 又快又脏

由于我们的主应用程序和登录页面是一个每周部署多次的 Rails 应用程序，我们只是发布了从 Jekyll 到`public/blog`的静态输出，并使用了一些 git 子模块技巧。博客文件几乎会立即缓存在 Cloudflare CDN 中，几乎不会引发 Rails 请求。

### Hax

正如您可能已经猜到的那样，由于各种原因，这种耦合很麻烦，而且有点笨拙。在 GitHub pages 上托管博客会容易得多——用 Jekyll 来说是小菜一碟——快速发布微小的变化，并向那些没有 prod 访问权限的人开放博客发布步骤。

nginx 反向代理可以完成这项工作，但是我们使用 Puma 应用服务器提供 Heroku 的 Rails 应用，所以要做到这一点，我们必须在堆栈中的某个地方插入额外的基础设施。(或许通过转用 Phusion。)这种权衡似乎不值得。

### 输入 Cloudflare 工人

我们正要设置重定向并切换回`blog.statushero.com`，这时我们偶然发现了 [Cloudflare 工作人员](https://developers.cloudflare.com/workers/about/)。请看一看——这是一个强大的产品。几行 Javascript 代码就解决了我们的问题:

```
addEventListener('fetch', event => {
  var url = new URL(event.request.url);
  if (url.pathname.startsWith('/blog/') || url.pathname === '/blog') {
    handleBlog(event, url);
  } else {
    event.respondWith(fetch(event.request));
  }
})

async function handleBlog(event, url) {
  var originUrl = url.toString().replace(
    'https://statushero.com/blog',
   'https://8012labs.github.io/statushero-blog');
  event.respondWith(fetch(originUrl)); 
} 
```

Enter fullscreen mode Exit fullscreen mode

Cloudflare 甚至为您提供了一个漂亮的基于浏览器的小 UI 来预览和测试您的工作代码。

现在我们拥有了两个世界的精华——一个方便的 Jekyll/GitHub 页面组合的博客子目录。