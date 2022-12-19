# Firebase Functions 上可以使用 Node.js 8 了哦！ ！ ！

> 原文：<https://dev.to/jgs/firebase-functions--nodejs-8--23lk>

# tldr

*   升级`firebase-tools`
*   在`functions/package.json`中添加`"engines": { "node": "8" }`
*   `firebase deploy`

# Weiwei

[在 https://cloud.Google.com/functions/docs/concepts/nodejs-8-runtime](https://cloud.google.com/functions/docs/concepts/nodejs-8-runtime)上(？ )在 Cloud Functions 上写着可以使用 Node.js 8(还有 Python )，我踊跃地试了一下。

那在 Firebase Functions 上不是也可以用吗...？ 我查了一下，完全没有消息。 也有报道说可以从网络控制台更改运行时间，但自己的项目中没有这样的项目…。

用`runtime`搜索 firebase-tools 的存储库后，感觉像是在对应。 追了一段时间代码后，最开始注意到就好了，但发行说明上已经好好写了。

[https://github . com/firebase/firebase-tools/releases/tag/v 4 . 0 . 0](https://github.com/firebase/firebase-tools/releases/tag/v4.0.0)

不要先认真读发行说明！

# Yu tan

暂时不看的时候，TypeScript 也可以官方对应，在用`firebase init`对话制作项目的时候，可以用 TS 制作文件夹，所以可以很容易地制作模型。 如果能用 TS 书写环境相当恶劣的 Node.js 8 驱动的东西的话，应该会进入很多选项吧...？ 出现了这样的矛盾。 虽然还很像 beta，但是想要枪电池...！