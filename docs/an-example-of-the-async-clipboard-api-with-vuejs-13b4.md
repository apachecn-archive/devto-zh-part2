# Vue.js 异步剪贴板 API 示例

> 原文：<https://dev.to/raymondcamden/an-example-of-the-async-clipboard-api-with-vuejs-13b4>

几天前，谷歌发布了 Chrome 66，该版本中启用的新功能之一是[异步剪贴板 API](https://www.w3.org/TR/clipboard-apis/#async-clipboard-api) 。正如您所猜测的，这提供了对用户剪贴板的访问(包括读和写)，并且非常容易使用。

你可以在这里阅读一篇很好的 API 介绍，[解除剪贴板访问](https://developers.google.com/web/updates/2018/03/clipboardapi)，但是不要像我一样，一看到代码就停止阅读。这个例子看起来很简单:

```
navigator.clipboard.writeText('Text to be copied')
  .then(() => {
    console.log('Text copied to clipboard');
  })
  .catch(err => {
    // This can happen if the user denies clipboard permissions:
    console.error('Could not copy text: ', err);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这是写在剪贴板上，以防不明显。当我尝试这段代码时，它失败了，错误非常模糊(“未定义”)。阅读以上文章，您会发现这实际上是有据可查的:

> 与许多新的 API 一样，navigator.clipboard 仅支持通过 HTTPS 提供的页面。为了防止滥用，只有当页面是活动选项卡时，才允许访问剪贴板。

过了一会儿...

> 由于 Chrome 只允许当一个页面是当前的活动标签时访问剪贴板，你会发现这里的一些例子如果直接粘贴到 DevTools 中就不太好了，因为 DevTools 本身就是活动标签。

我很遗憾错过了这一点，但这并不是我第一次看到代码就停止阅读，这样我就可以马上玩它了。

所以——想看看用 Vue.js 的例子吗？你当然知道！想象一个场景，我们为用户生成了一个代码。我们想让它更容易使用，所以当我们可以的时候，我们会提供一个按钮，将它复制到他们的剪贴板。首先，HTML:

```
<div id="app" v-cloak>
  Your cool code:
  <input v-model="code">
  <button v-if="supportsCB" @click="copy">Copy</button>
  <div v-if="message">{{message}}</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

记下该按钮。它正在检查一个属性，看它是否应该出现。现在让我们看看 JavaScript。

```
const app = new Vue({
  el:'#app',
  data() {
    return {
      code:'vueIsBetterThanPBJ',
      supportsCB:false,
      message:''
    }
  },
  created() {
    if(navigator.clipboard) {
      this.supportsCB = true;
    }
  },
  methods:{
    copy() {
      navigator.clipboard.writeText(this.code)
        .then(() => {
          console.log('Text is on the clipboard.');
          this.message = 'Code copied to clipboard.';
        })
      .catch(e => {
        console.error(e);
        this.message = 'Sorry, unable to copy to clipboard.'
      });    
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我首先使用`created`钩子来查看`navigator.clipboard`是否存在。如果是这样，我就通过将`supportsCB`属性设置为 true 来启用按钮。注意，我也可以通过检查权限 API 来使其更加安全。

接下来——我使用`writeText`调用来定义我的`copy`方法。完成后，无论成功还是失败，我都会编辑一条消息让用户知道。这可能有点夸张，但我想确认一下会更好。你可以在下面玩这个，但显然你会想用 Chrome 66。

[https://codepen.io/cfjedimaster/embed/JvjpYp/?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/JvjpYp/?height=600&default-tab=result&embed-version=2)