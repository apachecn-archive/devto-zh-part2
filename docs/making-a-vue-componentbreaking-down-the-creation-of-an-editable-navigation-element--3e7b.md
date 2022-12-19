# 制作 Vue 组件:一个可编辑的导航元素

> 原文：<https://dev.to/teekatwo/making-a-vue-componentbreaking-down-the-creation-of-an-editable-navigation-element--3e7b>

我有一个有趣的想法来创建一个导航，为了简单起见，我称之为可编辑导航。我希望这种导航易于使用。当你填写表格时，就会添加一个链接。您可以删除链接、编辑链接的内容以及更改链接的顺序。

为了开始这个项目，我把它分为 3 个阶段:添加链接，删除链接，移动链接。当我浏览它们的时候，我觉得这些阶段有点太模糊了。我忽略了使产品可用的重要细节。让加法看起来如此简单背后的细微差别，错误处理和警告。没有这些，我可能会创造一个非常糟糕的体验。因此，我添加了错误处理的另一个阶段，但是可以很容易地添加更多的阶段来包含更平滑的体验。

## 第一阶段:添加链接

[https://codepen.io/Vpugh/embed/bLKMaJ?height=600&default-tab=result&embed-version=2](https://codepen.io/Vpugh/embed/bLKMaJ?height=600&default-tab=result&embed-version=2)

第一阶段代码笔:我创建导航的第一阶段是开发一个从输入数据添加链接的功能。这是一个简单的事情，创建一个空数组来放置新的链接，当“添加到导航”按钮被按下时，它将输入的内容添加到数组中。这又会给导航添加一个新的链接。

这可能比我想的更简单。如果输入为空，没有任何安全措施来防止输入为空并触发函数，或者如果输入为空，则没有任何错误消息来告诉用户需要填写。我稍后会修复这个问题，第一个想法是让这个函数工作。

这段代码的难点在于存储数据的嵌套数组的问题。我最初无法将信息推入数组，它们就是不出现。当我把它写成一个嵌套数组[{ text: text，url: url }]时，它工作了。

```
addLink: function() {
  var text = this.newLink.trim()
  var url = this.newURL.trim()
  if (text) {
   this.links.push({ text: text, url: url })
   this.newLink = ""
   this.newURL = ""
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第二阶段:删除链接

[https://codepen.io/Vpugh/embed/oEyJQE?height=600&default-tab=result&embed-version=2](https://codepen.io/Vpugh/embed/oEyJQE?height=600&default-tab=result&embed-version=2)

[Phase 2 Codepen](https://codepen.io/Vpugh/pen/oEyJQE) :下一步是创建一个函数，它会考虑到你点击的链接的索引，然后从数组中删除该对象。
T3】

```
removeLink: function(index) {
  this.links.splice(index, 1)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第三阶段:错误处理

[https://codepen.io/Vpugh/embed/wyXVMx?height=600&default-tab=result&embed-version=2](https://codepen.io/Vpugh/embed/wyXVMx?height=600&default-tab=result&embed-version=2)

[Phase 3 Codepen](https://codepen.io/Vpugh/pen/wyXVMx) :这是从需要添加错误处理(警告和阻止数据添加到数组)开始的。然后，它演变成添加功能，用于按下 enter 来触发该功能。如果您在最后一次输入时按 enter 键，将会触发添加功能。我发现自己想要填写完最后一个输入，然后按下回车键，期望它能工作，但它没有。

```
addLink: function() {

      var text = this.newLink.trim()
      var url = this.newURL.trim()

      if ((text !== '') && (url !== '')) {
        this.isEmpty = false
        this.links.push({ text: text, url: url })
        this.newLink = ""
        this.newURL = ""

      } else {
        this.isEmpty = true
        this.error = 'Complete all boxes!'
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这个函数在概念上非常简单。该逻辑背后的原因是将文本输入内容和 url 输入内容变成一个变量，然后检查其中一个变量是否为空。如果是这样，那么函数将使变量 isEmpty 为 true，这将显示一个带有消息的错误框。不会发生阵列推送，也不会添加链接。

我确实在这个函数中犯了一些错误。对于原始逻辑语句，它不能正确地触发函数。它只检查变量文本是否存在以及变量 url 是否为空。这个问题是因为我想检查输入是否为空，而不是它们是否存在。如果 url 不为空且文本为空，将触发该函数。这不是我想要发生的。这将推动一个没有标题的链接，将是一个空白空间。

```
if ((text && url !== '') 
```

Enter fullscreen mode Exit fullscreen mode

我遇到的另一个问题是试图过于花哨，使用 isEmpty 切换，而不是显式声明 true 或 false。每当逻辑出现故障时，无论是否仍有错误，它都会不停地打开和关闭错误消息。在这个更新版本中，如果输入中有内容，则变量始终为 true，如果输入不为空，则变量为 false。

这三个阶段过得很快，它们花了我一天的大部分时间。我在第三阶段停滞不前，这是一个需要简化和不那么花哨的经典案例。

## 阶段 4 —编辑链接

[https://codepen.io/Vpugh/embed/VQBOax?height=600&default-tab=result&embed-version=2](https://codepen.io/Vpugh/embed/VQBOax?height=600&default-tab=result&embed-version=2)

第四阶段代码公开:这是我迄今为止最大的挑战！我最初关注的是进行更改的机制，并决定使用一个模型。

```
 <transition name="fade">
    <div class="editMenu" v-show="isEditing">
      <button class="close" @click="hideForm">X</button>
      <h3>Edit Link</h3>
      <label for="" >Link Title</label>
      <input v-model="editLink" @keyup.enter="editLinks" type="text">
      <label for="">Link URL</label>
      <input v-model="editURL" @keyup.enter="editLinks" type="text">
    </div>
  </transition> 
```

Enter fullscreen mode Exit fullscreen mode

我通过使用[过渡](https://vuejs.org/v2/guide/transitions.html)和 [v-show](https://vuejs.org/v2/guide/conditional.html#v-show) 创建了一个简单的。当按下按钮时，过渡创建动画中的平滑淡入，并且 v-show 激活进入视图的模态。

```
showForm: function(index) {
  this.isEditing = true
  this.editingIndex = index
  this.editURL = this.links[index].url
  this.editLink = this.links[index].text
} 
```

Enter fullscreen mode Exit fullscreen mode

编辑部分一直是个麻烦点。在罗尔·尼斯肯斯的帮助下，他为我解决了这个关键问题。针对被按下的链接进行编辑的方法是获取链接的索引，并将其保存到数据变量中。这将在 showForm 函数运行时完成，以便索引值(现在是 editingIndex 的变量)可用于下一个函数。

```
editLinks: function(){
      var text = this.editLink.trim()
      var url = this.editURL.trim()
      Vue.set(this.links, this.editingIndex, { text: text, url: url })
      this.isEditing = false
      this.editLink = ""
      this.editURL = ""
    } 
```

Enter fullscreen mode Exit fullscreen mode

这个函数获取输入中的信息，并将它们推回到它们的数组中。这就是 [Vue.set()](https://vuejs.org/v2/guide/reactivity.html) 派上用场的地方。

Vue.set(object，key，value)是这样工作的。**对象**是我想要改变的地方，链接数组。**键**是被点击的特定链接的索引。**值**是输入的内容。

## 阶段 5 -重新整理链接

这个最后阶段将集中在使链接在导航中改变位置。我不确定从哪里开始做这个工作，也不确定是使用外部扩展还是导入一些东西来帮助做这个。我将尝试先构建一些东西，然后从那里开始变得更复杂。