# 使用 Vue.js、Yandex 翻译 API 和响应式语音构建音频翻译器

> 原文：<https://dev.to/jordanirabor/build-an-audio-translator-with-vuejs-yandex-translate-api-and-responsive-voice--5b52>

## 简介

几周前，我决定在 Vue.js 中列出一些很酷的项目，为此，我开始在互联网上搜寻有价值的资源。在一天结束的时候，我已经收集了一些有用的 API 的重要知识。其中，我发现 Yandex Translate API 真的很吸引人；它能够接受几乎任何文本，并返回 90 多种语言的对等文本。

在玩了一会儿 API(使用 [Postman](https://www.getpostman.com/) )之后，我决定将我的 Vue.js 知识打包到这个 API 中。结果呢？我拼凑了一个 Vue.js translator web 应用程序。

在这个项目中，我通过引用[responsive voice js](https://responsivevoice.org/)[CDN](https://code.responsivevoice.org/responsivevoice.js)和触发一个简单的函数来集成音频功能。

这是已完成项目的视觉展示:

[![](../Images/fb9de315c4a7789ff054ead809aa1dfd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RFHhs7F---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.scotch.io/40410/vYzxg2WJTQyeqLFzViph_reek.gif)

让我们看看我是如何建造它的。

## 要求

因为我们将使用渐进式 Javascript 框架 [Vue.js](https://vuejs.org/) 构建这个应用程序，所以我们需要确保在继续之前安装一些东西:

*   [Node.js](https://nodejs.org/)
*   节点程序包管理器( [npm](https://www.npmjs.com/) )
*   [vista-CLI](https://github.com/vuejs/vue-cli)工具(npm 安装-g vista-CLI)

我们还需要:

*   使用 [Yandex 翻译 API](https://translate.yandex.com/developers/keys) 的 API 密钥

要创建 Yandex 账户，请点击[此处](https://passport.yandex.com/registration?origin=translate&retpath=https%3A%2F%2Ftranslate.yandex.com%2Fdevelopers):

[![](../Images/73769e59eaa577d5d5fc151392378052.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bw0PNB-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/40410/pxLlRBnwQhe0MjirfPMb_screenshot-passport.yandex.com-2018-01-07-09-26-19.png)

要创建一个免费的 API 密钥，请点击[这里](https://translate.yandex.com/developers/keys):

[![](../Images/ea6b5f455635758fdb82eb64e9a4619e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WI8E4IDH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/40410/zI9XUlrTBSDu28pfDj08_screenshot-translate.yandex.com-2018-01-06-22-55-24.png)

一旦安装了依赖项，并且您有了密钥，我们就可以开始了！

## 创建项目

我们将使用 Vue-CLI 工具创建这个项目，另一种方法是从 [CDN](https://unpkg.com/vue@2.5.13/dist/vue.js) 中引用 Vue.js，如果您愿意，也可以这样做。

我们将使用`webpack-simple`模板，因为开发起来不会太复杂。

> 您可能需要更改项目目录的所有权，并以 root 用户身份运行这些命令(使用`sudo` )

```
vue init webpack-simple translator 
```

Enter fullscreen mode Exit fullscreen mode

使用这个命令，您需要响应几行命令提示符查询:

[![](../Images/7ac8365f31986a23c8de2a0f7a235a8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pjFS_QUu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/40410/1Sj8i9KOQ5ua8FkWnuua_snapshot1.png)

让我们导航到项目目录并安装依赖项:

```
cd translator
npm install 
```

Enter fullscreen mode Exit fullscreen mode

### 创建翻译组件

我们将为这个项目创建一个组件，因为我们不想用不必要的组件来扰乱开发环境。该组件将包含两个并排放置的`textareas`；一个保存要翻译的文本，而另一个显示翻译的文本，两边都有一个`audio`功能。

让我们在新安装的项目的`src`目录中创建一个`components`目录和一个`translate`文件。

```
mkdir -p src/components
cd src/components
touch translate.vue 
```

Enter fullscreen mode Exit fullscreen mode

这个文件将包含`translate`组件的所有代码。

## 为翻译组件编写代码

在我们编写任何代码之前，让我们考虑一下根组件(App.vue)和子组件(translate.vue)之间的关系。让我们假设繁重的工作将由根组件完成，因此子组件将接收 props，注册一些变量和`emit`事件，直到根组件完成用户交互。

现在我们已经了解了这是如何工作的，让我们在`script`标签中写一些代码来接收道具和初始化变量。

```
 export default {

  name: 'translate',

  props: {

   AvailableLanguages : {
     type : Object
   },

   ResponseText : {
     type : String
   },

   CurrentLanguage : {
     type : String
   },

   TranslateLanguage : {
     type : String
   }
 },

  data () {
    return {
      Text: '',
      FromLanguage : "English",
      ToLanguage : "French"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们从上到下分析一下上面的代码，这样我们就能理解发生了什么:

1.  首先，我们导出 default，这将允许我们将这个组件导入到根组件中。
2.  我们初始化`name`变量，然后接收并验证一些属性，显然，这些属性来自根组件。下面是它们是什么和它们做什么:

> *   `AvailableLanguages` will save the translatable language object returned by Yandex API.
> *   `ResponseText` The translated text of the specified language returned by Yandex API will be saved.
> *   `CurrentLanguage` is the code of the current language that we are entering.
> *   `TranslateLanguage` is the code of the language used for translation.

1.  最后，我们将一个`Text`(用于输入的文本)变量初始化为一个空字符串。然后，我们将另外两个变量`FromLanguage`和`ToLanguage`分别初始化为“英语”和“法语”。这些变量将保存所选语言的全名(不是代码)。

太棒了。

### 书写标记

收到了`props`并初始化了上面代码中的一些变量，我们现在应该写`HTML`了。我们主要是将`textareas`绑定到它们匹配的数据变量，并在用户交互时触发事件。`HTML`将被写在`template`标签中，这样 Vue.js 就知道这是`MarkUp`并在运行时渲染它。

```
<div id="parent_translate">

<div id="input">

<div id="imageholder">

<select v-model="FromLanguage" @change="changeFromLanguage">

<option v-for="(lang, code) in AvailableLanguages" :value="code"> {{ lang }} </option>

</select>

<img @click="translateSpeak" src="../assets/speaker.png">

</div>

<textarea placeholder="Type in something here" v-model="Text"></textarea>

<button @click="submit" id="submit">SUBMIT</button>
</div>

<div id="output">

<div id="imageholder">

<select v-model="ToLanguage" @change="changeToLanguage">

  <option v-for="(lang, code) in AvailableLanguages" :value="code"> {{ lang }} </option>

</select>

<img @click="responseSpeak" src="../assets/speaker.png">

</div>

<textarea placeholder="Your output goes here" disabled v-model="ResponseText"> </textarea>

</div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细看看绑定到该标记的功能:

第一个`select`标签使用`v-model`指令绑定到`FromLanguage`数据变量，每当它的值改变时，它触发一个事件处理程序“changeFromLanguage”。

嵌套在第一个`select`标签中的`option`标签接受可用语言的对象(它将作为根组件的一个属性)并使用`v-for`指令遍历它。它的`:value`指令被绑定到`code`变量，这样在循环过程中创建的每个选项标签都有自己唯一的`code`。

让我们看看更多的标记:

```
<select v-model="FromLanguage" @change="changeFromLanguage">

<option v-for="(lang, code) in AvailableLanguages" :value="code"> {{ lang }} </option>

</select> 
```

Enter fullscreen mode Exit fullscreen mode

前面讨论的`select`标签下面的`img`标签保存了音频功能的图像表示，并将点击事件定向到“translateSpeak”事件处理程序。

```
<img @click="translateSpeak" src="../assets/speaker.png"> 
```

Enter fullscreen mode Exit fullscreen mode

第一个`textarea`的功能非常简单，它将`Text`变量与自身绑定，这样它只有在没有文本写入时才显示一个占位符。

```
<textarea placeholder="Type in something here" v-model="Text"></textarea>

<button @click="submit" id="submit">SUBMIT</button> 
```

Enter fullscreen mode Exit fullscreen mode

标签执行一个单一的功能；每当它被点击时，就会触发一个`submit`方法。

在`MarkUp`中的这一点之后，剩余的代码只是已经显示的内容的重复，没有新的逻辑或功能。

让我们在`methods`对象中编写事件处理程序。

### 编写事件处理程序

让我们从编写选择框的处理程序开始这一部分，只要选择的选项是`changed`，它们就会被触发。

```
 changeFromLanguage:function( ev ) {
    let LanguageCode = ev.target.value;
    this.$emit('updateFromLanguage', LanguageCode)
  },

  changeToLanguage:function( ev ) {
    let LanguageCode = ev.target.value;
    this.$emit('updateToLanguage', LanguageCode)
  } 
```

Enter fullscreen mode Exit fullscreen mode

这两个处理程序的作用是相当明显的。首先，一个新变量保存所选选项的语言`code`，然后这个更新被`emitted`到根组件。还指定了根组件上处理这些发出的事件的处理程序。

```
 submit:function( ev ){
    let Word = this.Text
    if ( Word ) {
    this.$emit('translate', Word)
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

每当单击`Submit`按钮时，上面的 submit 方法就会被调用，它的简单工作是在将键入的文本传递给父组件的同时`emit`一个事件。

在我们研究音频函数的事件处理程序之前，我们必须引入`ResponsiveVoiceJS`库，因为我们依赖于它来实现音频函数，我们可以通过在`index.html`文件的结束`</body>`标签之前插入一个`script`标签来实现。

```
<!DOCTYPE html>

<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="https://fonts.googleapis.com/css?family=Indie+Flower" rel="stylesheet">
    A simple translator application using the Yandex API
    <style>

    body{
      overflow-x: hidden;
      overflow-y: hidden; 
      background: linear-gradient( to right, DodgerBlue, lightblue);
    }

    </style>
  </head>
  <body>
    <div id="app"></div>
    <script src="/dist/build.js"></script>
    <script src='https://code.responsivevoice.org/responsivevoice.js'></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

为了让应用程序说话，我们需要一个来自`ResponsiveVoiceJS`库中的函数:

```
responsiveVoice.speak() 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在我们的音频处理器中嵌入这个函数，比如:

```
 translateSpeak:function(){
    responsiveVoice.speak(this.Text);
  },

  responseSpeak:function(){
    responsiveVoice.speak(this.ResponseText);
  } 
```

Enter fullscreen mode Exit fullscreen mode

`speak()`函数接收两个数据变量之一的文本并播放它。

### 包括款式

最后，让我们包含一些作用域`Styles`来帮助事情保持漂亮:

```
<style scoped>
#parent_translate {
  width: 100%;
  height: 100%;
  display: flex;
  flex-wrap: nowrap;
  flex-direction: row;
}
select {
  padding: 0.2em 0em;
  margin: 0;
  font-family: 'Indie Flower', cursive;
}
#submit {
background: #4ed34c;
padding: 0.7em;
border: none;
border-radius: 1px;
cursor: pointer;
color: black;
}
#input, #output {
  background-color: #f1f1f1;
  width: inherit;
  height: inherit;
  text-align: center;
  font-size: 30px;
  border-radius: 60px;
}
#input {
  margin: 0px 2px 0px 0px;
}
#output {
    margin: 0px 0px 0px 2px;  
}
textarea {
  width: inherit;
  height: 60%;
  margin: 0px;
  padding: 45px 20px;
  box-sizing: border-box;
  outline: none;
  border: none;
  text-align: center;
  font-size: 25px;
  font-family: 'Indie Flower', cursive;
  font-weight: bold;
}
#input > div, #output > div {
  width: inherit;
  height: 20%;
  margin: 0px;
  padding: 0px;
}
#input > div > img, #output > div > img {
 padding: 10px 0px 0px 0px;
 width: 12%;
 height: 60%;
 cursor: pointer;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这就是`translate`组件的全部内容，事情将会变得更加实用和有趣。

## 建筑根构件

在我们开始为这个组件编写代码之前，我希望我们快速探索一下 [Yandex 翻译 API](https://translate.yandex.com/developers) 并检索一个[自由密钥](https://translate.yandex.com/developers/keys)，如果你还没有这样做的话，因为这对这个项目的后续部分至关重要。

在 Yandex 上创建账户之后:

[![](../Images/73769e59eaa577d5d5fc151392378052.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bw0PNB-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/40410/pxLlRBnwQhe0MjirfPMb_screenshot-passport.yandex.com-2018-01-07-09-26-19.png)

您将能够创建一个[免费密钥](https://translate.yandex.com/developers/keys):

[![](../Images/ea6b5f455635758fdb82eb64e9a4619e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WI8E4IDH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/40410/zI9XUlrTBSDu28pfDj08_screenshot-translate.yandex.com-2018-01-06-22-55-24.png)

### 书写标记

根组件不需要这么多的`MarkUp`，所以我们将只编写几行代码，在其中我们将把翻译组件作为一个定制标记插入:

```
<div id="app">

<h2>VUE AUDIO TRANSLATOR </h2>

<div id="translate">

<translate v-bind:AvailableLanguages="AvailableLanguages" v-bind:TranslateText="TranslateText" v-bind:ResponseText="ResponseText"  @updateFromLanguage="updateFromLanguage($event)" @updateToLanguage="updateToLanguage($event)" @translate="handleTranslation($event)">
</translate>

</div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

在`<translate>`定制标签上有两个值得注意的活动，让我们来看看:

1.  `AvailableLanguages`、`TranslateText`、`ResponseText`作为道具流传下来。
2.  根组件从翻译组件接收`@updateFromLanguage`、`@updateToLanguage`和`@translate`作为发出的事件。

### 包括款式

让我们包含一些样式来帮助 UI 看起来更好:

```
<style scoped>
#translate {
  height: 45vh;
  max-width: 90%;
  margin: 2.5vh auto;
}
h2 {
  text-align: center;
  font-size: 1.6rem;
  color: black;
  font-weight: bold;
  font-family: 'Indie Flower', cursive;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们可以用这个命令运行应用程序:

```
npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

[![](../Images/71c171b81ae1a39d8e0528ebd1bd0604.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aI_eifQt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.scotch.io/40410/GrrD2zoRjey9qMhW8iJg_Peek%25202018-01-10%252015-18.gif)

输出显示两个组件的 UI 都已经完成，但是当单击下拉菜单时，它没有填充语言。这是因为我们还没有编写驱动这个功能的脚本。

让我们现在做那件事。

### 编写 Javascript

让我们在这个组件中做三件事:

1.  导入翻译组件。
2.  注册组件
3.  初始化变量

```
import translate from './components/translate.vue'

export default {
  name: 'app',

  components: {
    translate : translate
  },

  data () {
    return {
      TranslateText : '',
      ResponseText : '',
      CurrentLanguage : 'en',
      TranslateLanguage : 'fr',
      AvailableLanguages : {}
    }
  }

  } 
```

Enter fullscreen mode Exit fullscreen mode

### 挂钩到已创建的()生命周期方法

Vue.js 允许我们挂钩不同的[组件生命周期方法](https://vuejs.org/v2/guide/instance.html#Instance-Lifecycle-Hooks)，做不同的事情来增强应用程序的灵活性。对于这个应用程序，我们希望在创建应用程序时向 Yandex Translate API 发送一个`GET`请求。由于 Yandex Translate API 允许我们为受支持语言的 JSON 有效负载及其代码创建一个`HTTP request`,因此每当创建应用程序时，我们都会发送一个请求。

我们需要一个`HTTP client`来发送这个请求，所以让我们得到一个，虽然有许多有竞争力的选择(vue-resource，Fetch API 等)，我们将使用基于承诺的`HTTP client` [axios](https://www.npmjs.com/package/axios) 。

让我们引入`axios`和一个用于将`axios`集成到`Vue.js` :
的小包装器

```
npm install --save axios vue-axios 
```

Enter fullscreen mode Exit fullscreen mode

要实际使用`axios`和`vue-axios`，我们需要将它们导入到`src/main.js`文件中使用:

```
import Vue from 'vue'
import App from './App.vue'

import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios)

new Vue({
  el: '#app',
  render: h => h(App)
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经设置了`axios`，我们可以在每次创建应用程序时请求支持语言的对象及其代码:

```
 created(){

  var self = this;

 this.axios.get('https://translate.yandex.net/api/v1.5/tr.json/getLangs?ui=en&key=YOUR_KEY_GOES_HERE')
  .then(function (response) {
    self.AvailableLanguages = response.data.langs;
  })
  .catch(function (error) {
    console.log(error);
  })

  } 
```

Enter fullscreen mode Exit fullscreen mode

在查询了 Yandex Translate API 之后，`response`作为一个承诺被异步传回，我们深入研究了一下`response`并更新了`AvailableLanguages`对象的值。

### 编写翻译和验证响应方法

这是整篇文章的中心主题，因为这是实际翻译文本和验证请求的地方。我们将在这里做三件简单的事情:

1.  用我们的惟一键和要翻译的文本向 Yandex Translate API 发出请求。
2.  检查响应以确认文本是否被正确翻译或者是否返回了错误。
3.  如果响应代码为`200`则更新`ResponseText`数据变量的值，否则将错误字符串记录到控制台。

```
translate:function(){

  var self = this;

 this.axios.get('https://translate.yandex.net/api/v1.5/tr.json/translate?key=YOUR_KEY_GOES_HERE&text='+self.TranslateText+'&lang='+self.CurrentLanguage+'-'+self.TranslateLanguage+'&format=plain')
  .then(function (response) {

  self.validateResponse(response);
  })
  .catch(function (error) {
    console.log(error);
  })

  },

  validateResponse:function(response){

        if (response.status === 422) {

            console.log('Could Not Translate Text Please Try Again Later');

        } else if (response.status === 413) {

            console.log('Please Enter A Shorter Text to translate');

        } else if (response.status === 200) { 

           this.ResponseText = response.data.text[0]

        } else {

          console.log('Cannot translate at this moment please try again later');
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这几乎是这些方法的全部功能，让我们看看根组件如何处理从`translate`组件发出的事件。

### 编写事件处理程序

从前面的小节中，我们知道根组件必须处理翻译组件发出的三个事件:

> *   `@updateFromLanguage``@updateToLanguage`T2】

我们来分别写处理这些的方法:

```
 updateFromLanguage:function( value ){
    this.CurrentLanguage = value;
    this.translate(); 
```

Enter fullscreen mode Exit fullscreen mode

这个处理程序从翻译组件接收一个值，并用它更新变量`CurrentLanguage`的值。然后它调用`translate()`方法来完成它的工作。

每当选择一种新语言(要翻译的语言)时，根组件就会触发这个方法。

它调用 translate 方法的原因是，当用户在语言之间切换时，可以获得翻译的即时更新，而不必每次都点击提交按钮。

```
 updateToLanguage:function( value ){
    this.TranslateLanguage = value;
    this.translate();
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个方法和上面的方法做的几乎一样，唯一的区别是它选择翻译的语言，而不是翻译的语言。

最后，看看处理翻译组件的提交按钮的点击事件的方法:

```
 handleTranslation:function( value ){
    this.TranslateText = value;
    this.translate();
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个处理程序简单地用从 translate 组件接收的参数值更新`TranslateText`变量，然后通过调用`translate()`方法翻译文本。

### 运行应用程序

我们可以用这个命令运行应用程序:

```
 npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

这将在浏览器中打开一个新窗口，位于开发地址`localhost:8080`:

[![](../Images/fb9de315c4a7789ff054ead809aa1dfd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RFHhs7F---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.scotch.io/40410/vYzxg2WJTQyeqLFzViph_reek.gif)

仅此而已！我们刚刚建立了一个功能音频翻译应用程序，并准备测试。

### 构建应用程序

我们可以选择使用以下命令进行生产构建:

```
 npm run build 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在本教程中，我们看到了如何在 Vue.js 中构建一个简单的音频翻译应用程序。我们还使用了一个强大的 HTTP 客户端，`axios`来查询 Yandex Translate API 的数据。

我们可以得出结论，Vue.js 是一个非常令人兴奋的渐进式框架，可以构建许多令人敬畏的项目。

这个项目的代码可以在 [Github](https://github.com/Jordanirabor/Audio-Translator) 上找到。