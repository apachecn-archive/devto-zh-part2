# CSS 加载效果

> 原文：<https://dev.to/sandeepkamboj12/css-loading-effect-3jo3>

Css 为创造加载效果提供了很好的机会。不需要包含外部 gif 加载器。

HTML :

```
<div class="spinner">
  <div class="double-bounce1"></div>
  <div class="double-bounce2"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

CSS :

```
.spinner {
  width: 40px;
  height: 40px;

  position: relative;
  margin: 100px auto;
}

.double-bounce1, .double-bounce2 {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background-color: #333;
  opacity: 0.6;
  position: absolute;
  top: 0;
  left: 0;

  -webkit-animation: sk-bounce 2.0s infinite ease-in-out;
  animation: sk-bounce 2.0s infinite ease-in-out;
}

.double-bounce2 {
  -webkit-animation-delay: -1.0s;
  animation-delay: -1.0s;
}

@-webkit-keyframes sk-bounce {
  0%, 100% { -webkit-transform: scale(0.0) }
  50% { -webkit-transform: scale(1.0) }
}

@keyframes sk-bounce {
  0%, 100% { 
    transform: scale(0.0);
    -webkit-transform: scale(0.0);
  } 50% { 
    transform: scale(1.0);
    -webkit-transform: scale(1.0);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

很少其他类型的 CSS 加载器

-= the last fantasy =-荣誉出品本字幕仅供学习交流，严禁用于商业途径

[https://loading.io/css/](https://loading.io/css/)