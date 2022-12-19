# 设置最小和最大的 CSS 值为“字体大小”,“填充”,“空白”,以及更多不仅仅是“高度”和“宽度”

> 原文：<https://dev.to/mckabue/setting-minimum-and-maximum-css-values-for-font-size-padding-margin-and-many-more-not-just-height-and-width-135p>

**见下面的要点**

```
Usage:
 ======

 /* Single property 
 html {
   @include fluid-type(font-size, 320px, 1366px, 14px, 18px);
 }

 /* Multiple properties with same values */
 h1 {
   @include fluid-type(padding-bottom padding-top, 20em, 70em, 2em, 4em);
 }

////////////////////////////////////////////////////////////////////////////

div {
    @include fluid - type(font - size, 600px, 1200px, 16px, 32px);
}

@media screen and(max - width: 600px) {
    div {
        font - size: 16px;
    }
}

@media screen and(min - width: 1200px) {
    div {
        font - size: 36px;
    }
} 
```