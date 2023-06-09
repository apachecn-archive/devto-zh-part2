# 即时测试任何 OAuth/OAuth2 提供者的任何 API 端点的实时代码片段

> 原文：<https://dev.to/neth_6/live-code-snippets-to-instantly-test-any-api-endpoints-of-any-oauthoauth2-providers-11n0>

我们都有过这样的经历，在找到一个我们认为可以返回一些我们想要的关于用户的数据，或者可以代表用户执行一些事情的端点之前，我们仔细研究了文档记录不良的 OAuth API 端点，其中一些已经被否决甚至停止使用。在我们可以测试这个端点之前，我们花了无数的时间在我们的前端代码上编写了一个社交登录按钮，它将与后端通信，以完成 OAuth 流程，从 OAuth 提供商(如脸书、Twitter、Github 等)获取访问令牌。，最后是关键时刻——用访问令牌调用 API 端点。

然后*然后*——端点没有做我们期望的事情，因为它的行为已经改变或者文档不准确。

任何开发者都不应该经历这些！因此，我们用所有现成的 OAuth 片段拼凑了一个站点，为每个流行的提供者创建一个社交登录引导按钮，但这还不是全部，该站点包括一个用于调用这些提供者的每个流行 API 端点的片段。

听起来难以置信？让我们旋转转盘，选择一个随机动作在 OAuth 提供者上执行。

我们开始吧..........。。。。。。'在 Github 中搜索存储库'

你可以点击[这里的](https://tome.oauth.io/providers/github/search-a-repository?utm_source=dev-to&utm_medium=post&utm_campaign=oauth-pain)来测试 Javascript 代码片段，以立即“在 Github 中搜索存储库”,并调整它以调用其他 Github API 端点。

如果您想抢先一步，测试其他 OAuth 提供者，请点击这里的。

下面是工作代码的简要描述。

首先，代码很短，即< 15 lines, all front-end Javascript, with no backend. Secondly, it has few external requirements.

```
$('#github-button').on('click', function() {
  // Initialize with your OAuth.io app public key
  OAuth.initialize('txp2rKYpuKZXaaYC5kB-m13KnVE');

  // Use popup for oauth
  // Alternative is redirect
  OAuth.popup('github').then(provider => {

    // Got the OAuth provider object, which contains access token, etc.
    console.log('provider:', provider);

    provider.get('/search/repositories?q=oauth-io').then(data =>            {
      console.log('Repositories:', data);
    });
  });
}) 
```

必要要求:

*   用于创建社交按钮的 CSS
    *   bootstrap - [https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css)
    *   bootstrap-social-[https://cdnjs . cloud flare . com/Ajax/libs/bootstrap-social/4 . 12 . 0/bootstrap-social . min . CSS](https://cdnjs.cloudflare.com/ajax/libs/bootstrap-social/4.12.0/bootstrap-social.min.css)
    *   font-awesome-[https://maxcdn . bootstrapcdn . com/font-awesome/4 . 7 . 0/CSS/font-awesome . min . CSS](https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css)
*   DOM 操作
    *   jquery-[https://code . jquery . com/jquery-3 . 2 . 1min . js](https://code.jquery.com/jquery-3.2.1.min.js)
*   OAuth magic by OAuth.io
    *   OAuth.io Javascript 库-[https://cdn . raw git . com/oauth-io/oauth-js/c5af 4519/dist/oauth . js](https://cdn.rawgit.com/oauth-io/oauth-js/c5af4519/dist/oauth.js)

如果你想在你自己的网页上使用 Javascript 代码片段，你可以复制粘贴它，但是注意你需要 [https://oauth.io](https://oauth.io) 来使它工作。如果你对整个设置感兴趣，我可以做一个单独的帖子。