# 不带 jQuery 的淡入图像

> 原文：<https://dev.to/acro5piano/fade-in-image-without-jquery--3fkb>

不知道有多少人写过这种文章，但是没有 jQuery 就写了淡入图像或者文字。

```
.fadein {
  opacity: 0;
}
.fadein.is-active {
  opacity: 1;
  transition: all ease 0.65s;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Fade in Threshold
const screenOffset = window.innerHeight / 2 

const elements = document.getElementsByClassName('fadein')
for (let element of elements) {
  window.addEventListener('scroll', () => {
    if (window.scrollY + screenOffset > element.offsetTop) {
      element.classList.add('is-active')
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 <p style="height: 100vh">Prints out this usage information.</p>
  <img class="fadein" src="https://www.google.co.img/branding/googlelogo/1x/googlelogo_color_272x92dp.png"> 
```

Enter fullscreen mode Exit fullscreen mode

如果用 React 的话，应该把上面的代码写在`componentDidMount`里。

参考:

[http://brian.hatenablog.jp/entry/floating-fade-in](http://brian.hatenablog.jp/entry/floating-fade-in)