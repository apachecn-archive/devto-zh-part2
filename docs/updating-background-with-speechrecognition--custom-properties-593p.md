# 用语音识别和自定义属性更新背景

> 原文：<https://dev.to/karolinedealencar/updating-background-with-speechrecognition--custom-properties-593p>

警告:它只能在 Chrome 上运行。

过了很久，我决定从我的
[用 SpeechRecognition &自定义属性笔](https://codepen.io/KarolinedeAlencar/pen/Oxvjve)，
写一个快速教程，在这里我使用 SpeechRecognition API 和 CSS 自定义属性来更新主体的背景颜色。
制作这支笔帮助我了解了更多关于 SpeechRecognition API 以及如何通过 JavaScript 更新自定义属性的知识，我希望它也能帮助你！

现在我们开始吧！

## 基础 HTML

```
<main class="main">
        <h1 class="title">Speak!</h1>
        <p class="description">You need accept the microphone permission!</p>
        <p class="value">Actual value: <span class="color-value" data-js="varValue">palevioletred</span></p>
        <span class="loader"></span>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

## 基础 CSS

```
:root {
  --color: palevioletred;
  font-size: 16px;
}

@media (max-width: 500px) {

  :root {
    font-size: 12px;
  }

}

body {
  font-family: 'Roboto', sans-serif;
  background-color: var(--color);
}

.main {
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 0 .6rem;

  color: #232121;
  text-align: center;
  border: 2px solid #232121;
}

.title {
  margin: 0;
  font-size: 4rem;
}

.description {
  margin: 0;
  font-size: 1.4rem;
  font-weight: 300;
}

.value {
  font-size: 3rem;
  font-weight: 300;
}

.color-value {
  font-weight: bold;
}

.loader {
  font-size: 20px;
  font-style: italic;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们设置了 **- color** 变量，并将其用于身体的背景色。变量有一个初始值，我们也在 HTML 中设置了这个值。

这就是我们开始编写 JavaScript 所需的全部内容。开始吧！:)

## 教程

#### 设置 SpeechRecognition 对象

```
window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition; 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 webkitSpeechRecognition 是因为 Chrome 只支持带有前缀属性的 API。

#### 定义实例

```
const recognition = new SpeechRecognition(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置语言

```
recognition.lang = 'en-US'; 
```

Enter fullscreen mode Exit fullscreen mode

#### 开始识别

```
recognition.start(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 更新加载器文本

我们还可以在 API 开始监听输入音频时更新加载器文本，这样用户就知道 API 已经启动了。

```
recognition.addEventListener('start', () => loader.textContent = 'Detecting...'); 
```

Enter fullscreen mode Exit fullscreen mode

#### 【重启】API

现在我们需要监听结束事件并‘重启’API，这样用户可以再次更新颜色

```
recognition.addEventListener('end', recognition.start); 
```

Enter fullscreen mode Exit fullscreen mode

#### 从 API 获取结果

首先我们需要听结果事件

```
recognition.addEventListener('result', e => {
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后从 API 中获取结果

```
recognition.addEventListener('result', e => {
    const transcript = Array.from(e.results)
        .map(result => result[0].transcript);
}); 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们初始化了 API，并且得到了语音识别的结果。现在我们需要在 HTML 中更新背景和颜色值。

#### 更新变量值

现在我们有了结果，我们可以更新颜色变量

```
recognition.addEventListener('result', e => {
    document.body.style.setProperty('--color', transcript);
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后，用当前颜色更新 HTML

#### 更新 HTML

```
recognition.addEventListener('result', e => {
    span.textContent = transcript;
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 拆卸装载机

我们还可以通过将该值更新为' '
,来“移除”加载程序

```
recognition.addEventListener('result', e => {
    loader.textContent = '';
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 我们最终的 Javascript:

```
window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

const span = document.querySelector('[data-js="varValue"]');
const main = document.querySelector('.main');
const loader = document.querySelector('.loader');

const recognition = new SpeechRecognition();
recognition.lang = 'en-US';

recognition.addEventListener('result', e => {
    const transcript = Array.from(e.results)
        .map(result => result[0].transcript)

    document.body.style.setProperty('--color', transcript);
    span.textContent = transcript;
    loader.textContent = '';
});

recognition.addEventListener('start', () => loader.textContent = 'Detecting...');

recognition.addEventListener('end', recognition.start);

recognition.start(); 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已:)