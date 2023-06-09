# JavaScript30 - 01 上的新 Vue JavaScript 鼓套件

> 原文：<https://dev.to/davefollett/a-new-vue-on-javascript30---01-javascript-drum-kit-1k7f>

这篇文章是 JavaScript30 系列的 [A New Vue 的一部分，探索使用](https://dev.to/t/anewvueonjs30) [Vue](https://vuejs.org) 重新实现 [JavaScript30](https://JavaScript30.com) 项目。在阅读之前，最好先:

*   完成了 JavaScript30 的 01 - JavaScript 架子鼓项目
*   阅读[JavaScript 30 上的新 Vue 入门](https://dev.to/davefollett/a-new-vue-on-javascript30---getting-started-4o61)文章

## 关键 Vue 概念

本文讨论了以下 Vue 概念:

*   `v-for`指令
*   类绑定
*   事件绑定

## 01 - JavaScript 鼓套件

JavaScript30 项目的第一步是构建一个鼓工具包，将按键绑定到演奏鼓的声音。这个非常有趣，最后我创建了两个不同的 Vue 实现:

*   [index-view-v1 . html](https://github.com/davefollett/JavaScript30/blob/master/01%20-%20JavaScript%20Drum%20Kit/index-VUE-V1.html)
*   [index-view-v2 . html](https://github.com/davefollett/JavaScript30/blob/master/01%20-%20JavaScript%20Drum%20Kit/index-VUE-V2.html)

让我们看看我创建的第一个版本。

## 视图版本 1

对于第一种方法，我决定简单地将原始项目中的代码插入到它们对应的 Vue 位置:

*   HTML 部分放在根目录`<div>`中
*   这些功能进入了`methods`部分
*   页面加载时执行的 JavaScript 被放在了`mounted`函数中
*   不需要`data`、`computed`和`watch`部分，因此将其删除

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  JS Drum Kit
  <link rel="stylesheet" href="style.css">
  <!-- Use Vue from a CDN -->
  <script src="https://unpkg.com/vue"></script>
</head>
<body>

<!-- Vue Root DOM Element -->
<div id="app">
  <div class="keys">
    <div data-key="65" class="key">
      <kbd>A</kbd>
      <span class="sound">clap</span>
    </div>
    <div data-key="83" class="key">
      <kbd>S</kbd>
      <span class="sound">hihat</span>
    </div>
    <div data-key="68" class="key">
      <kbd>D</kbd>
      <span class="sound">kick</span>
    </div>
    <div data-key="70" class="key">
      <kbd>F</kbd>
      <span class="sound">openhat</span>
    </div>
    <div data-key="71" class="key">
      <kbd>G</kbd>
      <span class="sound">boom</span>
    </div>
    <div data-key="72" class="key">
      <kbd>H</kbd>
      <span class="sound">ride</span>
    </div>
    <div data-key="74" class="key">
      <kbd>J</kbd>
      <span class="sound">snare</span>
    </div>
    <div data-key="75" class="key">
      <kbd>K</kbd>
      <span class="sound">tom</span>
    </div>
    <div data-key="76" class="key">
      <kbd>L</kbd>
      <span class="sound">tink</span>
    </div>
  </div>

  <audio data-key="65" src="sounds/clap.wav"></audio>
  <audio data-key="83" src="sounds/hihat.wav"></audio>
  <audio data-key="68" src="sounds/kick.wav"></audio>
  <audio data-key="70" src="sounds/openhat.wav"></audio>
  <audio data-key="71" src="sounds/boom.wav"></audio>
  <audio data-key="72" src="sounds/ride.wav"></audio>
  <audio data-key="74" src="sounds/snare.wav"></audio>
  <audio data-key="75" src="sounds/tom.wav"></audio>
  <audio data-key="76" src="sounds/tink.wav"></audio>
</div>

<!-- Vue Instance Section -->
<script>
  var app = new Vue({
    el: '#app',
    methods: {
      removeTransition: function (e) {
        if (e.propertyName !== 'transform') { return }
        e.target.classList.remove('playing')
      },
      playSound: function (e) {
        const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`)
        const key = document.querySelector(`div[data-key="${e.keyCode}"]`)
        if (!audio) { return }

        key.classList.add('playing')
        audio.currentTime = 0
        audio.play()
      }
    },
    mounted: function () {
      const keys = Array.from(document.querySelectorAll('.key'))
      keys.forEach(key => key.addEventListener('transitionend', this.removeTransition))
      window.addEventListener('keydown', this.playSound)
    }
  })
</script>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

虽然这个版本很好用，也很容易制作，但我觉得它没有利用 Vue 的特性，所以我决定对它进行迭代。

## 视图版本 2

当查看 JavaScript30 项目的 HTML 部分时，您可以看到每个键的重复模式:keyCode、keyName 和 soundName。通过将这些部分提取到一个数据结构中，我们可以利用 Vue 的`v-for`指令来循环它并创建相同的 HTML。让我们首先看看数据结构，一个对象数组，其中每个对象代表一个键:

```
data: {
  keys: [
    {keyCode: 65, keyName: "A", soundName: "clap", audio: new Audio("sounds/clap.wav"), isPlaying: false},
    {keyCode: 83, keyName: "S", soundName: "hihat", audio: new Audio("sounds/hihat.wav"), isPlaying: false},
    {keyCode: 68, keyName: "D", soundName: "kick", audio: new Audio("sounds/kick.wav"), isPlaying: false},
    {keyCode: 70, keyName: "F", soundName: "openhat", audio: new Audio("sounds/openhat.wav"), isPlaying: false},
    {keyCode: 71, keyName: "G", soundName: "boom", audio: new Audio("sounds/boom.wav"), isPlaying: false},
    {keyCode: 72, keyName: "H", soundName: "ride", audio: new Audio("sounds/ride.wav"), isPlaying: false},
    {keyCode: 74, keyName: "J", soundName: "snare", audio: new Audio("sounds/snare.wav"), isPlaying: false},
    {keyCode: 75, keyName: "K", soundName: "tom", audio: new Audio("sounds/tom.wav"), isPlaying: false},
    {keyCode: 76, keyName: "L", soundName: "tink", audio: new Audio("sounds/tink.wav"), isPlaying: false},
  ]
}, 
```

Enter fullscreen mode Exit fullscreen mode

除了包含 keyCode、keyName 和 soundName，我还添加了一个用于播放音频的 [HTMLAudioElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLAudioElement) 和一个`isPlaying`标志。这允许我们删除`<audio>`元素，并简化添加和删除应用于关键 div 元素的`playing`类的逻辑。如果你是 Vue 的新手，下一部分可能看起来有点复杂，但我会尽我所能来分解它。

```
<div id="app">
  <div class="keys">
    <div
      v-for="key in keys"
      :key="key.keyCode"
      :class="[key.isPlaying ? 'playing' : '', 'key']"
      @transitionend="removeTransition($event, key)"
    >
      <kbd>{{ key.keyName }}</kbd>
      <span class="sound">{{ key.soundName }}</span>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，使用`v-for`指令大大缩短了 HTML 部分。现在我们能够循环`keys`数组，将其数据值放入相应的点中。Vue 建议在使用`v-for`时设置`:key`属性，尽管这对这段代码来说不是必需的，但我还是做了。关于`:key`属性的更多信息，你可以在[这里](https://vuejs.org/v2/guide/list.html#key)阅读。

在下一行，我将类绑定到`<div>`元素。这将总是应用`key`类，但仅当`key.isPlaying`为真时才应用`playing`类。

最后，在循环中，我们设置`transitionend`事件来调用`removeTransition()`方法，传递`$event`和`key`对象。这样设置`transitionend`事件允许我从挂载的函数中移除`querySelectorAll`调用和`addEventListener`循环。

现在剩下的就是对控制`key.isPlaying`标志的`removeTransition`和`playSound`方法做一些小的修改，并播放来自`keys`数组的音频。

## 把所有的东西放在一起

[https://codesandbox.io/embed/yvyqm83lnz?initialpath=01%20-%20JavaScript%20Drum%20Kit%2Findex-VUE-V2.html&module=%2F01%20-%20JavaScript%20Drum%20Kit%2Findex-VUE-V2.html](https://codesandbox.io/embed/yvyqm83lnz?initialpath=01%20-%20JavaScript%20Drum%20Kit%2Findex-VUE-V2.html&module=%2F01%20-%20JavaScript%20Drum%20Kit%2Findex-VUE-V2.html)

通过利用 Vue 的特性，我能够对代码做一些很好的改进。循环数据结构而不是重复的 HTML 使代码更易于维护和将来扩展。只需在`keys`数组中添加一个新条目，就可以添加新的键。如果 HTML 需要改变，可以在一个地方更新，而不是九个关键的`<div>`元素中的每一个。一些额外的改进包括:

*   移除所有直接的 JavaScript DOM 查询和操作
*   删除所有数据属性

我希望你喜欢这篇文章，如果有任何问题、评论或*更正*请随时给我发消息。本系列中的所有代码都可以在官方 [JavaScript30 GitHub 资源库](https://github.com/wesbos/JavaScript30)的 [my fork](https://github.com/davefollett/JavaScript30) 中找到，该资源库位于:

*   [https://github.com/davefollett/JavaScript30](https://github.com/davefollett/JavaScript30)

## 接下来

在[中，JavaScript30](https://dev.to/t/anewvueonjs30) 系列的下一个新 Vue 是:

[![davefollett image](img/b32752a825067f737b786f8ac82aa66e.png)](/davefollett) [## JavaScript30 - 02 JS 和 CSS 时钟上的新 Vue

### 戴夫·福莱特 8 月 17 日 183 分钟阅读

#anewvueonjs30 #vue #javascript30 #javascript](/davefollett/a-new-vue-on-javascript30---02-js-and-css-clock-45l4)

[JavaScript 30-02 上新的 Vue JS 和 CSS 时钟](https://dev.to/davefollett/a-new-vue-on-javascript30---02-js-and-css-clock-45l4)。