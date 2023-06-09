# 对未能建造的咆哮

> 原文：<https://dev.to/r0mflip/a-rant-on-failing-to-build-197g>

## 一个卑微的开始

我喜欢节点服务器，我喜欢写裸机服务器，我也喜欢 Express.js，我认为自己是一个节点初学者。JS 但是我只是想写一个快速的中间件来生成 [ETag 头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag)。ETags 是使缓存无效的一种全新而简洁的方法。

起初，当我读到 ETag 头的时候，我认为有“强”和“弱”的变体是没有用的，后来我知道我错了。“strong”ETags 表示基于响应体的字节生成头值。这意味着当我为任何给定的内容生成 ETag 时，它需要对该内容是唯一的，换句话说，这意味着强 ETag 是由抗冲突算法生成的内容的散列。

## 华而不实的代码

从中间件捕获响应体非常容易。大概是这样的...

```
const crypto = require('crypto');

const taggart = opts => {
  // standard express style
  return (req, res, next) => {
    // save methods
    const write = res.write;
    const end = res.end;

    // sha1 ain't that bad
    const hash = crypto.createHash('sha1');

    // keep track, for content-length
    let length = 0;

    const onData = (chunk, encoding) => {
      // sometimes chunk can be 'undefined'
      if (!chunk) {
        return;
      }

      // convert chunk to buffer
      chunk = Buffer.isBuffer(chunk) ? chunk : Buffer.from(chunk, encoding);

      // update hash using chunk data
      hash.update(chunk);
      length += Buffer.byteLength(chunk, 'utf8');
    };

    const onEnd = (chunk, encoding) => {
      onData(chunk, encoding);

      // generate tag
      const l = length.toString(16);
      const h = hash.digest('hex');

      // weak or strong? use length and hash as ETag
      const tag = opts.weak ? `W/${l}-${h}` : `${l}-${h}`;
      res.setHeader('ETag', tag);
    };

    // override the default methods
    res.write = (...args) => {
      onData(...args);
      write.apply(res, [...args]);
    };

    res.end = (...args) => {
      onEnd(...args);
      end.apply(res, [...args]);
    };

    next();
  };
};

module.exports = taggart; 
```

Enter fullscreen mode Exit fullscreen mode

## 远景

我们所做的是劫持`res`对象的`res.write`和`res.end`方法，该对象是 [`http.ServerResponse`](https://nodejs.org/api/http.html#http_class_http_serverresponse) 的实例。`res`对象的`write`和`end`方法用于将数据写入发送给客户端的响应，它们继承自 [`Stream`](https://nodejs.org/api/stream.html) 类。

开始时，我们创建一个散列，在`onData`方法中，我们使用块更新散列并删除它们(**不存储块**，它们会变得非常大)。我们也在跟踪回应的规模。

对`res.end`的调用表明响应已经结束，现在我们可以在`onEnd`函数中完成散列并将其设置为头。但是有一个问题。在 HTTP 中，请求和响应是流式的。对于每个对`res.write`的调用，如果可能的话，部分响应会被发送到客户端，消息头在第一个块中发送。报头在第一个块中发送，这发生在[对`res.write`](https://nodejs.org/api/http.html#http_response_write_chunk_encoding_callback) 的第一次调用期间。

## 死路一条

如果您尝试运行上述代码，将会得到一个致命错误。如果您的响应足够小(小于 65535 字节左右的\_(ツ)_/)，那么它可以放在第一个块中。您在单个块中获得全部数据，更新散列，并在对`res.end`的调用中设置头。完全可行，但前提是您的响应小于 64Kb 左右。

但我想发送图像和视频的电子标签，我敢肯定不小于 64Kb。对于一个 2Mb 的图像，发回一个 http 代码真的会有帮助，对吗？由于响应的流性质，我们无法做到这一点。只有在调用`res.end`时才能生成散列，但那时头可能已经被发送了。

## 唯一的路是向上

所以，现在是妥协的时候了:

1.  我们可以只对小的响应用 ETags 来解决。
2.  我们可以事先为静态内容生成散列，可能在构建过程中或其他时候，将它保存在字典中，以后再检索它。麻烦。
3.  我们可以在每个请求上生成散列——首先准备响应，生成散列，然后用适当的头传输响应。干净实用。
4.  [`Trailers`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.39) 都可以用，我正在努力，看起来不太乐观，我可能会错。

## 结论

我们可以使用像 [`etag`](https://github.com/jshttp/etag/) 这样的模块，它有点像是和 [`send`](https://github.com/pillarjs/send/) 结合在一起，看起来像是打算和 [`server-static`](https://github.com/expressjs/serve-static) 一起使用。反正都是同一个人写的。`send`基于[文件统计](https://nodejs.org/api/fs.html#fs_class_fs_stats)生成“弱”ETags。

我开始意识到通过一个模块生成 ETags 是很难的。我必须连接到你的服务器，这是一个低级组件。无论如何，检查内容是否陈旧是相当容易的。

## 参考文献

*   [https://github . com/pillar js/send/pull/105 # issue comment-198766116](https://github.com/pillarjs/send/pull/105#issuecomment-198766116)
*   [https://www . w3 . org/Protocols/RFC 2616/RFC 2616-sec 13 . html # sec 13 . 3 . 3](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13.3.3)

> **注:**这是我的第一篇公开博客。感谢你从头到尾的阅读，希望你喜欢。我的母语不是英语，我正在那里提高我的技能。
> 我所谓的失败中间件是 GitHub 上的[，我在](https://github.com/ramlmn/taggart/) [`fix-tagging`分支](https://github.com/ramlmn/taggart/tree/fix-tagging)上工作，没有检查主分支，它是脏的。请留下建议，这对我帮助很大。谢了。