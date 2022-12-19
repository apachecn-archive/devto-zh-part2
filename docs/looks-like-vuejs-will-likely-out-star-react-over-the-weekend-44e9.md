# 看起来 Vue.js 可能会在周末超越明星！

> 原文：<https://dev.to/ycmjason/looks-like-vuejs-will-likely-out-star-react-over-the-weekend-44e9>

> 原帖:[https://www.ycmjason.com/blog/2018/06/14.html](https://www.ycmjason.com/blog/2018/06/14.html)

快到了！快到了！！！！

几周前，我发表了 [vue-vs-react](https://github.com/ycmjason/vue-vs-react) 。

由于星差越来越近(在我打这篇文章的时候是 268 星差)，我决定给我的项目增加一个新的特性。当明星们做出反应时，烟火会伴随着背景声音爆炸！

你可以简单地在一个标签页中打开[网站](https://vue-vs-react.ycmjason.com/)，当 Vue out-star 做出反应时，你应该会收到背景音乐的通知！

我是如此如此如此如此如此如此兴奋！

# 建设本项目时值得一提的事情

## 第一次使用`$refs`

在使用 Vue 时，很少有直接引用 DOM 的情况。借助 Vue 的力量，大多数事情都可以轻松处理。

在这个项目 tho 里，我有一个`<audio>`，当`vue.stars > react.stars`的时候就会触发播放。为了做到这一点，我必须获取对元素的引用并执行`elem.play()`。在 Vue 中获取对 DOM 的引用的方法是添加`ref="name"`。所以我只是简单的做了如下(见[这一行](https://github.com/ycmjason/vue-vs-react/blob/v0.2.0/src/components/Vs.vue#L3) ):

```
<audio src="..." ref="audio"></audio> 
```

Enter fullscreen mode Exit fullscreen mode

那么`this.$refs.audio`将指向那个元素！现在我可以在组件内部做`this.$refs.audio.play()`和`this.$refs.audio.pause()`！！！！(见[这条线](https://github.com/ycmjason/vue-vs-react/blob/v0.2.0/src/components/Vs.vue#L33))

这让我第一次意识到，当你想访问本地 API(T2)的时候，T0 是很有用的。

我目前做的方式很不规范。正确的方式可能类似于:

```
// Audio.vue
<template>
    <audio :src="src" ref="audio"></audio>
</template>

<script>
export default {
    props: ['playing'],
    watch: {
        playing(p) {
            if (p) this.$refs.audio.play();
            else this.$refs.audio.pause();
            // actually I will write: this.$refs.audio[p? 'play': 'pause']();
        },
    },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

有了这个包装音频组件，我们再也不需要在代码库中的任何地方使用`$refs`!是低级的东西，我们应该总是把它包装起来，以提升代码的抽象层次。

## Vue 可以非常快速地更新 DOM

如果你点击了这个网站，你可能会注意到转发的星星数量在增加。这个效果是通过这个 [TransitioningNumber](https://github.com/ycmjason/vue-vs-react/blob/v0.2.0/src/components/TransitioningNumber.vue) 组件实现的。

这个组件的工作方式是，每当`porps.number`改变时，它[计算动画`frames`](https://github.com/ycmjason/vue-vs-react/blob/v0.2.0/src/components/TransitioningNumber.vue#L32) 。`frames`只是一个从以前的`props.number`到现在的`props.number`的数字列表。因为我已经将`FPS`设置为 22，所以每帧对应的秒数将是`1 / 22`。为了制作动画，我们可以简单地将第一帧的`this.displayNumber`更改为最后一帧，每帧之间的超时时间为`1 / 22`秒。我很惊讶 Vue 能处理得这么好，结果看起来真的让我很满意。

我举一个例子来说明上面的实现。假设我们想在`2`秒内显示从`0`到`1000`的过渡。为了达到 22 FPS，我们知道我们需要`22 * 2 = 44`帧。所以我们将构建一个列表，`frames`，有 44 帧，范围从 0 到 1000。很明显，每一帧都会有`step = 1000 / 44 = 22.7272727 ~= 23`。然后我们构造列表`frames = [0, 23, 46, 68, ..., 1000]`。然后，我们将`this.displayNumber`设置为每帧，每帧之间的超时时间为`1 / 22`秒。

这种方法更像是一种实验，而且看起来非常幼稚。如果你能提出更好的方法，我将不胜感激。请在下面评论！

## Firestore 太棒了

在这个项目中，我使用了 [firebase](https://firebase.google.com/) ，如果你还不知道，它基本上是一个后端即服务，你不需要关心服务器的架构。它允许您关注应用程序逻辑。

Firestore 是 firebase 的一个实时数据库。它有一个强大的 API，包括基本的 CRUD(创建、读取、更新、删除)操作和**订阅数据库更新**等等！！！！再也不用自己设置 REST API 了，用 firestore 就行。你再也不需要设置非常烦人的网络插座，只需使用 firestore！“太神奇了！请为我做一切！”

请参考这两个文件( [1](https://github.com/ycmjason/vue-vs-react/blob/v0.2.0/src/services/repoSnapshots.js) 和 [2](https://github.com/ycmjason/vue-vs-react/blob/v0.2.0/src/App.vue) )。

```
// services/repoSnapshots.js (expanded)
import firebase from 'firebase';

export default {
    subscribe: (fn) => firebase.firestore()
      .collection('repo-snapshots')
      .orderBy("timestamp", "desc")
      .limit(20)
      .onSnapshot(snapshot => fn(snapshot.docs.map(d => d.data()))),
}

// App.vue (<script>)
import repoSnapshots from './services/repoSnapshots.js';

export default {
    ...
    data: () => ({
        snapshots: [],
    }),
    created() {
        repoSnapshots.subscribe(snapshots => this.snapshots = snapshots);
    },
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，代码描述得非常简单。虽然文档不是很完整，但是需要一些时间来理解我想要的方法。用起来还是很简单的。

## 包裹才是真正的牛逼

Vue-cli 3.0 绝对惊艳。但我认为[包裹](https://parceljs.org/)仍然是最 0 配置捆绑包的赢家。由于这个非常强大的包，这个项目很容易启动！我从来不需要担心添加`sass-loader` / `vue-loader`或任何加载程序。它只是，为你而做！神奇！

# 反正，

我希望你和我一样兴奋地看到 Vue 的反应，并喜欢读这篇文章。请随意问我任何问题，如果可以的话，我一定会回答。感谢阅读！